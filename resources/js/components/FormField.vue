<template>
    <field-wrapper>
        <div class="w-1/5 px-8 py-6">
            <slot>
                <form-label :for="field.name">
                    {{ field.name }}
                </form-label>
            </slot>
        </div>

        <div class="w-4/5 px-8 py-6">
            <a
                class="inline-block font-bold cursor-pointer mr-2 animate-text-color select-none"
                :class="{ 'text-60': localeKey !== currentLocale, 'text-primary border-b-2': localeKey === currentLocale }"
                :key="`a-${localeKey}`"
                v-for="(locale, localeKey) in field.locales"
                @click="changeTab(localeKey)"
            >
                {{ locale }}
            </a>

            <vue-ckeditor
                ref="field"
                :id="field.name"
                v-model="value[currentLocale]"
                class="mt-4"
                :config="config"
                @keydown.tab="handleTab"
            />

            <p v-if="hasError" class="my-2 text-danger">
                {{ firstError }}
            </p>
        </div>
    </field-wrapper>
</template>


<script>
    import {FormField, HandlesValidationErrors} from 'laravel-nova'
    import { EventBus } from '../event-bus';
    import VueCkeditor from 'vue-ckeditor2';

    export default {
        components: {VueCkeditor},

        mixins: [FormField, HandlesValidationErrors],

        props: ['resourceName', 'resourceId', 'field'],

        data() {
            return {
                config: this.field.options,
                locales: Object.keys(this.field.locales),
                currentLocale: null,
            }
        },

        mounted() {
            this.currentLocale = this.locales[0] || null;

            EventBus.$on('localeChanged', locale => {
                if(this.currentLocale !== locale) {
                    this.changeTab(locale, true);
                }
            });
        },

        methods: {
            /*
             * Set the initial, internal value for the field.
             */
            setInitialValue() {
                this.value = this.field.value || ''
            },

            /**
             * Fill the given FormData object with the field's internal value.
             */
            fill(formData) {
                Object.keys(this.value).forEach(locale => {
                    formData.append(this.field.attribute + '[' + locale + ']', this.value[locale] || '')
                })
            },

            /**
             * Update the field's internal value.
             */
            handleChange(value) {
                this.value[this.currentLocale] = value
            },

            changeTab(locale, dontEmit) {
                if(this.currentLocale !== locale){
                    if(!dontEmit){
                        EventBus.$emit('localeChanged', locale);
                    }

                    this.currentLocale = locale;

                    this.$nextTick(() => {
                        this.$refs.field.update()
                    })
                }
            },

            handleTab(e) {
                const currentIndex = this.locales.indexOf(this.currentLocale)
                if (!e.shiftKey) {
                    if (currentIndex < this.locales.length - 1) {
                        e.preventDefault();
                        this.changeTab(this.locales[currentIndex + 1]);
                    }
                } else {
                    if (currentIndex > 0) {
                        e.preventDefault();
                        this.changeTab(this.locales[currentIndex - 1]);
                    }
                }
            }
        }
    }
</script>

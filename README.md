# Laravel Nova CKEditor 4 Field (Translatable)

This package is a fork from [waynestate/nova-ckeditor4-field](https://github.com/waynestate/nova-ckeditor4-field) extended with translation functionalities for [dimsav/laravel-translatable](https://github.com/dimsav/laravel-translatable). The translation functionalities are inspired from [yeswedev/nova-translatable](https://framagit.org/yeswedev/ywd_nova-translatable).

## Installation

You can install the package into a Laravel application that uses [Nova](https://nova.laravel.com) via composer:

```bash
composer require Charl13/nova-ckeditor4-field
```

## Usage

For documentation refer to the original package [waynestate/nova-ckeditor4-field](https://github.com/waynestate/nova-ckeditor4-field).

Additionally this field can be translated:

```php
<?php

namespace App\Nova;

use Waynestate\Nova\CKEditor;

class Article extends Resource
{
    // ...

    public function fields(Request $request)
    {
        return [
            // ...

            CKEditor::make('Body', 'body')->locales([
                'en' => 'English',
                'de' => 'German',
            ]);

            // ...
        ];
    }
}
```

# Standalone Installation

This guide will instruct you on how to install Socialstream as a standalone application. If you require additional functionality such as those provided by Laravel Jetstream, Laravel Breeze, or Filament, please follow the [standard installation guide](../getting-started/installation.md).

{% hint style="warning" %}
This guide assumes you are installing Socialstream into a fresh, blank blade-based Laravel application. If you are installing Socialstream into an existing Laravel app, or one built with Vue, React or another frontend technology, this guide will not work and will break your application.
{% endhint %}

***

## Installation

Please follow the [official documentation](https://laravel.com/docs/10.x) for getting started with a brand new Laravel application.

Once you have created your app, `cd` into your projects directory and install Socialstream via `composer`:

```sh
composer require joelbutcher/socialstream
```

You will then want to publish the configuration file and migrations required to get Socialstream working:

```
// Config
php artisan vendor:publish --tag=socialstream-config

// Migrations
php artisan vendor:publish --tag=socialstream-migrations
```

You will then want to run your migrations:

```
php artisan migrate
```

Next, you will want to follow the [official guide](https://tailwindcss.com/docs/guides/laravel) for installing Tailwind CSS into your Laravel application. Once you have done this, you will need to update the `content` option of your applications `tailwind.config.js` file to include Socialstream's `resources` directory and files:

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    './vendor/joelbutcher/socialstream/resources/**/*.blade.php',
    ...
  ],
  ...
}
```

You may then add the Socialstream component into your application:

```php
<x-socialstream::socialstream />
```

Finally, you will want to install and build your frontend:

```sh
npm install && npm run build
```

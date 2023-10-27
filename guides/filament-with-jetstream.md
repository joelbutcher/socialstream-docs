# Filament with Jetstream

In some use cases, the need may arise for you to have multiple stacks installed within your application. For example, you may have a user-facing authenticated area to your app, as well as an admin panel built with Filament Admin Panel. This guide will instruct you on how to get your application set up for this scenario.

***

{% hint style="info" %}
This guide will assume you want to install the user-facing portion of your site via Laravel Jetstream and use Filament Admin Panel for staff. It also assumes you are using the same User model for both.

If you are using Laravel Breeze, please refer to [this guide](broken-reference).
{% endhint %}

## Installation

Firstly, you will want to install all the your dependencies via `composer`:

```sh
composer require laravel/jetstream filament/filament joelbutcher/socialstream
```

Once you have installed these dependencies, you may will want to run the install commands for both Jetstream and Filament **before** installing Socialstream:

```sh
php artisan jetstream:install <stack> <options>

php artisan filament:install --panels
```

{% hint style="warning" %}
Be sure to make a note of any optional features (such as teams support, dark mode, or email verification) as you will need this later on
{% endhint %}

Once you have done this, you will then need to install the Socialstream stack for Filament:

```sh
php artisan socialstream:install filament
```

This will ensure you have the base installation required for Socialstream to work with your application. It will also publish the required migrations, models and config files into your application for filament.

Next, you will need to install Socialstream for your desired Jetstream stack, ensuring to pass any options you may have opted-in for when installing Jetstream:

```sh
php artisan socialstream:install jetstream <stack> <options>
```

That's it! You can now enjoy using Socialstream for you application's users and for your apps admin panel.

***

## Notes

Because we installed Socialstream for Jetstream as the last step, the `User` model that was published by Filament will have been overwritten. To fix this, you may wish to re-add this into your User model:

```php
<?php

use Filament\Models\Contracts\FilamentUser;
use Filament\Panel;

class User extends Authenticatable implements FilamentUser
{
    public function canAccessPanel(Panel $panel): bool
    {
        return true;
    }
}
```

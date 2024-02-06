# Laravel Passport

If you wish to use this package alongside Laravel Passport, you may encounter the following error message when attempting to authorise with Passports OAuth server:

```log
Driver [authorize] not supported
```

This is because Socialstream registers routes using the `oauth/{provider}` structure. This conflicts with Laravel Passports `oauth/authorize` route. To solve this, you may set a route `prefix` in your applications `socialstream.php` config file:

```
'prefix' => 'auth',
```

Alternatively, if you wish to keep the prefix Socialstream uses, you may alter add prefix to Passport's routes by editing the Passport config file. If you haven't already, publish Passport's config file:

```sh
php artisan vendor:publish --tag=passport-config
```

 Add the following to `config/passport.php`:

```php
    /*
    |--------------------------------------------------------------------------
    | Path prefix
    |--------------------------------------------------------------------------
    |
    | Default is 'oauth'.
    |
    */

    'path' => 'passport-oauth',
```

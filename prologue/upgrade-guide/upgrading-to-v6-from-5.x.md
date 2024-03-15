# Upgrading to v6 from 5.x

{% hint style="info" %}
A Laravel 11 demo repo can be found [here](https://github.com/joelbutcher/socialstream-demo). This repo was originally built with Laravel 10 and Jetstream v4 with Vue for Inertia for the frontend. To view the complete upgrade to Laravel 11, check out this [merge commit](https://github.com/joelbutcher/socialstream-demo/commit/903934d233038f2034f90d4bca78f4c433349463).
{% endhint %}

## Updating Dependencies

### **PHP 8.2.0 Required**

Socialstream now requires PHP 8.2.0 or higher

### Laravel 11 Required

Socialstream new requires Laravel 11 to be installed, to do update the `laravel/framework` composer dependency to `^11.0` in your application's `composer.json` file.

{% hint style="warning" %}
Please follow the [official upgrade guide](https://laravel.com/docs/11.x/upgrade#upgrade-11.0) on what dependencies also need updating for this
{% endhint %}

### Jetstream 5 Required

If you use Socialstream with Laravel Jetstream, please ensure you have followed the [official upgrade guide](https://github.com/laravel/jetstream/blob/5.x/UPGRADE.md) for upgrading you Jetstream version to v5

### Filament

If you use Socialstream along with Filament Admin Panels, you will want to make sure you've upgraded `filament/filament` to `v3.2.39`

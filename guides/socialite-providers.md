---
description: >-
  The aim of this guide is to walk you through setting up a fresh Laravel
  project with Jetstream, Socialstream and Socialite Providers.
---

# Socialite Providers

{% hint style="info" %}
If you're unfamiliar with Laravel Socialite, Jetstream or Socialite Providers, we encourage you to read through their documentation before continuing with this guide.

* [Laravel Socialite](http://laravel.com/docs/10.x/socialite)
* [Laravel Jetstream](https://jetstream.laravel.com/introduction.html)
* [Socialite Providers](https://socialiteproviders.com/usage/)
{% endhint %}

***

Before we begin, this guide assumes that you have already setup a fresh Laravel project with Socialstream. If this is not the case, check out the [installation.md](../getting-started/installation.md "mention") instructions for Socialstream.

## Installation

To install a socialite provider, you may do so via composer. For example, to add the Apple provider, you may execute the following command in your terminal:

```sh
composer require socialiteproviders/apple
```

## Setup

### Icons

First, we need to create an SVG icon or custom button component for the provider within your desired stack. You will then need to update the published components for your stack to cater for this provider:

**Livewire**

Icon location: `resources/views/components/socialstream-icons/`

* `resources/views/components/socialstream.blade.php`
* `resources/views/components/connected-account.blade.php`

**Inertia**

Icon location: `resources/js/Components/SocialstreamIcons/`

* `resources/js/Components/Socialstream.vue`
* `resources/js/Components/ConnectedAccount.vue`

## Config

### Socialstream Providers

Inside your applications `socialstream.php` config file, you will want to add the string representation of the SocialiteProvider you are adding support for. For example, if you are adding Sign in With Apple support, you would add the string `'apple'` to the `providers` array in `config/socialstream.php`

```php
'providers' => [
    \JoelButcher\Socialstream\Providers::github(),
    \JoelButcher\Socialstream\Providers::google(),
    [
        'id' => 'apple',
        'name' => 'Apple',
        'label' => 'Sign in with Apple',
    ],
],
```

### Service Provider

#### Laravel 11+

In `bootstrap/providers.php`.

```php
return [
    // a whole bunch of providers
    // remove 'Laravel\Socialite\SocialiteServiceProvider',
    \SocialiteProviders\Manager\ServiceProvider::class, // add
];
```

#### In Laravel 10 or Below

In `config\app.php`.

```php
'providers' => [
    // a whole bunch of providers
    // remove 'Laravel\Socialite\SocialiteServiceProvider',
    \SocialiteProviders\Manager\ServiceProvider::class, // add
];
```

### Add provider event listener

#### Laravel 11+

In Laravel 11, the default `EventServiceProvider` provider was removed. Instead, add the listener using the `listen` method on the `Event` facade, in your `AppServiceProvider` `boot` method.

* Note: You do not need to add anything for the built-in socialite providers unless you override them with your own providers.

```php
Event::listen(function (\SocialiteProviders\Manager\SocialiteWasCalled $event) {
    $event->extendSocialite('microsoft', \SocialiteProviders\Microsoft\Provider::class);
});
```

<details>

<summary>Laravel 10 or below</summary>

Configure the package's listener to listen for `SocialiteWasCalled` events.

Add the event to your `listen[]` array in `app/Providers/EventServiceProvider`. See the [Base Installation Guide](https://socialiteproviders.com/usage/) for detailed instructions.

```php
protected $listen = [
    \SocialiteProviders\Manager\SocialiteWasCalled::class => [
        // ... other providers
        \SocialiteProviders\Microsoft\MicrosoftExtendSocialite::class.'@handle',
    ],
];
```

\


</details>

### Database Changes – `token`

{% hint style="danger" %}
Some providers will not return a token in the callback response. As such, you will need to modify the `connected_accounts` table migration to make the `token` field nullable:

```php
$table->string('token', 1000)->nullable();
```
{% endhint %}




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

Inside your applications `socialstream.php` config file, you will want to add the string representation of the SocialiteProvider you are adding support for. For example, if you are adding Sign in With Apple support, you would add the string `'apple'` to the `providers` array:

```
// ./config/socialstream.php

'providers' => [
    \JoelButcher\Socialstream\Providers::github(),
    \JoelButcher\Socialstream\Providers::google(),
    'apple',
],
```

### Service Providers

When using Socialstream alongside Socialite Providers, you will need **both** service providers adding your the `providers` array in you application's `app.php` config file:

```php
'providers' => [
    // ...
    \App\Providers\SocialstreamServiceProvider::class, // keep
    // ...

    // ...    
    // remove 'Laravel\Socialite\SocialiteServiceProvider::class',
    \SocialiteProviders\Manager\ServiceProvider::class, // add
    // ...
];
```

### Database Changes – \`token\`&#x20;

{% hint style="danger" %}
Some providers will not return a token in the callback response. As such, you will need to modify the `connected_accounts` table migration to make the `token` field nullable:

```php
$table->string('token', 1000)->nullable();
```
{% endhint %}




---
description: >-
  This page will walk you through how to get Socialstream up and running in a
  new Laravel application using Laravel Jetstream with Livewire 3. This example
  will use GitHub as the social provider.
---

# Installation

{% hint style="warning" %}
Socialstream is designed to work in tandem with either of Laravel's starter kits (Breeze and Jetstream) and Filament Admin Panel, and as such, Socialstream should be installed into a **NEW** Laravel applications only. **It is not recommended to install Socialstream into an existing application.**
{% endhint %}

## Example Installation

To install Socialstream into your application you may use `composer`, you will then need to execute the `install` artisan command:

```
composer require joelbutcher/socialstream -W

php artisan socialstream:install
```

The install command will walk you through a bunch of options, such as the starter kit, for this example, choose `Laravel Jetstream`:

```
Socialstream supports Laravel Breeze, Laravel Jetstream, and Filament.

 ┌ Which development starter kit would you like to use? ────────┐
 │   ○ Laravel Breeze                                           │
 │ › ● Laravel Jetstream                                        │
 │   ○ Filament Admin Panel                                     │
 └──────────────────────────────────────────────────────────────┘
```

Next, we will need to choose the technology stack we wish to use, choose `Livewire`.

```
 ┌ Which stack would you like to use? ──────────────────────────┐
 │ › ● Livewire                                                 │
 │   ○ Vue with Inertia                                         │
 └──────────────────────────────────────────────────────────────┘
```

Next, select the optional features you wish to include with your application. In this example, we've decided we want to use Jetstreams `Dark mode` and `Team` features.

```
 ┌ Would you like any optional features? ───────────────────────┐
 │   ◻ API support                                              │
 │   ◼ Dark mode                                                │
 │   ◻ Email verification                                       │
 │ › ◼ Team support                                             │
 └──────────────────────────────────────────────────────────────┘
```

And finally, we have the option of selecting the testing framework we wish to use when building our application. Feel free to make your own decision at this step. In this example, I've chosen `Pest`, the best testing framework for PHP and Laravel.

```
 ┌ Which testing framework do you prefer? ──────────────────────┐
 │   ○ PHPUnit                                                  │
 │ › ● Pest                                                     │
 └──────────────────────────────────────────────────────────────┘
```

Once you've done this, Socialstream will install all the required dependencies for the options you've selected, including pulling in and installing Laravel Jetstream for you – no need to `composer require` another package manually yourself!

```
 Laravel Jetstream is not installed.


 Laravel Jetstream has been installed successfully!

 ⠂ Installing Socialstream...

 Making Socialstream compatible with teams

  Installed Socialstream for Laravel Jetstream (Livewire)
```

You can test the installation by running `php artisan serve` and visiting your application at [`http://127.0.0.1:8000`](http://127.0.0.1:8000):

<figure><img src="../.gitbook/assets/screenshot (1).png" alt=""><figcaption><p><a href="http://127.0.0.1:8000/">http://127.0.0.1:8000/</a></p></figcaption></figure>

You'll notice that if you click on `Log in` or `Register` links at the top of the page that Socialstream isn't loaded, this is because we have a few things to configure before we can use Socialstream.

Firstly, we need to configure our OAuth provider. In this example, we will be using GitHub, head on over to GitHub and go to your profiles developer settings and create a new OAuth app:

<figure><img src="../.gitbook/assets/screenshot (2).png" alt=""><figcaption><p>Register a new OAuth application</p></figcaption></figure>

When you have done this, you will be presented with your applications Client ID, make a note of this for later.

Next, click the `Generate a new client secret` button just under the Client ID. GitHub may prompt you to confirm this action via a 2FA prompt. Make a note of the Client Secret for later.

***

### Configuring the Github Provider

Now that we have our OAuth provider configured, we need to enable the provider in our application. Add the following code snippet to your applications `socialstream.php` config file:

```php
'providers' => [
    Providers::github(),
],
```

{% hint style="info" %}
You may change the text that appears on each providers button, by specifying a `label` in each provider: `Provider::github(label: 'My Provider')`.
{% endhint %}

Now, when you navigate to your applications `/register` route, you will now see the `Login via GitHub` option:

<figure><img src="../.gitbook/assets/screenshot (3).png" alt="" width="375"><figcaption></figcaption></figure>

### Configure Client Credentials

The last thing to do, is configure the client credentials in your application. Add the following to your applications `services.php` config file:

```
    'github' => [
        'client_id' => '<your-client-id>',
        'client_secret' => '<your-client-secret>',
        'redirect' => '/oauth/github/callback',
    ],
```

{% hint style="info" %}
You may add a prefix to your applications Socialstream routes by specifying a `prefix` option in your `socialstream` config file:

```php
`prefix` => 'login',
```

This is especially helpful if you plan to use Laravel Passport, which also defines `/oauth` routes.
{% endhint %}

### Configure Socialstream Features

You may specify the socialstream features you wish for your app to support in the `features` array. A number are already added for you, with some commented out by default.

<pre class="language-php"><code class="lang-php"><strong>    'features' => [
</strong>        // Features::generateMissingEmails(),
        // Features::createAccountOnFirstLogin(),
        // Features::globalLogin(),
        // Features::authExistingUnlinkedUsers(),
        Features::rememberSession(),
        Features::providerAvatars(),
        Features::refreshOAuthTokens(),
    ],
</code></pre>

{% hint style="info" %}
The `providerAvatars` feature requires the `profilePhotos` Jetstream feature to be enabled for it to take affect.
{% endhint %}

## Installation Options

Socialstream supports a number of installation options depending on the starter kit (and stack) you choose to install. With each starter kit and stack, Socialstream will install all the required dependencies  for each one.

### Laravel Jetstream

Socialstream supports Laravel Jetstream with Vue (Inertia) or Livewire 3.0, with the following options:

* `--dark` – dark mode support
* `--api` – allows users to manage API tokens (distrubuted via Laravel Sanctum)
* `--verification` – user email verification
* `--teams` – Jetstreams team management features
* `--pest` – support for the best testing library for Laravel, Pest PHP
* `--ssr` – server side rendering (Vue with Inertia only)

### Laravel Breeze

Socialstream supports all of Laravel Breeze's stack options; including the new [Volt Class and Functional](https://livewire.laravel.com/docs/volt) API's.

The following options are available for Laravel Breeze:

* `--dark` – dark mode support
* `--pest` – support for the best testing library for Laravel, Pest PHP
* `--ssr` – server side rendering (Vue and React with Inertia only)
* `--typescript` – typescript support (Vue and React with Inertia only)

### Filament Admin Panels

Socialstream supports Filament Admin Panels, for admin login and registration (if enabled). It does this via a plugin extension for the `AdminPanelProvider`. It appends the `socialstream::components.socialstream` component to the bottom of both forms:

```php
$panel->renderHook('panels::auth.login.form.after', function () {
    return Socialstream::show() ?
        view(config('socialstream.component', 'socialstream::components.socialstream'), [
            'errors' => session('errors') ?? new ViewErrorBag(),
        ]) : '';
});

if ($panel->hasRegistration()) {
    $panel->renderHook('panels::auth.register.form.after', function () {
        return Socialstream::show() ?
            view(config('socialstream.component', 'socialstream::components.socialstream'), [
                'errors' => session('errors') ?? new ViewErrorBag(),
            ]) : '';
    });
}
```

{% hint style="info" %}
If you wish to change the layout of the views for filament, you may publish Socialstream components using the `vendor:publish` artisan command:

```sh
php artisan vendor:publish --tag=socialstream-views
```
{% endhint %}

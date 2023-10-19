# Configuration

After installing Socialstream, the primary configuration file will be located at `config/socialstream.php`. This configuration file allows you to configure the middleware Socialstream's routes sit behind, the prompt used to divide socialstream from the other authentication components on the `login` and `registration` panels, as well as the providers and features that are enabled.

## Middleware

By default, Socialstream uses the `web` middleware. For most applications, this should be the only middleware required for Socialstream's routes. However, you are free to change this to suit your applications needs.

## Prompt

The prompt is the text between the "Login" or "Registration" forms and Socialstreams OAuth buttons, this is set to `Or Login Via` by default. You are free to change this as you see fit.

## Providers

No providers are enabled by default (and thus the Socialstream section of the "Login" and "Registration" views is not rendered. However, you are free to enable as many providers as you wish.&#x20;

### Adding Providers

Socialstream natively only ships with support for the providers offered by [Laravel Socialite](https://laravel.com/docs/10.x/socialite). If you wish to add a provider, you may do so one of three ways:

#### Via the \`Providers\` static class

Socialstream ships with a `Providers` class, with static methods for each of Socialites supported providers. This class is imported in the default configuration file, and may be called like so:

```php
'providers' => [
    Providers::github(),
],
```

#### As a string

You may simply pass a string into the `providers` array within Socialstream's configuration file to add a provider.

{% hint style="warning" %}
Please note that the string value must also match the value of a service in your applications `services.php` configuration file as this is what is used to retrieve a providers credentials.
{% endhint %}

#### As an array

Additional providers may be added as an array:

```php
'providers' => [
    [
        'id' => 'github',
        'name' => 'GitHub',
    ],
]
```

The `id` property is the valid passed to Socialite when calling `Socialite::provider(...)` . This is then used by Socialite to resolve the OAuth credentials from your applications `config/services.php` file.

The `name` property is used in the "Connect Accounts" panel when rendering what providers are (or aren't) connected to your profile.

### Button Labels

As of v5, Socialstream now ships with the ability to define the button labels rendered in the "Login" and "Registration" buttions:

```php
// Via 'Providers' helper
Providers::github(label: 'Continue with GitHub'),

// Via array
[
    'id' => 'github',
    'name' => 'GitHub',
    'buttonLabel' => 'Continue with GitHub',
],
```

If a button label is not specified, Socialstream will fall back to the `name` property. If you have passed just a string, Socialstream will format the ID in a human readable way and render that as the button label instead.

## Features

You may specify which of Socialstream's features are enabled here, such as the profile avatar feature. Available feature options can be found in this sites navigation.

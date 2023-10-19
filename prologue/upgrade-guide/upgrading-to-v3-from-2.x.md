# Upgrading to v3 from 2.x

{% hint style="info" %}
This upgrade guide only discusses upgrading Socialstream 3.x and assumes you are upgrading from an **unaltered 2.x install**. Upgrading Jetstream, Livewire, Inertia, Vue or Tailwind CSS is out of scope for this documentation. Please consult the upgrade guides for each of these packages instead.
{% endhint %}

## Overview <a href="#user-content-changes-1" id="user-content-changes-1"></a>

* [Changes](upgrading-to-v3-from-2.x.md#user-content-changes-1-1)
  * [Disabling Socialstream](upgrading-to-v3-from-2.x.md#disabling-socialstream)
  * [Providers](upgrading-to-v3-from-2.x.md#providers)
  * [Remember Sessions](upgrading-to-v3-from-2.x.md#remember-sessions)
  * [Token Column Length](upgrading-to-v3-from-2.x.md#token-column-lengths)
  * [Provider Avatars](upgrading-to-v3-from-2.x.md#provider-avatars)
  * [Connected Account Policy](upgrading-to-v3-from-2.x.md#connected-account-policy)
  * [Updating Connected Accounts](upgrading-to-v3-from-2.x.md#updating-connected-accounts)
  * [Resolving Users from Socialite](upgrading-to-v3-from-2.x.md#resolving-users-from-socialite)

***

## Changes <a href="#user-content-changes-1" id="user-content-changes-1"></a>

### Disabling Socialstream

{% hint style="info" %}
Impact: **Low**
{% endhint %}

To disable Socialstream in v3, you will need to update your existing `SocialstreamServiceProvider.php` to include the following code snippet in your providers `boot` method:

```php
Socialstream::enabled(fn () => false);
```

The function accepts a callback so if you wanted to implement more complex logic, you may do so.

> Note, the callback MUST return a boolean

### Providers

{% hint style="info" %}
Impact: **Low**
{% endhint %}

V3 introduces a new `Providers` class, for defining what Socialite providers you have enabled in your config. This class is also used in the socialstream.blade.php stub and the connected-account.blade.php component stub. Please update any Socialite providers you have in your `socialstream.php` config file to use this class, e.g:

```php
use \JoelButcher\Socialstream\Providers;

return [
    // ...

    'providers' => [
        Providers::google(),
        Providers::facebook(),
    ],

];
```

### Remember Sessions

{% hint style="info" %}
Impact: **Low**
{% endhint %}

V3 of Socialstream move the remember session config variable into the 'features' config array. During your upgrade, if you have previously set this config variable to `true`, you will need to add it to your features list.

```php
return [
    // ...

    'features' => [
        Features::rememberSession(),
    ],

];
```

### Token Column Lengths

{% hint style="info" %}
Impact: **Low**
{% endhint %}

In version 3.x, we've fixed an issue with the length of tokens and refresh tokens being too long for the columns in the database.

To fix this yourself, you should create a new `connected_accounts` migration:

```sh
php artisan make:migration update_connected_accounts_token_lengths --table=connected_accounts
```

Once done, you should then add the following code to the `up` method:

```php
$table->string('token', 1000)->change();
$table->string('refresh_token', 1000)->change();
```

### Provider Avatars

{% hint style="info" %}
Impact: **Low**
{% endhint %}

In v3, we've updated the provider avatars feature to download the avatar from the url provided by the Socialite user. If you have opted to use the `providerAvatars` feature in your config's features definition, you should add the `SetsProfilePhotoFromUrl` trait to your user model:

```php
<?php

use JoelButcher\Socialstream\SetsProfilePhotoFromUrl;

class User extends Authenticatable
{
    // ...
    use SetsProfilePhotoFromUrl;

    // ...
}
```

It's worth noting that if you still to load the users profile photo from a URL, you will need to keep the `getProfilePhotoUrlAttribute` method override published by Socialstream, in your user model.

### Connected Account Policy

{% hint style="info" %}
Impact: **Low**
{% endhint %}

V3 uses a new policy to determine whether or not a user can access certain functionality. To ensure compatibility with v3, make sure you copy the stub found [here](https://github.com/joelbutcher/socialstream/blob/3.x/stubs/app/Policies/ConnectedAccountPolicy.php) to your `app/Policies` directory.

### Updating Connected Accounts

{% hint style="info" %}
Impact: **Low**
{% endhint %}

Socialstream v3 will now keep your connected accounts up to date whenever a user uses SSO with your application. updating a users OAuth token and refresh token on every successful login. To make sure this works for you, copy [this stub](https://github.com/joelbutcher/socialstream/blob/3.x/stubs/app/Actions/Socialstream/UpdateConnectedAccount.php) into the `app/Actions/Socialstream` directory. You will then need to add the following to your `app\Providers\SocialstreamServiceProvider.php`:

```php
use App\Actions\Socialstream\UpdateConnectedAccount;
use JoelButcher\Socialstream\Socialstream;

// Add this to the 'boot' method.
Socialstream::updateConnectedAccountsUsing(UpdateConnectedAccount::class);
```

### Resolving Users from Socialite

{% hint style="info" %}
Impact: **Low**
{% endhint %}

To allow additional flexibility, v3 allows you to override how your app resolves users from Socialite. For example, you may wish to call the `stateless` method, like so:

```php
$user = Socialite::driver('facebook')->stateless()->user();
```

To ensure v3 compatibility, copy the `ResolveSocialiteUser` action stub found [here](https://github.com/joelbutcher/socialstream/blob/3.x/stubs/app/Actions/Socialstream/ResolveSocialiteUser.php) to your `app/Actions/Socialstream` directory and add the following to your `app\Providers\SocialstreamServiceProvider.php`:

```php
use App\Actions\Socialstream\ResolveSocialiteUser;
use JoelButcher\Socialstream\Socialstream;

// Add this to the 'boot' method.
Socialstream::resolvesSocialiteUsersUsing(ResolveSocialiteUser::class);
```

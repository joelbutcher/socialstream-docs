# Upgrading to v5 from 4.x

{% hint style="info" %}
This upgrade guide only discusses upgrading Socialstream 5.x and assumes you are upgrading from an **unaltered 4.x install**. Upgrading Jetstream, Livewire, Inertia, Vue or Tailwind CSS is out of scope for this documentation. Please consult the upgrade guides for each of these packages instead.
{% endhint %}

## Overview

* [Upgrading via `socialstream:upgrade`](upgrading-to-v5-from-4.x.md#upgrading-via-socialstream-upgrade)
* [Upgrading Manually](upgrading-to-v5-from-4.x.md#upgrading-manually)
  * [Updating Dependencies](upgrading-to-v5-from-4.x.md#updating-dependencies)
  * [Renaming Oauth to OAuth](upgrading-to-v5-from-4.x.md#renaming-oauth-to-oauth)
  * [The `Socialstream::refresesOauthTokens` Method](upgrading-to-v5-from-4.x.md#the-socialstream-refresesoauthtokens-method)
  * [The `Socialstream` component](upgrading-to-v5-from-4.x.md#the-socialstream-component)
  * [The `Socialstream::providers` Method](upgrading-to-v5-from-4.x.md#the-socialstream-providers-method)
    * [Livewire Stack Changes](upgrading-to-v5-from-4.x.md#livewire-stack-changes)
    * [Inertia Stack Changes](upgrading-to-v5-from-4.x.md#inertia-changes)
  * [The `HandlesInvalidState::handle` method](upgrading-to-v5-from-4.x.md#the-handlesinvalidstate-handle-method)

***

## Upgrading via `socialstream:upgrade`

{% hint style="info" %}
The `socialstream:upgrade` artisan command is only available **after** installing Socialstream `5.0` and assumes you have an **unaltered** `4.x` install.
{% endhint %}

Upgrading Socialstream has never been easier. With `5.0` we have created an artisan command that will guide you through all the changes you need to make. Simply execute the following command:

```sh
php artisan socialstream:upgrade
```

You may then follow the on-screen instructions to complete the upgrade to Socialstream 5.0.

***

## Upgrading Manually

### Updating Dependencies

{% hint style="danger" %}
Impact: **High**
{% endhint %}

Socialstream v5.0 removes `laravel/jetstream` as a dependency due to adding support for multiple stater kit options. Before upgrading, you should ensure that jetstream is added to the `require` dependencies your `composer.json` file. You can do this by executing the following command:

```sh
composer require laravel/jetstream:
```

### Renaming Oauth to OAuth

{% hint style="danger" %}
Impact: **High**
{% endhint %}

v5 applies a patch to rename all incorrect instances of "Oauth" with "OAuth". Please make sure you have updated any usages to also match this change.

### The `Socialstream::refresesOauthTokens` Method

{% hint style="danger" %}
Impact: **High**
{% endhint %}

This method has been renamed to fix the typo, if you are using the method, please update it to `refreshesOAuthTokens`.

### The `Socialstream` Component

{% hint style="warning" %}
Impact: **Medium**
{% endhint %}

The main `Socialstream` component has been updated, you should replace it's contents with the one found on GitHub for your chosen Jetstream stack:

* [Livewire](https://github.com/joelbutcher/socialstream/blob/18f0990f3c51b22271d7edb2a40e0afdd3d40fdf/stubs/jetstream/livewire/resources/views/components/socialstream.blade.php)
* [Inertia](https://github.com/joelbutcher/socialstream/blob/18f0990f3c51b22271d7edb2a40e0afdd3d40fdf/stubs/jetstream/inertia/resources/js/Components/Socialstream.vue)

### Provider Icons

{% hint style="warning" %}
Impact: **Medium**
{% endhint %}

The new default installation for Socialstream moves the logic for showing a providers icon to it's own component for each stack. It is recommended that you copy this logic over for your stack and update your existing Socialstream installation to reference the new component:

**Livewire**

Create a new `provider-icon.blade.php` within `resources/views/components/socialstream-icons` inside your Laravel Application and add the contents from [here](https://github.com/joelbutcher/socialstream/blob/bb100b8d6f5f2cc046b61f845b92ba9c5fa64fcf/stubs/jetstream/livewire/resources/views/components/socialstream-icons/provider-icon.blade.php), making sure to cater for any additional icons you may have added.

Replace any `@if`, `@elseif` and `@else` directive calls that are used to determine what icons are shown with:

```php
<x-socialstream-icons.provider-icon :provider="$provider['id']" class="h-6 w-6"/>
```

### The `Socialstream::providers` Method

{% hint style="danger" %}
Impact: **High**
{% endhint %}

The `Socialstream::providers` method now returns an array of arrays for each registered provider. The data structure for providers is built using `JoelButcher\Socialstream\Data\ProviderData::toArray` and looks like the following example:

```
[
    'id' => 'github',
    'name' => 'GitHub',
    'buttonLabel' => 'GitHub', // configurable, can also be null.
]
```

#### **Livewire Stack Changes**

**Add a new `provider-icon.blade.php` Component**

This new component is a switch-case around the `$provider['id']` property and used to determine which icon should be shown. You should place this component within the `resources/views/components/socialstream-icons` directory of your Laravel application. The source code can be found [here](https://github.com/joelbutcher/socialstream/blob/5.x/stubs/jetstream/livewire/resources/views/components/socialstream-icons/provider-icon.blade.php).

{% hint style="info" %}
You will want to include the cases for any additional providers you may have added to your Socialstream installation.
{% endhint %}

This component is referenced within [`socialstream.blade.php` component](https://github.com/joelbutcher/socialstream/blob/18f0990f3c51b22271d7edb2a40e0afdd3d40fdf/stubs/jetstream/livewire/resources/views/components/socialstream.blade.php) and the [`connected-account.blade.php` component](https://github.com/joelbutcher/socialstream/blob/cbf0c55a4dbf2cef48edccf80b52115c69b205b1/stubs/jetstream/livewire/resources/views/components/connected-account.blade.php#L6C13-L6C95).

**Changes to `connected-account.blade.php`**

Socialstream `5.0` updates the `connected-account` component to use the new provider array. You should update your version of this component to match the source code's [expectations](https://github.com/joelbutcher/socialstream/blob/cbf0c55a4dbf2cef48edccf80b52115c69b205b1/stubs/jetstream/livewire/resources/views/components/connected-account.blade.php#L10).

You should also update your applications `connected-accounts-form.blade.php` to reflect the change to the `$provider` array:

```php
// Replace line 27
$account = $this->accounts->where('provider', $provider)->first();

// With
$account = $this->accounts->where('provider', $provider['id'])->first();

---

// Replace line 30
<x-connected-account provider="{{ $provider }}" created-at="{{ $account?->created_at }}">

// With
<x-connected-account :provider="$provider" created-at="{{ $account?->created_at }}">

---

// Replace line 47
<x-action-link href="{{ route('oauth.redirect', ['provider' => $provider]) }}">

// With
<x-action-link href="{{ route('oauth.redirect', ['provider' => $provider['id']]) }}">

---


// Replace line 72
<x-danger-button class="ml-2" wire:click="removeConnectedAccount({{ $this->selectedAccountId }})" wire:loading.attr="disabled">

// With
<x-danger-button class="ml-2" wire:click="removeConnectedAccount({{ $this->id }})" wire:loading.attr="disabled">
```

#### Inertia **Stack** Changes

**Add a new `ProviderIcon.vue` Component**

Socialstream 5.0 adds a new `ProviderIcon.vue` component. You will want to add this component from [GitHub](https://github.com/joelbutcher/socialstream/blob/24a99bb8a8d3a0dc2a3ee71e04e68768c65c6a27/stubs/jetstream/inertia/resources/js/Components/SocialstreamIcons/ProviderIcon.vue) and ensure any additional provider icons you have created are catered for.

This component is referenced within [`Socialstream.vue` ](https://github.com/joelbutcher/socialstream/blob/18f0990f3c51b22271d7edb2a40e0afdd3d40fdf/stubs/jetstream/livewire/resources/views/components/socialstream.blade.php)and [`ConnectedAccount.vue`](https://github.com/joelbutcher/socialstream/blob/cbf0c55a4dbf2cef48edccf80b52115c69b205b1/stubs/jetstream/livewire/resources/views/components/connected-account.blade.php#L6C13-L6C95).

**Changes to `ConnectedAccount.vue`**

The connected account component has been updated to reflect the changes made to the `Socialstream::providers` method. As a result, existing installations of this component will now break. You will want to make sure that your `ConnectedAccount.vue` component is updated to align with the [new version on GitHub](https://github.com/joelbutcher/socialstream/blob/24a99bb8a8d3a0dc2a3ee71e04e68768c65c6a27/stubs/jetstream/inertia/resources/js/Components/ConnectedAccount.vue).

**Changes to Auth Views**

Both `Login.vue` and `Register.vue` have been updated to pass properties from `$page.props.socialstream` into the updated `Socialstream.vue` component. Please make sure to review these changes and update your application accordingly.

**Changes to `ConnectedAccountsForm.vue` component**

A lot of the logic in this component has been updated to align with the changes made to `Socialstream::providers`, you may wish to review the [code on GitHub](https://github.com/joelbutcher/socialstream/blob/24a99bb8a8d3a0dc2a3ee71e04e68768c65c6a27/stubs/jetstream/inertia/resources/js/Pages/Profile/Partials/ConnectedAccountsForm.vue) and update your version of the component to ensure compatibility.

***

### The `HandlesInvalidState::handle` method

{% hint style="info" %}
Impact: **Low**
{% endhint %}

The `HandlesInvalidState` contract has been updated to remove the `$callback` parameter, this parameter was never used by Socialstream or called in anyway. You should update your implementations to also remove this parameter.

# Upgrading to v2 from 1.x

{% hint style="info" %}
This upgrade guide only discusses upgrading Socialstream 2.x and assumes you are upgrading from an **unaltered 1.x install**. Upgrading Jetstream, Livewire, Inertia, Vue or Tailwind CSS is out of scope for this documentation. Please consult the upgrade guides for each of these packages instead.
{% endhint %}

## Overview

* [Changes](upgrading-to-v2-from-1.x.md#user-content-changes-2)
  * [Connected Account Details](upgrading-to-v2-from-1.x.md#connected-account-details)
  * [Current Connected Account Context](upgrading-to-v2-from-1.x.md#current-connected-account-context)
  * [Create Connected Account Action](upgrading-to-v2-from-1.x.md#create-connected-account-action)
  * [Generate Provider Redirect Action](upgrading-to-v2-from-1.x.md#generate-provider-redirect-action)
  * [Connected Account Credentials](upgrading-to-v2-from-1.x.md#user-content-connected-account-credentials)
  * [Inertia Stack Changes](upgrading-to-v2-from-1.x.md#user-content-inertia-stack)
    * [Authentication Views](upgrading-to-v2-from-1.x.md#authentication-views)

***

## Changes <a href="#user-content-changes-2" id="user-content-changes-2"></a>

### Connected Account Details

{% hint style="danger" %}
Impact: **High**
{% endhint %}

Version 2.x of Socialstream now captures more user data from a provider and saves them to your `connected_accounts` table. In order to correctly save this data, you will need to create a new migration to make the appropriate changes.

To do this, you should create a new `connected_accounts` migration:

```sh
php artisan make:migration update_connected_accounts_table --table=connected_accounts
```

The geneated migration should contain the following code:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class UpdateConnectedAccountsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('connected_accounts', function (Blueprint $table) {
            $table->string('name')->after('provider_name')->nullable();
            $table->string('nickname')->after('name')->nullable();
            $table->string('email')->after('nickname')->nullable();
            $table->string('telephone')->after('email')->nullable();
            $table->string('avatar_path')->after('telephone')->nullable();

            $table->renameColumn('provider_name', 'provider');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('connected_accounts', function (Blueprint $table) {
            // Revert...
        });
    }
}
```

### Current Connected Account Context

{% hint style="danger" %}
Impact: **High**
{% endhint %}

When you login using a social provider, Socialstream will now set the context for the most-recent, or "current" provider being used. To do this, a new `current_connected_account_id` column will need adding to your users table.

Generate a new `users` migration:

```sh
php artisan make:migration update_users_table --table=users
```

The migration should be popuplated with the following content:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class UpdateUsersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('users', function (Blueprint $table) {
            $table->foreignId('current_connected_account_id')->after('current_team_id')->nullable();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('users', function (Blueprint $table) {
            //
        });
    }
}
```

### Create Connected Account Action

{% hint style="danger" %}
Impact: **High**
{% endhint %}

Socialstream 2.x adds a new action for creating connected accounts on registration with a provider. You should copy the new [CreateConnectedAccount](https://github.com/joelbutcher/socialstream/blob/2.x/stubs/app/Actions/Socialstream/CreateConnectedAccount.php) action to the `App/Actions/Socialstream` directory in your project.

You should then add the register the action with Socialstream by placing the following code into the `boot` method of your application's `SocialstreamServiceProvider`:

```php
use App\Actions\Socialstream\CreateConnectedAccount;

Socialstream::createConnectedAccountsUsing(CreateConnectedAccount::class);
```

### Generate Provider Redirect Action

{% hint style="danger" %}
Impact: **High**
{% endhint %}

Socialstream 2.x includes a new action to generate the redirects URI's used to authenticate with providers.

You should then register this action with Socialstream by placing the following code into the `boot` method of your application's `SocialstreamServiceProvider`:

```php
use App\Actions\Socialstream\GenerateRedirectForProvider;

Socialstream::generatesProvidersRedirectsUsing(GenerateRedirectForProvider::class);
```

If you wish, you may override this action by writing your own. This may allow you to define `scopes` or additional parameters, such as a `response_type` for explicit grants. See below for an example.

> Note: the action **MUST** implement the `JoelButcher\Socialstream\Contracts\GeneratesProviderRedirect` contract.

```php
<?php

namespace App\Actions\Socialstream;

use JoelButcher\Socialstream\Contracts\GeneratesProviderRedirect;
use Laravel\Socialite\Facades\Socialite;

class GenerateRedirectForProvider implements GeneratesProviderRedirect
{
    /**
     * Generates the redirect for a given provider.
     *
     * @param  string  $provider
     *
     * @return \Symfony\Component\HttpFoundation\RedirectResponse
     */
    public function generate(string $provider)
    {
        return Socialite::driver($provider)
            ->scopes(['*'])
            ->with(['response_type' => 'token'])
            ->redirect();
    }
}
```

### Connected Account Credentials <a href="#user-content-connected-account-credentials" id="user-content-connected-account-credentials"></a>

{% hint style="info" %}
Impact: **Low**
{% endhint %}

For convenience, Socialstream now also provides a `Credentials` helper class which can be used for authenticating with additional provider API's (e.g. Facebook's Graph API).

You may retrieve an instance of this class from a connected account:

```php
$connectedAccount = \App\Models\ConnectedAccount::first();

$credentials = $connectedAccount->getCredentials();
```

### Inertia Stack <a href="#user-content-inertia-stack" id="user-content-inertia-stack"></a>

#### Authentication Views

To upgrade your application's authentication views to use the new Vue files from Jetstream 2.x, you should copy the [Jetstream auth files](https://github.com/laravel/jetstream/tree/2.x/stubs/inertia/resources/js/Pages/Auth), then the [Socialstream auth files](https://github.com/joelbutcher/socialstream/tree/2.x/stubs/inertia/resources/js/Pages/Auth) to the `resources/js/Pages/Auth` folder location.

You will also need to copy the [Providers.vue](https://github.com/joelbutcher/socialstream/blob/2.x/stubs/inertia/resources/js/Socialstream/Providers.vue) file to the `resources/js/Socialstream` directory.

However, if you wish to continue to render the Blade based authentication views, you should add the following code to the `boot` method of your application's `JetstreamServiceProvider` class:

```php
use Illuminate\Support\Facades\Route;
use Laravel\Fortify\Fortify;

Fortify::loginView(function () {
    return view('auth/login', [
        'canResetPassword' => Route::has('password.request'),
        'status' => session('status'),
    ]);
});

Fortify::requestPasswordResetLinkView(function () {
    return view('auth/forgot-password', [
        'status' => session('status'),
    ]);
});

Fortify::resetPasswordView(function (Request $request) {
    return view('auth/reset-password', [
        'email' => $request->input('email'),
        'token' => $request->route('token'),
    ]);
});

Fortify::registerView(function () {
    return view('auth/register');
});

Fortify::verifyEmailView(function () {
    return view('auth/verify-email', [
        'status' => session('status'),
    ]);
});

Fortify::twoFactorChallengeView(function () {
    return view('auth/two-factor-challenge');
});

Fortify::confirmPasswordView(function () {
    return view('auth/confirm-password');
});
```

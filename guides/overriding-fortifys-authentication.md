# Overriding Fortify's Authentication

{% hint style="success" %}
This guide looks to provide assistance that specifically tackles scenarios where you may be overriding Fortify's authentication pipeline. via `Fortify::authenticateUsing()`.
{% endhint %}

## Background

If you are using the Socialstream + Jetstream stack, you may want to opt-in to Fortify's Two Factor Authentication mechanics. When logging in via Socialstream, we have had to override two fundamental authentication classes with our own. The first is to create our own `eloquent`  user provider (found [here](https://github.com/joelbutcher/socialstream/blob/5309717b6d769ae9359844d518e835e1df894135/src/Auth/SocialstreamUserProvider.php) and registered [here](https://github.com/joelbutcher/socialstream/blob/d115bc82a4e15631faffcf512cbaf85e67ca8998/src/SocialstreamServiceProvider.php#L98-L101)). The second is to create our own version of Fortify's [login pipeline](https://github.com/laravel/fortify/blob/42695c45087e5abb3e173725b4f1ef4956a7b47d/src/Http/Controllers/AuthenticatedSessionController.php#L71) and use our own `RedirectIfTwoFactorAuthenticatable` class (which extends Fortify's one, but overrides the `validateCredentials` logic with our own).

If you're overriding the authentication pipeline to handle custom business logic (for example, [to check if a user has been blocked by an admin and redirect away if so](https://github.com/joelbutcher/socialstream/issues/370#issuecomment-2363262761)) you will need to make sure you include the same logic we use our `RedirectIfTwoFactorAuthenticatable` override, to make sure that login with socialstream providers still works.

```php
use App\Models\User;
use JoelButcher\Socialstream\Contracts\ResolvesSocialiteUsers;
use JoelButcher\Socialstream\Socialstream;
use Illuminate\Validation\ValidationException;

Fortify::authenticateUsing(function (Request $request) {
    if ($provider = $request->route('provider')) {
        $socialUser = app(ResolvesSocialiteUsers::class)
            ->resolve($provider);
    
        $connectedAccount = Socialstream::$connectedAccountModel::where('email', $socialUser->getEmail())->first();
    
        if (! $connectedAccount) {
            ValidationException::withMessages([
                Fortify::username() => [__('auth.failed')],
            ]);
        }
    
        return $connectedAccount->user;
    }
    // You're custom authentication logic here.
});
```

### Example

In this example, we're checking to see if a user has been blocked by and admin and returning a validation error if that is the case.

{% hint style="warning" %}
If you are following the example in Fortify's documentation and you are doing a hash check for the user entered password against the hash stored on the model, you will want to make sure you **don't** do this check for Socialstream routes.
{% endhint %}

First, check our route param to see if the user is coming from a Socialstream OAuth callback route:

```php
use App\Models\User;
use JoelButcher\Socialstream\Contracts\ResolvesSocialiteUsers;
use JoelButcher\Socialstream\Socialstream;
use Illuminate\Validation\ValidationException;

Fortify::authenticateUsing(function (Request $request) {
    $user = null;
    $provider = $request->route('provider');

    // 1a. Attempt the resolve the user via socialstream
    if ($provider) {
        $socialUser = app(ResolvesSocialiteUsers::class)
            ->resolve($provider);

        $connectedAccount = Socialstream::$connectedAccountModel::where('email', $socialUser->getEmail())->first();

        if (! $connectedAccount) {
            throw ValidationException::withMessages([
                Fortify::username() => [__('auth.failed')],
            ]);
        }

        $user = $connectedAccount->user;
    }

    // 1b. Attempt to resolve the user if email present in request (i.e. from login form).
    if (! $user && $request->has('email')) {
            $user = User::where('email', $request->email)->first();
    }

    // 2. Check if the resolved user is blocked and handle
    if ($user->blockedByAdmin()) {
        throw ValidationException::withMessages([
            Fortify::username() => [__('auth.blocked')],
        ]);
    }

    // 3. User is not blocked, log in if from Socialstream route
    if ($provider) {
        return $user;
    }

    // 4. User hasn't set a password, so must login using an OAuth provider
    if (is_null($user->password)) {
        throw ValidationException::withMessages([
            Fortify::username() => [__('auth.failed')],
        ]);
    }

    // 5. Verify the password if the user has logged in via a form
    return Hash::check($request->password, $user->password) ? $user : null;
});
```


# Overriding the Callback Logic

Want even more freedom over your application? Well as of Socialstream v4.1.0, you can specifiy overrides for how Socialstream handles the OAuth callback part of the flow. 🤯

## Handling OAuth 2.0 Errors

If the callback from a provider contains an `errors` key in the request, Socialstream handles this via a new `HandlesOauthCallbackErrors` interface. By default, the implementation is bound to the `\JoelButcher\Socialstream\Actions\Auth\HandleOauthCallbackErrors` implementation, but you can change this at any time by specifying a custom callback within the `boot` method of your applications `AppServiceProvider`:

```php
<?php

namespace App\Providers

use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    public function boot(): void
    {
        Socialstream::handlesOAuthCallbackErrorsUsing(function ($app) {
            // ...
        });
    }
}
```

Alternatively, you can provider the FQN of a `HandlesOauthCallbackErrors` instance:

```php
<?php

namespace App\Providers

use App\Actions\Socialstream\Auth\HandleCallbackErrors;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    public function boot(): void
    {
        Socialstream::handlesOAuthCallbackErrorsUsing(HandleCallbackErrors::class);
    }
}
```

## Override Callback Logic

Socialstream now deferes all OAuth authentication logic to a new `AuthenticatesOauthCallback` implementation. This implementation lives within the source code for socialstream and can remain unchanged for the majority of use-cases. However, should you wish to override, or customise this logic, you can do so by providing your own implementation. Simple define a closure, or create a class that implements the `AuthenticatesOauthCallback` contract and specify this within the `boot` method of your applications `AppServiceProvider`:

```php
<?php

namespace App\Providers

use App\Actions\Socialstream\Auth\HandleOAuthCallback;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    public function boot(): void
    {
        // Socialstream::authenticatesOauthCallbackUsing(function ($app) {
            // ...
        // });
        Socialstream::authenticatesOauthCallbackUsing(HandleOAuthCallback::class);
    }
}
```


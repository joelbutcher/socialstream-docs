# Authenticating Users

The main benefit of using Socialstream versus writing an OAuth integration for your Laravel application yourself, is that it handles all of the authentication logic for you. If you wish, you may override this logic, by registering your own "authenticator" with Socialstream. To do so, simply create a class that implements Socialstream's `AuthenticatesOauthCallback` contract, and register it with Socialstream via the `boot` method of a service provider:

```php
public function boot(): void
{
    Socialstream::authenticatesOauthCallbackUsing(CustomAuthenticateOauthCallback::class);
}
```

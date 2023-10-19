# Resolving Users

Socialstream resolves users from Socialite using the following logic:

```php
Socialite::driver($provider)->user();
```

However, you may decide you want to customise this functionality. For example, you may decide to use the `stateless` method to [disable state verification](https://laravel.com/docs/9.x/socialite#stateless-authentication). You may do this by updating the `resolve` method in `app/Actions/Socialstream/ResolveSocialiteUser.php`:

```php
/**
 * Resolve the user for a given provider.
 */
public function resolve(string $provider): User
{
    return match ($provider) {
        Providers::google() => Socialite::driver($provider)->stateless()->user(),
        default => Socialite::driver($provider)->user(),
    };
}
```

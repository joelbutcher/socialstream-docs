# Socialite Redirect

Socialstream generates the OAuth redirect for a provider using the following logic:

```php
Socialite::driver($provider)->redirect()
```

You may find you want to alter this logic; for example, you may wish to alter the scopes requested from the provider at runtime. To do so, simply update the `generate` method of your applications `app/Actions/Socialstream/GenerateRedirectForProvider.php` action file:

```php
public function generate(string $provider): RedirectResponse
{
    $scopes = ['*'];

    if ($provider = 'github' && config('services.github.manage_repos') {
        $scopes = array_merge($scopes, [
            'repos.manage',
        ]);
    }
    
    return Socialite::driver($provider)
        ->scopes($scopes)
        ->with(['response_type' => 'token'])
        ->redirect();
}
```

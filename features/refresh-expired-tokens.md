# Refresh Expired Tokens

As of Socialstream v4.1.0, socialstream will now automatically check and refresh expired access tokens when they're retrieved from the database. To do this, socialstream uses the `client_id` and `client_secret` values stored in your applications services.php config file.

{% hint style="warning" %}
This may slow down your application depending on any delays in the request to the providers OAuth server.
{% endhint %}

This feature is enabled by default, to disable this feature, simply remove it from the `features` array inside your `socialstream.php` config file:

```php
return [
    'features' => [
        // ...
        Features::refreshOauthTokens(),
    ],
];
```

You may wish to customise the logic behind refreshing an expired token, to do this, you can provide a closure to the `Socialstream::refreshesProviderTokenWith()` method from within the `boot` method of your applications `AppServiceProvider`. For example, for GitHub, this would look like the following:

```php
Socialstream::refreshesProviderTokenWith('github', function (ConnectedAccount $account) {
    $response = Http::asForm()->post(...);

    return new RefreshedCredentials(
        token: $response['access_token'],
        refreshToken: $response['refresh_token'],
        expiry: now()->addSeconds($response['expires_in']),
    );
});
```

Alternatively, you may provide the method with the FQN of a resolver class. This class must extend the base provider class from either Laravel Socialite, or the Socialite Providers package you are using. For example, if you were to use the `Reddit` provider from [socialiteproviders/reddit](https://github.com/SocialiteProviders/Reddit), you may define a class for refresh Reddit Oauth tokens and use that instead:

```php
<?php

namespace App\RefreshTokenProviders;

use GuzzleHttp\RequestOptions;
use JoelButcher\Socialstream\Concerns\RefreshesOauth2Tokens;
use JoelButcher\Socialstream\Contracts\RefreshTokenProvider;
use SocialiteProviders\Reddit\Provider as RedditProvider;

class RedditOauth2RefreshResolver extends RedditProvider implements RefreshTokenProvider
{
    use RefreshesOauth2Tokens;

    public function __construct()
    {
        parent::__construct(
            request: request(),
            clientId: config('services.reddit.client_id'),
            clientSecret: config('services.reddit.client_secret'),
            redirectUrl: '',
        );

        $this->guzzle = [
            RequestOptions::AUTH => [$this->clientId, $this->clientSecret],
        ];
    }
}
```

You would then add the following to the `boot` method of your applications `AppServiceProvider`:

```php
Socialstream::refreshesProviderTokenWith('reddit', RedditOauth2RefreshResolver::class);
```

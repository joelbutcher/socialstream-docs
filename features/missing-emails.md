# Missing Emails

Some providers (such as GitHub) don't always return an email address when authenticating with them. To attempt to solve this problem, Socialstream offers the ability to generate an email for any user this happens with. This email address is a unique combination of the `user_id` returned after authentication and the name of the provider used to authenticate with, coupled with your applications domain name:

```php
$email = "{$user->id}@{$provider}".config('app.domain');
```

{% hint style="info" %}
For example, for a GitHub user with the ID of 758273, the resulting email address would be `758273@github.myapp.com`
{% endhint %}

If you wish to customise how your application handles missing email, Socialstream publishes a `ResolveSocialiteUser.php` file containing this logic in your applications `app/Actions/Socialstream` directory:

```php
<?php

namespace App\Actions\Socialstream;

use JoelButcher\Socialstream\Contracts\ResolvesSocialiteUsers;
use JoelButcher\Socialstream\Socialstream;
use Laravel\Socialite\Contracts\User;
use Laravel\Socialite\Facades\Socialite;

class ResolveSocialiteUser implements ResolvesSocialiteUsers
{
    /**
     * Resolve the user for a given provider.
     */
    public function resolve(string $provider): User
    {
        $user = Socialite::driver($provider)->user();

        if (Socialstream::generatesMissingEmails()) {
            $user->email = $user->getEmail() ?? ("{$user->id}@{$provider}".config('app.domain'));
        }

        return $user;
    }
}
```

To turn on this feature add the following to applications `socialstream.php` config file:

```php
Features::generateMissingEmails(),
```

# Provider Avatars

{% hint style="warning" %}
This feature requires you to have the [Profile Photos](https://jetstream.laravel.com/2.x/features/profile-management.html#profile-photos) feature enabled in Laravel Jetstream
{% endhint %}

The `Feature::providerAvatars()` feature instructs Socialstream to store the value of the `$avatar` property returned with the user data from Socialite after successfully authenticating with a provider.

Socialstream will download the image from the URL and use Jetstreams underlying Profile Photo's feature implementation so store the image in your specified disk. Please consult the [Jetstream documentation](https://jetstream.laravel.com/2.x/features/profile-management.html#managing-profile-photos) for more information on configuring this feature.

To turn on this feature add the following to applications `socialstream.php` config file:

```php
Features::providerAvatars()
```

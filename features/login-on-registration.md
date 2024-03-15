# Login on Registration (deprecated)

If a user has already registered with a particular email address, and the OAuth account they attempt to register with returns the same email, the provider will be linked to the existing user and they will be logged in.

{% hint style="warning" %}
This feature can allow compromised social accounts unsolicited access to your users accounts.
{% endhint %}

To turn on this feature add the following to applications `socialstream.php` config file:

```php
Features::loginOnRegistration()
```

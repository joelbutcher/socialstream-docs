# Auth Existing Unlinked Users

This feature isn't new to Socialstream, but is a renamed version of the now deprecated ["Login on Registration"](login-on-registration.md) feature.  <mark style="background-color:orange;">Please update your application to use this feature instead</mark>

Enable this feature if you wish to allow users to link a new provider to an existing user in your application without them being authenticated.

{% hint style="warning" %}
This feature can allow compromised social accounts unsolicited access to your users accounts.
{% endhint %}

To enable this feature, add the following to the `features` array of your `socialstream` config file:

```php
Features::authExistingUnlinkedUsers()
```

# Register from Login

This feature enables the capability to register a new user when a user attempts to authenticate via the '/login' route.

To turn on this feature add the following to applications `socialstream.php` config file:

```php
Features::createAccountOnFirstLogin()
```

# Remember Session

This feature instructs Laravel's authentication layer to keep the user authenticated indefinitely, or until they manually logout. This feature is the equivalent of having the "remember me" checkbox on a login form checked.

In order for this feature to work, your `users` table must include the string `remember_token` column, which will be used to store the "remember me" token. The `users` table migration included with new Laravel applications already includes this column.

If you have this feature enabled, you may use the `viaRemember` method to determine if the currently authenticated user was authenticated using the "remember me" cookie:

```php
use Illuminate\Support\Facades\Auth;
 
if (Auth::viaRemember()) {
    // ...
}
```

To turn on this feature add the following to applications `socialstream.php` config file:

```php
Features::rememberSession()
```

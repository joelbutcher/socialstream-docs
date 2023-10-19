# Handling Invalid State

If you've ever used Socialite in the past, you may be familiar with the `InvalidStateException` you get if you try to refresh the callback route. When you first generate the redirect for a provider, Socialite will generate a random string 40 characters in length and store this in the session. When the user returns and `Socialite::driver(...)->user()` is called, this "pulls" the state stored in the session via `$request->session()->pull()`, and compares this value with a `state` parameter on the callback request.

Socialte will forget the state (remove it from the session) as soon as the value is retrieved from the session (see [here](https://github.com/laravel/framework/blob/a47df681bf4b72cb11e38f7ee7c285406ca934e0/src/Illuminate/Session/Store.php#L306-L309)). As such, subsequent attempts to the callback route will fail. To get around this, you can make both the redirect and callback requests "stateless". However, not all providers support this. You may, therefore, decide to alter this functionality on a per-provider basis. Socialite makes this easy for you, just update`app/Actions/Socialstream/HandleInvalidState.php` with logic specific to your use case.
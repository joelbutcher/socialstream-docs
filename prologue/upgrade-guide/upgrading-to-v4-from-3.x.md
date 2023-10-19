# Upgrading to v4 from 3.x

{% hint style="info" %}
This upgrade guide only discusses upgrading Socialstream 4.x and assumes you are upgrading from an **unaltered 3.x install**. Upgrading Jetstream, Livewire, Inertia, Vue or Tailwind CSS is out of scope for this documentation. Please consult the upgrade guides for each of these packages instead.
{% endhint %}

## Overview <a href="#user-content-changes" id="user-content-changes"></a>

* [Changes](upgrading-to-v4-from-3.x.md#user-content-changes-1)
  * [Native Type Declarations](upgrading-to-v4-from-3.x.md#user-content-changes-2)
  * [User Profile Photo](upgrading-to-v4-from-3.x.md#user-profile-photo)
  * [Inertia Props](upgrading-to-v4-from-3.x.md#changes-to-jetstream-props-in-inertia)

***

## Changes <a href="#user-content-changes" id="user-content-changes"></a>

### **Native Type declarations** <a href="#user-content-changes" id="user-content-changes"></a>

{% hint style="danger" %}
Impact: **High**
{% endhint %}

Version 4.x updates the interfaces and published `php` files to use native type declarations. Please ensure the following files have been updated to match their interface counterparts:

```
CreateConnectedAccount.php
CreateUserFromProvider.php
HandleInvalidState.php
ResolveSocialiteUser.php
SetUserPassword.php
UpdateConnectedAccount.php
```

### **User Profile Photo**

{% hint style="danger" %}
Impact: **High**
{% endhint %}

If you have included the `HasProfilePhoto` trait in your user model, please update your model to the following:

```diff
    use HasProfilePhoto {
-       getProfilePhotoUrlAttribute as getPhotoUrl;
+       HasProfilePhoto::profilePhotoUrl as getPhotoUrl;
    }
```

And replace the `getProfilePhotoUrlAttribute` method in the model with:

```php
/**
 * Get the URL to the user's profile photo.
 */
public function profilePhotoUrl(): Attribute
{
    return filter_var($this->profile_photo_path, FILTER_VALIDATE_URL)
        ? Attribute::get(fn () => $this->profile_photo_path)
        : $this->getPhotoUrl();
}
```

### **Inertia Props**

{% hint style="danger" %}
Impact: **High**
{% endhint %}

If you're using Inertia, please update your `Profile/Show.vue` file to the following:

```diff
-29:                    <UpdateProfileInformationForm :user="$page.props.user" />
+29:                    <UpdateProfileInformationForm :user="$page.props.auth.user" />
```

# Installation

{% hint style="warning" %}
Socialstream is primarily intended to be used within **NEW** Laravel applications with either Laravel Jetstream, Laravel Breeze, or Filament Admin Panels.

If you don't require any of these starter-kits, you may [install Socialstream as a standalone package](../guides/standalone-installation.md).&#x20;
{% endhint %}

You may install Socialstream into your project via composer:

```sh
composer create-project laravel/laravel example-app

cd example-app

composer require joelbutcher/socialstream
```

After installing Socialstream, you may execute the `socialstream:install` Artisan command. This command will guide you through various stacks and options to configure the installation according to your application's requirements.

The first of these configurations is deciding which starter kit to use. Out of the box, Socialstream integrates directly into Laravel Jetstream, Laravel Breeze, or Filament. The benefits of each of these starter kits, and their various stacks, is listed below:

### Laravel Jetstream

Jetstream is a comprehensive starter kit for Laravel artisans, built on top of [Laravel Sactum](https://laravel.com/docs/10.x/sanctum) and [Laravel Fortify](https://laravel.com/docs/10.x/fortify), Jetstream adds two-factor authentication, session management, API token management and Teams functionality to your application. Jetstream is available in the following stacks:

* `inertia` (Vue with Inertia)
* `livewire` (Livewire)

Install Socialstream for Laravel Jetstream:

```sh
php artisan socialstream:install jetstream inertia

php artisan socialstream:install jetstream livewire
```

For more information, please refer to the [official documentation](https://laravel.com/docs/10.x/starter-kits#laravel-breeze) for Laravel Breeze.

Both Laravel Breeze and Laravel Jetstream are designed and crafted using [TailwindCSS](https://tailwindcss.com)

### Laravel Breeze

Laravel Breeze offers a minimal, simple implementation of Laravel's authentication mechanics, including login, registration, password reset and email verification. It also provides you with a simple profile page where your users can update their name, email and password information, as well as delete their account. Laravel Breeze is available in the following "stacks":

* `blade` (Blade with Alpine)
* `livewire` (Livewire (Volt) with Alpine)
* `livewire-functional` (Livewire (functional) with Alpine)
* `react` (React with Inertia)
* `vue` (Vue with Inertia)

Install Socialstream for Laravel Breeze:

```shell
php artisan socialstream:install breeze blade

php artisan socialstream:install breeze livewire

php artisan socialstream:install breeze livewire-functional

php artisan socialstream:install breeze react

php artisan socialstream:install breeze vue
```

For more information, please refer to the [official documentation](https://laravel.com/docs/10.x/starter-kits#laravel-breeze) for Laravel Breeze.

#### Dark mode

If you would like to include "dark mode" support when scaffolding your application's frontend, provide the `--dark` directive when executing the socialstream`:install` command:

```bash
php artisan socialstream:install breeze livewire --dark
```

#### Typescript

Laravel Breeze comes with support for typescript with React or Vue. To include typescript support into your applications frontend scaffolding, add the `--typescript` directive when executing the `socialstream:install` command:

```bash
php artisan socialstream:install breeze react --typescript

php artisan socialstream:install breeze vue --typescript
```

#### SSR

Laravel Breeze and Laravel Jetstream both ship with support for Server Side Rendering via [Laravel Vite](https://laravel.com/docs/10.x/vite#ssr) & [Inertia](https://inertiajs.com/server-side-rendering). To install SSR support, you may do so by adding the `--ssr` directive when executing the `socialstream:install` command:

```bash
php artisan socialstream:install breeze react --ssr

php artisan socialstream:install breeze vue --ssr

php artisan socialstream:install jetstream inertia --ssr
```

### Filament (beta)

{% hint style="warning" %}
Filament support is currently in beta. It is not recommended that you run this in a production environment as you may experience issues.
{% endhint %}

Filament is a TALL stack package that allows Laravel developers to quickly scaffold a UI using easy-to-configure components without having to worry about building components. Socialstream will integrate easily into Filament admin panel by publishing the required Livewire files and rendering them after the login panel

Install Socialstream for Filament

```bash
php artisan socialstream:install filament
```

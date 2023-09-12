# Installation

{% hint style="danger" %}
**New Applications Only**

Like Laravel Jetstream, Socialstream should only be used within new Laravel applications. Attempting to install Socialstream (or Jetstream) into existing Laravel applications may cause unexpected behaviours and issues.
{% endhint %}

## Installing Socialstream

To get started using Socialstream in your application, you should `composer require` the package into a **new** Laravel project:

```
composer require joelbutcher/socialstream
```

Once you have installed Socialstream, you should run the `socialstream:install` Artisan command. This command will install Laravel Jetstream for you, if you have not done so already. As a result, it accepts a number of _(optional)_ arguments that allow you to configure Jetstream and Socialstream according to your applications needs:

* **Stack**: The `--stack` argument allows you to specify your desired frontend stack (`livewire` or `inertia`). If you don't provide this arguement, the `install` command will prompt you to make a choice.
* **Teams**: The `--teams` argument indicates if teams support should be installed when installing Laravel Jetstream.
* **API**: The `--api` argument indicates if API support should be installed when installing Laravel Jetstream.
* **Verification**: The `--verification` argument indicates if email verification support should be installed when installing Laravel Jetstream.
* **Pest**: The `--pest` argument indicates if Pest should be installed when installing Laravel Jetstream.
* **SSR**: The `--ssr` argument indicates if Inertia SSR support should be installed when installing Laravel Jetstream.
* **Composer**: The `--composer=` argument allows you to specify the absolute path to the Composer binary which should be used for installing packages.

#### Install Socialstream with Livewire

```bash
php artisan socialstream:install --stack=livewire
php artisan socialstream:install --stack=livewire --teams
```

#### Install Socialstream with Inertia

```bash
php artisan socialstream:install --stack=inertia
php artisan socialstream:install --stack=inertia --teams
```

Inertia may also be installed with SSR support:

```bash
php artisan socialstream:install --stack=inertia --ssr
```

You can read more about running the SSR server in the [Laravel Vite plugin](https://laravel.com/docs/vite#ssr) and [Inertia](https://inertiajs.com/server-side-rendering) documentation.

### Finishing Up

To wrap up your installation, Socialstream will run the `npm install` and `npm run build` commands required to install your frontend stacks dependencies and build your application. After this you need to manually run your migrations to ensure your application has the tables required for Socialstream to work:

```bash
php artisan migrate
```

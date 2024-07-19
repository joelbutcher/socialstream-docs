---
description: >-
  THE social authentication solution for Laravel Breeze, Jetstream and Filament
  Admin Panels.
cover: .gitbook/assets/4944bd00-482a-11eb-990e-7b72beae4e64.png
coverY: 0
layout:
  cover:
    visible: true
    size: hero
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Introduction

## What is Socialstream?

Socialstream is a third-party Laravel package, delivering an all encompassing solution to adding social OAuth integration to your Laravel Jetstream, Laravel Breeze, or Filament Admin Panel based Laravel application.

With Socialstream, you no longer need to spend hours designing the business logic behind social OAuth for your users, simply install the package for your desired starter kit and supported stack, run your migrations, set your supported providers in `./config/socialstream.php` and configure their client details in `./config/services.php`. Finally, build your frontend and set the desired features (such as [global login](features/global-login.md), or [generate missing emails](features/missing-emails.md)) and off you go!

When installed, it provides a UI adaptation on top of all three technologies to provide support for all the first-party social providers supported by Laravel Socialite out-of-the-box. For clarity, Socialstream explicitly provides support for:

* Bitbucket
* Facebook
* Github
* Gitlab
* Google
* LinkedIn
* LinkedIn (via OpenId)
* Slack
* Twitter

{% hint style="info" %}
Further integration with other providers (such as Apple, Microsoft, Reddit and many more) can be added by using the [Socialite Providers](https://github.com/socialiteproviders) package. A guide on how to do this can be found [here](guides/socialite-providers.md).
{% endhint %}

When you first install Socialstream into your application, via the `socialstream:install` artisan command, you will be guided through the installation specific for your application. You will be asked a series of questions, such as which starter kit you will be using and the associated technology stack that powers it (e.g. Laravel Jetstream and Livewire, or Laravel Breeze and Volt's functional API).

{% hint style="warning" %}
Socialstream is designed to work in tandem with either of Laravel's starter kits (Breeze and Jetstream) and Filament Admin Panel, and as such, Socialstream should be installed into a **NEW** Laravel applications only. **It is not recommended to install Socialstream into an existing application**
{% endhint %}

## How does it work?

Under the hood, Socialstream harnesses the power of Laravel Socialite to power each OAuth integration. When you add Socialstream to your project (via `composer require`) it will automatically pull in the latest compatible version of Laravel Socialite for you, so you don't have to remember to install it yourself!

Socialstream exposes two wildcard routes. The first is `/oauth/{provider}`  (`oauth.redirect`). This route should be called by any "Log in Via \[X]" button on your site or in your application and will redirect your user(s) to the providers OAuth flow.

The second route is is `/oauth/{provider}/callback`  (`oauth.callback`). This is the callback URL that each provider will send your user(s) once they have completed their OAuth authorization.

{% hint style="info" %}
You will not need to add these routes to your routes files, nor will you need to publish any routes files for them to work.
{% endhint %}

## What does it provide?

### Frontend

Depending on the starter kit and stack you are using, Socialstream will configure the published view files for Laravel Jetstream or Laravel Breeze to add a section beneath the login / registration panels that users can click for each of your applications supported browsers. The frontend logic can be found in the following published files:

* Laravel Jetstream (Livewire) – `socialstream.blade.php`
* Laravel Jetstream (Inertia) – `Socialstream.vue`
* Laravel Breeze (blade, livewire, livewire-functional) – `socialstream.blade.php`
* Laravel Breeze (vue, vue typescript) – `Socialstream.vue`
* Laravel Breeze (react) – `Socialstream.jsx`,&#x20;
* Laravel Breeze (react typescript) – `Socialstream.jsx`

### Backend

Installing Socialstream will publish four different things for the backend.

#### Config

The first is a config file, this will be published to `./config/socialstream.php`. This config file controls a number of things from the middleware used to wrap Socialstreams routes, to the enabled providers, the enabled features and, in the case of Filament, the auth guard and component used to render Socialstream's UI.

#### Migrations & Models

The second is some migrations and their relevant models. The migrations are to adjust the users table so the password can be set to `null` and another migration to create the `connected_accounts` table (one user can have many linked accounts). The models are `User` and `ConnectedAccount`.&#x20;

#### Actions

The third is a bunch of "Action" classes (located at `/app/Actions/Socialstream`) used by Socialstream to execute various features. The published actions include:

* `CreateConnectedAccount`
* `CreateNewUserFromProvider`
* `GenerateRedirectForProvider`
* `HandleInvalidState`
* `ResolveSocialiteUser`
* `SetUserPassword`
* `UpdateConnectedAccount`

#### Service Provider

The fourth thing published for the backend, is the `SocialstreamServiceProvider`. This configures a bunch of sensible defaults for Socialstream's actions, and sets the base models used by Socialstream to the ones that are published along with the migrations.


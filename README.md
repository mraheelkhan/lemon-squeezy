# Lemon Squeezy for Laravel

<a href="https://github.com/lmsqueezy/laravel/actions">
    <img src="https://github.com/lmsqueezy/laravel/actions/workflows/tests.yml/badge.svg" alt="Tests">
</a>
<a href="https://github.com/lmsqueezy/laravel/actions/workflows/coding-standards.yml">
    <img src="https://github.com/lmsqueezy/laravel/actions/workflows/coding-standards.yml/badge.svg" alt="Coding Standards" />
</a>
<a href="https://packagist.org/packages/lemonsqueezy/laravel">
    <img src="https://img.shields.io/packagist/v/lemonsqueezy/laravel" alt="Latest Stable Version">
</a>
<a href="https://packagist.org/packages/lemonsqueezy/laravel">
    <img src="https://img.shields.io/packagist/dt/lemonsqueezy/laravel" alt="Total Downloads">
</a>

A package to easily integrate your [Laravel](https://laravel.com) application with Lemon Squeezy. It takes the pain out of setting up a checkout experience. Easily set up payments for your products or let your customers subscribe to your product plans. Handle grace periods, pause subscriptions, or offer free trials.

This package drew inspiration from [Cashier](https://github.com/laravel/cashier-stripe) which was created by [Taylor Otwell](https://twitter.com/taylorotwell).

Lemon Squeezy for Laravel is maintained by [Dries Vints](https://twitter.com/driesvints). Any sponsorship to [help fund development on this package](https://github.com/sponsors/driesvints) is greatly appreciated ❤️

We also recommend to read the Lemon Squeezy [docs](https://docs.lemonsqueezy.com/help) and [developer guide](https://docs.lemonsqueezy.com/guides/developer-guide).

> This package is a work in progress. As long as there is no v1.0.0, breaking changes may occur in v0.x releases. No upgrade path between v0.x versions will be provided.

## Requirements

- PHP 8.1 or higher
- Laravel 10.0 or higher

## Installation

There are a few steps you'll need to take to install the package:

1. Requiring the package through Composer
2. Creating an API Key
3. Connecting your store
4. Configuring the Billable Model
5. Connecting to Lemon JS
6. Setting up webhooks

We'll go over each of these below.

### Composer

Install the package with composer:

```bash
composer require lemonsqueezy/laravel
```

### API Key

Next, configure your API key. Create a new test key in [the Lemon Squeezy dashboard](https://app.lemonsqueezy.com/settings/api) and paste them in your `.env` file as shown below:

```ini
LEMON_SQUEEZY_API_KEY=your-lemon-squeezy-api-key
```

> **Warning**  
> Mever commit your API keys to Git. Also, always use a test key for development and never use a production key locally.

### Store URL

Your store url will be used when creating checkouts for your products. Go to [your Lemon Squeezy general settings](https://app.lemonsqueezy.com/settings/general) and copy the Store URL subdomain (the part before `.lemonsqueezy.com`) into the env value below:

```ini
LEMON_SQUEEZY_STORE=your-lemon-squeezy-subdomain
```

### Billable Model

To make sure we can actually create checkouts for our customers, we'll need to configure a model to be our "billable" model. This is typical the `User` model of your app. To do this, import and use the `Billable` trait on your model:

```php
use LemonSqueezy\Laravel\Billable;
 
class User extends Authenticatable
{
    use Billable;
}
```

Now your user model will have access to methods from our package to create checkouts in Lemon Squeezy for your products.

### Lemon JS

Lemon Squeezy uses its own JavaScript library to initiate its checkout widget. We can make use of it by loading it through the Blade directive in the `head` section of our app, right before the closing `</head>` tag.

```blade
<head>
    ...
 
    @lemonJS
</head>
```

### Webhooks

Finally, make sure to set up incoming webhooks. This is both needed in development as in production. Go to [your Lemon Squeezy's webhook settings](https://app.lemonsqueezy.com/settings/webhooks) and point the url to your exposed local app. You can use [Ngrok](https://ngrok.com/), [Expose](https://github.com/beyondcode/expose) or another tool of your preference for this.

Make sure to select all event types. The path you should point to is `/lemon-squeezy/webhook` by default. **We also very much recommend to [verify webhook signatures](#verifying-webhook-signatures).**

#### Webhooks & CSRF Protection

Incoming webhooks should not be affected by [CSRF protection](https://laravel.com/docs/csrf). To prevent this, add your webhook path to the except list of your `App\Http\Middleware\VerifyCsrfToken` middleware:

```php
protected $except = [
    'lemon-squeezy/*',
];
```

## Upgrading

Please review [our upgrade guide](./UPGRADE.md) when upgrading to a new version.

## Configuration

The package offers various way to configure your experience with integrating with Lemon Squeezy.

### Verifying Webhook Signatures

In order to make sure that incoming webhooks are actually from Lemon Squeezy, we can configure a signing secret for them. Go to your webhook settings in the Lemon Squeezy dashboard, click on the webhook of your app and copy the signing secret into the environment variable below:

```ini
LEMON_SQUEEZY_SIGNING_SECRET=your-webhook-signing-secret
```

Any incoming webhook will now first be verified before being executed.

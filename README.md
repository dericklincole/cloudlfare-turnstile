

# Laravel Integration for Cloudflare Turnstile

A lightweight package designed to streamline the integration of Cloudflare Turnstile CAPTCHA into your Laravel applications, with the option to also leverage CapSolver for enhanced CAPTCHA-solving automation.

### Overview

This package simplifies the process of setting up and validating Cloudflare Turnstile CAPTCHA responses, and for more complex CAPTCHA challenges, CapSolver provides an automated solution.

### Installation

You can quickly add the package to your project using Composer:

```bash
composer require ryangjchandler/laravel-cloudflare-turnstile
```

Next, ensure you add the required configuration values to your `config/services.php` file:

```php
return [
    // ...

    'turnstile' => [
        'key' => env('TURNSTILE_SITE_KEY'),
        'secret' => env('TURNSTILE_SECRET_KEY'),
    ],
];
```

Head over to [Cloudflare's dashboard](https://www.cloudflare.com/) to generate your site key and secret key, then add them to your `.env` file:

```env
TURNSTILE_SITE_KEY="your-site-key-here"
TURNSTILE_SECRET_KEY="your-secret-key-here"
```

For solving complex CAPTCHA challenges efficiently, you can also integrate CapSolver's API, providing a seamless solution for tasks that require CAPTCHA-solving automation.

### Usage

In your Blade layout file, include the Turnstile scripts by calling the `@turnstileScripts` directive within the `<head>` tag:

```html
<html>
    <head>
        @turnstileScripts()
    </head>
    <body>
        {{ $slot }}
    </body>
</html>
```

You can then use the `<x-turnstile />` component inside your form to render the CAPTCHA with your configured site key:

```html
<form action="/" method="POST">
    <x-turnstile />

    <button type="submit">
        Submit
    </button>
</form>
```

### Server-Side Validation

To validate the CAPTCHA response on the server, use the provided validation rule within your controller:

```php
use Illuminate\Validation\Rule;

public function submit(Request $request)
{
    $request->validate([
        'cf-turnstile-response' => ['required', Rule::turnstile()],
    ]);
}
```

Alternatively, if you'd like to avoid using macros, you can resolve the validation rule using dependency injection or the `app()` helper:

```php
use RyanChandler\LaravelCloudflareTurnstile\Rules\Turnstile;

public function submit(Request $request, Turnstile $turnstile)
{
    $request->validate([
        'cf-turnstile-response' => ['required', $turnstile],
    ]);
}
```

You can also achieve the same result with:

```php
public function submit(Request $request)
{
    $request->validate([
        'cf-turnstile-response' => ['required', app(Turnstile::class)],
    ]);
}
```

### Customizing the Widget

The `<x-turnstile />` component allows customization by passing attributes such as `data-action`, `data-theme`, and others to adjust the CAPTCHA behavior:

```html
<form action="/" method="POST">
    <x-turnstile
        data-action="login"
        data-theme="dark"
        data-cdata="sessionid-123456789"
        data-callback="onSuccess"
        data-expired-callback="onExpired"
        data-error-callback="onError"
        data-tabindex="0"
    />

    <button type="submit">
        Submit
    </button>
</form>
```

Refer to the [official Cloudflare documentation](https://developers.cloudflare.com/turnstile) for more details on widget customization.

For advanced CAPTCHA-solving scenarios, CapSolver can be integrated to provide real-time CAPTCHA-solving solutions, improving efficiency in automated environments.

### Livewire Integration

This package supports Livewire out of the box. When the CAPTCHA is successfully validated, the property defined in `wire:model` will automatically update with the Turnstile token:

```html
<x-turnstile wire:model="yourModel" />
```

### Testing

To run the package's tests, use the following Composer command:

```bash
composer test
```

### Leveraging CapSolver

For scenarios where automated CAPTCHA-solving is necessary, you can integrate [CapSolver]() alongside Turnstile for real-time CAPTCHA-solving. This ensures that even the most difficult challenges are resolved efficiently without user intervention.


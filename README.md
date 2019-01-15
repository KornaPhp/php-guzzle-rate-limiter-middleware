# A rate limiter middleware for Guzzle

[![Latest Version on Packagist](https://img.shields.io/packagist/v/spatie/guzzle-rate-limit.svg?style=flat-square)](https://packagist.org/packages/spatie/:package_name)
[![Build Status](https://img.shields.io/travis/spatie/guzzle-rate-limit/master.svg?style=flat-square)](https://travis-ci.org/spatie/:package_name)
[![Quality Score](https://img.shields.io/scrutinizer/g/spatie/guzzle-rate-limit.svg?style=flat-square)](https://scrutinizer-ci.com/g/spatie/:package_name)
[![Total Downloads](https://img.shields.io/packagist/dt/spatie/guzzle-rate-limit.svg?style=flat-square)](https://packagist.org/packages/spatie/:package_name)

A rate limiter middleware for Guzzle. Here's what you need to know:

- Specify a maximum amount of requests per minute or per second
- When the limit is reached, the process will `sleep` until the request can be made
- Implement your own driver to persist the rate limiter's request store. This is necessary if the rate limiter needs to work across separate processes, the package ships with an `InMemoryStore`.

## Installation

You can install the package via composer:

```bash
composer require spatie/guzzle-rate-limiter
```

## Usage

Create a [Guzzle middleware stack](http://docs.guzzlephp.org/en/stable/handlers-and-middleware.html) and register it on the client.

```php
use GuzzleHttp\Client;
use GuzzleHttp\HandlerStack;
use Spatie\GuzzleRateLimiter\RateLimiterMiddleware;

$stack = HandlerStack::create(
    RateLimiterMiddleware::perSecond(3)
);

$client = new Client([
    'handler' => $stack,
]);
```

You can create a rate limiter to limit per second or per minute.

```php
RateLimit::perSecond(3); // Max. 3 requests per second

RateLimit::perMinute(5); // Max. 5 requests per minute
```

## Custom stores

By default, the rate limiter works in memory. This means that if you have a second PHP process (or Guzzle client) consuming the same API, you'd still possibly hit the rate limit. To work around this issue, the rate limiter's state should be persisted to a cache. Implement the `Store` interface with your own cache, and pass the store to the rate limiter.

```php
use MyApp\RateLimiterStore;
use Spatie\GuzzleRateLimiter\RateLimit;

RateLimit::perSecond(3, new RateLimiterStore());
```

A Laravel example of a custom `Store`:

```php
<?php

namespace MyApp;

use Spatie\GuzzleRateLimiter\Store;
use Illuminate\Support\Facades\Cache;

class RateLimiterStore implements Store
{
    public function get(): array
    {
        return Cache::get('rate-limiter', []);
    }

    public function push(int $timestamp)
    {
        Cache::put('rate-limiter', array_merge($this->get(), $timestamp));
    }
}
```

### Testing

``` bash
composer test
```

### Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information on what has changed recently.

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

### Security

If you discover any security related issues, please email freek@spatie.be instead of using the issue tracker.

## Postcardware

You're free to use this package, but if it makes it to your production environment we highly appreciate you sending us a postcard from your hometown, mentioning which of our package(s) you are using.

Our address is: Spatie, Samberstraat 69D, 2060 Antwerp, Belgium.

We publish all received postcards [on our company website](https://spatie.be/en/opensource/postcards).

## Credits

- [Sebastian De Deyne](https://github.com/sebastiandedeyne)
- [All Contributors](../../contributors)

## Support us

Spatie is a webdesign agency based in Antwerp, Belgium. You'll find an overview of all our open source projects [on our website](https://spatie.be/opensource).

Does your business depend on our contributions? Reach out and support us on [Patreon](https://www.patreon.com/spatie).
All pledges will be dedicated to allocating workforce on maintenance and new awesome stuff.

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
# Vend SDK (a PHP client)

[![Latest Version on Packagist](https://img.shields.io/packagist/v/simplesquid/vend-sdk.svg?style=flat-square)](https://packagist.org/packages/simplesquid/vend-sdk)
[![Build Status](https://img.shields.io/travis/simplesquid/vend-sdk/master.svg?style=flat-square)](https://travis-ci.org/simplesquid/vend-sdk)
[![MIT License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE.md)
[![Total Downloads](https://img.shields.io/packagist/dt/simplesquid/vend-sdk.svg?style=flat-square)](https://packagist.org/packages/simplesquid/vend-sdk)

An unofficial PHP SDK for the [Vend POS API](https://docs.vendhq.com/).

## Installation

You can install this package via composer:

```bash
composer require simplesquid/vend-sdk
```

## Setup

To setup, get the instance of the SDK and set the desired user agent and settings:

```php
$vend = Vend::getInstance();

/* Optional configuration. */
$vend->userAgent('Vend SDK')
     ->requestTimeout(2)
     ->confirmationTimeout(30);
```

It is advisable that you use the client object as a singleton, however, there is no explicit restriction for this.

## Usage

This outlines a typical sequence of instructions, but is not a complete list of all the functionality of the SDK. Please review the code for more advanced usages. It is also suggested you read the [Vend API documentation](https://docs.vendhq.com/).

Alternatively, you can see a working authorisation implementation in our [Laravel Vend SDK package](https://github.com/simplesquid/laravel-vend-sdk).

### Authorisation

You have two options for authorisation with the Vend API, a Personal Access Token or OAuth 2.0.

#### Personal Access Token

When using the Personal Access Token, simply setup the Vend client like so:

```php
$vend->domainPrefix($domain_prefix)
     ->personalAccessToken($access_token);
```

#### OAuth 2.0

Setup the Vend client with the OAuth identifiers:

```php
$vend->clientId($client_id)
     ->clientSecret($client_secret)
     ->redirectUri($redirect);
```

##### Initial authorisation procedure

Send the user to the OAuth URL generated by:

```php
$vend->getAuthorisationUrl($previous_url);
```

Use the domain prefix and authorisation code returned from the OAuth process:

```php
/** @var \SimpleSquid\Vend\Resources\OneDotZero\Token */
$token = $vend->domainPrefix($domain_prefix)
              ->oAuthAuthorisationCode($code);
```

Make sure you store the returned Token object and the domain prefix.

##### Subsequent setup procedure

Once you have received the initial authorisation, you may setup the Vend client using the Token object received during the authorisation process:

```php
$vend->authorisationToken($token);
```

If a request throws a `\SimpleSquid\Vend\Exceptions\TokenExpiredException`, you can refresh the token like so:

```php
/** @var \SimpleSquid\Vend\Resources\OneDotZero\Token */
$token = $vend->refreshOAuthAuthorisationToken();
```

Again, make sure you store the returned Token object.

### Requests

To see all the available requests, take a look at traits located in the `Actions` folder. As an example, Products can be managed using the following requests:

```php
/**
 * List products.
 * Returns a paginated list of products.
 *
 * @param  int|null   $page_size  The maximum number of items to be returned in the response.
 * @param  int|null   $after      The lower limit for the version numbers to be included in the response.
 * @param  int|null   $before     The upper limit for the version numbers to be included in the response.
 * @param  bool|null  $deleted    Indicates whether deleted items should be included in the response.
 *
 * @return \SimpleSquid\Vend\Resources\TwoDotZero\ProductCollection
 */
$products = $vend->product->get($page_size, $after, $before, $deleted);
```

```php
/**
 * Get a single product.
 * Returns a single product object with a given ID.
 *
 * @param  string  $id  Valid product ID.
 *
 * @return \SimpleSquid\Vend\Resources\TwoDotZero\Product
 */
$product = $vend->product->find($id);
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

If you discover any security related issues, please email security@simplesquid.co.za instead of using the issue tracker.

## Credits

- [Matthew Poulter](https://github.com/mdpoulter)
- [All Contributors](../../contributors)

Package skeleton based on [spatie/skeleton-php](https://github.com/spatie/skeleton-php).

## About us

SimpleSquid is a small web development and design company based in Cape Town, South Africa.

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.

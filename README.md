# ThinTransitClient

[![Latest Version on Packagist][ico-version]][link-packagist]
[![Software License][ico-license]](LICENSE.md)
[![Build Status][ico-travis]][link-travis]
[![StyleCI][ico-styleci]][link-styleci]
[![Total Downloads][ico-downloads]][link-packagist]

   * [ThinTransitClient](#thintransitclient)
      * [What this is](#what-this-is)
      * [What this isn't](#what-this-isnt)
   * [Install](#install)
   * [Usage](#usage)
      * [Setup](#setup)
      * [Encrypting](#encrypting)
      * [Decrypting](#decrypting)
   * [Testing](#testing)
   * [Contributing](#contributing)
   * [Credits](#credits)
   * [License](#license)

## What this is

A very thin PHP wrapper around Hashicorp Vault's [Transit Engine](https://www.vaultproject.io/docs/secrets/transit/index.html)

## What this isn't

Unfortunatly, this isn't a full fledged vault client. When I started writing [LaraVault](https://github.com/Kidfund/LaraVault), [these clients](https://www.vaultproject.io/api/libraries.html#php) didn't exist yet. This client is the bare minimum need to communicate with Transit. Ideally, LaraVault would deprecate the need for this and use one of those clients

# Install

Via Composer

``` bash
$ composer require kidfund/thin-transit-client
```

# Usage

## Setup 

You'll need to store the address of your vault server and the currently available token somewhere. This is the token setup we use with LaraVault

```hcl
path "transit/decrypt/*" {
  capabilities = ["create", "update"]
}

path "transit/encrypt/*" {
  capabilities = ["create", "update"]
}
```

If we were using the TransitClient in a Laravel Service Providor, we could do something like this

```php
/**
 * @return TransitClient|null
 * @throws Exception
 */
protected function getTransitClient()
{
    $enabled = config('vault.enabled');

    if (!$enabled) {
        return null;
    }

    $vaultAddr = config('vault.addr');
    $vaultToken = config('vault.token');

    if ($vaultToken === null || $vaultToken === 'none') {
        throw new Exception('Vault token must be configured');
    }

    $client = new TransitClient($vaultAddr, $vaultToken);

    return $client;
}

/**
 * @return void
 */
public function register()
{
    $this->app->singleton(TransitClient::class, function () {
        return $this->getTransitClient();
    });
}
```

## Encrypting

```php
$encrypted = $client->encrypt($key, $plaintext);
```

You can also pass a context

```php
$encrypted = $client->encrypt($key, $plaintext, $context);
```

## Decrypting

```php
$plaintext = $client->decrypt($key, $cipherText,);
```

You can also pass a context

```php
$plaintext = $client->decrypt($key, $cipherText, $context);
```


# Testing

``` bash
$ ./vendor/bin/phpunit
```

# Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) and [CONDUCT](CONDUCT.md) for details.

# Credits

- [@timborder][link-author]

# License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.

[ico-version]: https://img.shields.io/packagist/v/kidfund/thin-transit-client.svg?style=flat-square
[ico-license]: https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square
[ico-travis]: https://img.shields.io/travis/kidfund/thin-transit-client/master.svg?style=flat-square
[ico-styleci]: https://github.styleci.io/repos/157907842/shield?branch=master
[ico-downloads]: https://img.shields.io/packagist/dt/kidfund/thin-transit-client.svg?style=flat-square

[link-packagist]: https://packagist.org/packages/kidfund/thin-transit-client
[link-travis]: https://travis-ci.org/kidfund/thin-transit-client
[link-author]: https://github.com/timbroder
[link-styleci]: https://github.styleci.io/repos/157907842

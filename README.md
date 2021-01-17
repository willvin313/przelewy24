# Przelewy24

A simple composer package that implements the przelewy24 payment gateway.

#### willvin's Przelewy24 payment processing library

[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE.md)
[![Codacy Badge](https://app.codacy.com/project/badge/Grade/8cfd72d55ca442e4b3d06fbbf3a7ce89)](https://www.codacy.com/manual/willvin313/przelewy24?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=willvin313/przelewy24&amp;utm_campaign=Badge_Grade)

For more information about the Przelewy24 API, take a look at the [manual](http://www.przelewy24.pl/files/cms/13/przelewy24_specification.pdf)

# Install

This gateway can be installed with [Composer](https://getcomposer.org/):

``` bash
$ composer require willvin/przelewy24
```

# Example

## Initiate Transaction

```php
require_once __DIR__ . '/vendor/autoload.php';

use willvin\Przelewy24\Gateway;

$gateway = new Gateway();

$gateway->initialize([
    'merchantId' => 'YOUR MERCHANT ID HERE',
    'posId'      => 'YOUR POS ID HERE',
    'crc'        => 'YOUR CRC KEY HERE',
    'testMode'   => true, // Sets P24 gateway to use sandbox url
]);

$gateway->setPostData([
    'p24_transactionId' => 'Transaction ID',
    'p24_amount'        => 'Amount',
    'p24_description'   => 'Description',
    'p24_email'         => 'Email',
    'p24_session_id'    => $gateway->getSessionId('Transaction ID'), //pass your transaction id here or use this $gateway->getSessionId($orderId) function to generate the id
    'p24_currency'      => 'Currency',
    'p24_country'       => 'Country',
    'p24_url_return'    => 'Url to redirect user, after payment',
    'p24_url_status'    => 'Transaction status callback url',
]);

$res = $gateway->trnRegister(); // ruturns a code like this D35CD73C0E-37C7B5-059083-E8EFB7FA96

if(!$res['error']){
    $res = $gateway->trnRequest($res['token']); // trigger the payment
} else {
    echo 'Transaction failed.';
}
```

## Verify Transaction

```php
require_once __DIR__ . '/vendor/autoload.php';

use willvin\Przelewy24\Gateway;

$gateway = new Gateway();

$rawData = file_get_contents('php://input');
parse_str($rawData, $p24Data);

if(!empty($p24Data)){
	$gateway->initialize([
		'merchantId' => 'YOUR MERCHANT ID HERE',
		'posId'      => 'YOUR POS ID HERE',
		'crc'        => 'YOUR CRC KEY HERE',
		'testMode'   => true, // Sets P24 gateway to use sandbox url
	]);

	$gateway->setPostData([
		'p24_session_id' => $p24Data['p24_session_id'],
		'p24_order_id' => $p24Data['p24_order_id'],
		'p24_amount' => $p24Data['p24_amount'],
		'p24_currency' => $p24Data['p24_currency']
	]);
	$res = $gateway->trnVerify(); // Use to verify the payment sent to your callback url
}
```

# Usage

## Initialization

```php
require_once __DIR__ . '/vendor/autoload.php';

use willvin\Przelewy24\Gateway;

$gateway = new Gateway();

$gateway->initialize([
    'merchantId' => 'YOUR MERCHANT ID HERE',
    'posId'      => 'YOUR POS ID HERE',
    'crc'        => 'YOUR CRC KEY HERE',
    'testMode'   => true, // Sets P24 gateway to use sandbox url
]);
```

OR

```php
require_once __DIR__ . '/vendor/autoload.php';

use willvin\Przelewy24\Gateway;

$gateway = new Gateway('YOUR MERCHANT ID HERE', 'YOUR POS ID HERE', 'YOUR CRC KEY HERE', true);
```

## Set Post Data

```php
$gateway->setPostData([
    'p24_transactionId' => 'Transaction ID',
    'p24_amount'        => 'Amount',
    'p24_description'   => 'Description',
    'p24_email'         => 'Email',
    'p24_session_id'    => 'Session ID',
    'p24_currency'      => 'Currency',
    'p24_country'       => 'Country',
    'p24_url_return'    => 'Url to redirect user, after payment',
    'p24_url_status'    => 'Transaction status callback url',
    'p24_channel'       => $gateway::P24_CHANNEL_ALL, // you have the following channels available P24_CHANNEL_CC, P24_CHANNEL_BANK_TRANSFERS, P24_CHANNEL_MANUAL_TRANSFER, P24_CHANNEL_ALL_METHODS_24_7, P24_CHANNEL_USE_PREPAYMENT, P24_CHANNEL_ALL
]);
```

OR

```php
$gateway->addValue('p24_transactionId', 'Transaction ID');
$gateway->addValue('p24_amount', 'Amount');
$gateway->addValue('p24_description', 'Description');
$gateway->addValue('p24_email', 'Email');
$gateway->addValue('p24_session_id', 'Session ID');
$gateway->addValue('p24_currency', 'Currency');
$gateway->addValue('p24_country', 'Country');
$gateway->addValue('p24_url_return', 'Url to redirect user, after payment';
$gateway->addValue('p24_url_status', 'Transaction status callback url';
$gateway->addValue('p24_channel', $gateway::P24_CHANNEL_ALL);// you have the following channels available P24_CHANNEL_CC, P24_CHANNEL_BANK_TRANSFERS, P24_CHANNEL_MANUAL_TRANSFER, P24_CHANNEL_ALL_METHODS_24_7, P24_CHANNEL_USE_PREPAYMENT, P24_CHANNEL_ALL
```

### Other available Post Data fields
```php
p24_address

p24_language

p24_client

p24_city

p24_order_id

p24_method

p24_time_limit

p24_shipping

p24_wait_for_result

p24_encoding

p24_transfer_label

p24_phone

p24_zip

#### Shopping cart details, where X is a number 1-100 (optional 2)

p24_name_X

p24_description_X

p24_quantity_X

p24_price_X

p24_number_X
```

For more details, you can read the [przelewy24 documentation](http://www.przelewy24.pl/eng/storage/app/media/pobierz/Instalacja/przelewy24_specification.pdf)

## Register transaction

```php
$res = $gateway->trnRegister(); // ruturns a token like this D35CD73C0E-37C7B5-059083-E8EFB7FA96
```

## Initiate Transaction

```php
$res = $gateway->trnRequest('Pass transaction token here'); // trigger the payment with your token
```

## Verify Transaction

```php
$res = $gateway->trnVerify(); // Use to verify the payment sent to your callback url
```

## Generate Session ID
```php
$gateway->getSessionId('Transaction ID');
```

# Support

If you believe you have found a bug, please report it using the [GitHub issue tracker](https://github.com/willvin313/przelewy24/issues),
or better yet, fork the library and submit a pull request.

*Click "Watch and Star" to get an email notification once an update is made to this repository. And contributions are also welcomed.*

You can support this project by donating to the following address.

<a href="https://www.buymeacoffee.com/GCWc1kS" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="51px" ></a><br>

<strong>PayPal:</strong> <a href="https://paypal.me/iamwillvin">Make a Donation</a>

<strong>Patreon:</strong> <a href="https://www.patreon.com/bePatron?u=25729924" data-patreon-widget-type="become-patron-button">Become a Patron!</a>

<strong>YouTube:</strong> <a href="https://www.youtube.com/channel/UCHCEiFFWcdcXhzgePJJtIZQ">Subscribe to willvin</a>

# Security

If you discover any security related issues, please email info@willvin.com instead of using the issue tracker.

# Credits

- [TicketSwap](https://github.com/ticketswap)
- [Przelewy24](https://przelewy24.pl)

# License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.

# phpnomad/email

[![Latest Version](https://img.shields.io/packagist/v/phpnomad/email.svg)](https://packagist.org/packages/phpnomad/email)
[![Total Downloads](https://img.shields.io/packagist/dt/phpnomad/email.svg)](https://packagist.org/packages/phpnomad/email)
[![PHP Version](https://img.shields.io/packagist/php-v/phpnomad/email.svg)](https://packagist.org/packages/phpnomad/email)
[![License](https://img.shields.io/packagist/l/phpnomad/email.svg)](https://packagist.org/packages/phpnomad/email)

`phpnomad/email` defines the contract PHPNomad applications use to send transactional mail. It contains a single strategy interface, `EmailStrategy`, and a failure exception. Concrete sending lives in a separate integration package, so your application code depends on the contract rather than on a specific mailer. Swap the strategy in your bootstrapper and the rest of your code stays the same. The built-in implementation is [`phpnomad/php-mail-integration`](https://packagist.org/packages/phpnomad/php-mail-integration), which sends through PHP's `mail()` function. Anything else (SMTP, a third-party API, a queued background worker) drops in by writing another class that implements the interface.

## Installation

```bash
composer require phpnomad/email
```

You will also need a strategy implementation. Install `phpnomad/php-mail-integration` for the default, or provide your own class that implements `EmailStrategy`.

## Quick Start

Inject `EmailStrategy` wherever you send mail, then call `send()` and handle `EmailSendFailedException`.

```php
<?php

namespace MyApp\Notifications;

use PHPNomad\Email\Exceptions\EmailSendFailedException;
use PHPNomad\Email\Interfaces\EmailStrategy;

class NotificationService
{
    public function __construct(protected EmailStrategy $emailStrategy)
    {
    }

    public function notify(string $recipient, string $body): void
    {
        try {
            $this->emailStrategy->send(
                [$recipient],
                'Your account is ready',
                $body,
                ['Content-Type: text/html; charset=UTF-8']
            );
        } catch (EmailSendFailedException $e) {
            // Log and continue, or rethrow depending on your policy.
        }
    }
}
```

## Overview

- `EmailStrategy` interface with a single `send(array $to, string $subject, string $body, array $headers): void` method
- `EmailSendFailedException` thrown by strategies when delivery fails
- A backend-agnostic contract your application depends on instead of a specific mailer
- Pairs with `phpnomad/php-mail-integration` for PHP's built-in `mail()` function, or any custom strategy you write

## Documentation

Full PHPNomad documentation is at [phpnomad.com](https://phpnomad.com).

## License

MIT. See [LICENSE.txt](LICENSE.txt).

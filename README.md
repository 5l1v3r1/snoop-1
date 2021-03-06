# Snoop [![Build status][travis-image]][travis-url] [![Version][version-image]][version-url] [![License][license-image]][license-url]

**:warning: This tool is currently broken as Rapportive recently changed their API so the trick is not available anymore.**

Snoop finds informations about an email address owner such as its name, social profiles, images and jobs.

## Installation

Add this line to your `composer.json` file:

```json
{
    "require": {
        "florianv/snoop": "~1.0"
    }
}
```

> Currently Guzzle 3 and 4 are supported HTTP clients, so you will need to require one of them:
>
> - `"guzzle/guzzle": "~3.0"`
> - `"guzzlehttp/guzzle": "~4.0"`

## Usage

> You can find a simple example using it [here](https://gist.github.com/florianv/ba3f2e46065543194a2b)

First, you need to create an adapter:

```php
// If you use Guzzle 3
$adapter = new \Snoop\Adapter\Guzzle3Adapter(new \Guzzle\Http\Client());

// If you use Guzzle 4
$adapter = new \Snoop\Adapter\Guzzle4Adapter(new \GuzzleHttp\Client());
```

Then you can create a Snoop instance and use it:

```php
// Create a Snoop instance
$snoop = new \Snoop\Snoop($adapter);

// Find the person with email 'john@doe.com'
$person = $snoop->find('john@doe.com');

$person->getFirstName(); // John
$person->getLastName(); // Doe
$person->getLocation(); // San Francisco Bay Area
$person->getHeadline(); // Developer at Google

foreach ($person->getImages() as $url) {}

foreach ($person->getJobs() as $job) {
    $job->getTitle(); // Developer
    $job->getCompanyName(); // Google
}

foreach ($person->getProfiles() as $profile) {
    $profile->getSiteName(); // Twitter
    $profile->getUsername(); // johndoe
}
```

By default, two requests will be issued: one to get a token and the other to get the informations,
but you can send them separately:

```php
// Fetch a token, maybe store it somewhere
$token = $snoop->fetchToken();

// Find the informations using the token
$person = $snoop->find('hello@world.com', $token);
```

### Exception handling

#### InvalidTokenException

The `InvalidTokenException` is thrown when the token is missing or invalid.

```php
try {
    $snoop->find('hello@world.com');
} catch (Snoop\Exception\InvalidTokenException $e) {
    // You might fetch a new token and retry
}
```

#### PersonNotFoundException

The `PersonNotFoundException` is thrown when there is no data associated with the email.

```php
try {
    $snoop->find('hello@world.com');
} catch (Snoop\Exception\PersonNotFoundException $e) {
    // This person was not found
}
```

## Notes

- The API limit is around 50 requests with the same IP every hour
- It uses a non-documented feature of the Rapportive API explained [here](http://jordan-wright.github.io/blog/2013/10/14/automated-social-engineering-recon-using-rapportive)
- There are other implementations using it [Python](https://github.com/jordan-wright/rapportive), [Ruby](https://github.com/the4dpatrick/find-any-email)

## License

[MIT](https://github.com/florianv/snoop/blob/master/LICENSE)

[travis-url]: https://travis-ci.org/florianv/snoop
[travis-image]: http://img.shields.io/travis/florianv/snoop.svg?style=flat-square

[license-url]: https://packagist.org/packages/florianv/snoop
[license-image]: http://img.shields.io/packagist/l/florianv/snoop.svg?style=flat-square

[version-url]: https://packagist.org/packages/florianv/snoop
[version-image]: http://img.shields.io/packagist/v/florianv/snoop.svg?style=flat-square

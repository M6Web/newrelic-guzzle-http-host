# newrelic-guzzle-http-host
A workaround to report Guzzle requests with correct Host Http header in NewRelic.

In some case, you may use a common proxy for several external calls to various hosts,
using the standard [`Host`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Host) to route your requests.
Unfortunatly, since all of these calls use the same uri, NewRelic will report them as external calls to the same target.

This small script provide a workaround to report the correct endpoint according to the HTTP `Host` header.

:warning: This library is a *workaround* relying on the current implementation of the newrelic extension!
Test it carefully before to use it with a new extension release.
See explanations section below for more details.

ℹ️An official feature request has been submitted to NewRelic team.

## Installation

```
composer require bedrockstreaming/newrelic-guzzle-http-host
```
That's all! The script is automatically imported thanks to [Composer autoloader](https://getcomposer.org/doc/04-schema.md#files).

ℹ️ You can also copy the content of [`newrelic_hook.php`](newrelic_hook.php) directly in your project.

## Explanations

The [newrelic extension](https://github.com/newrelic/newrelic-php-agent) [defines a Php function](https://github.com/newrelic/newrelic-php-agent/blob/3f93ee47f80703d46d8fccd53be7d6b80361a594/agent/lib_guzzle6.c#L433-L461),
a [Guzzle](https://github.com/guzzle/guzzle) middleware to report some metrics on each requests sent from Php.


Fortunately, the extension defines this function only if it doesn't already exist… then this workaround define this function before newrelic does 😅.
Original code is copied in this function, but we create a new http requests dedicated to NewRelic reporting, resolving the Http `HOST` header.
The original request used by Guzzle stays unchanged.


# 🌲 Timber - Log Better. Solve Problems Faster.

[![ISC License](https://img.shields.io/badge/license-ISC-ff69b4.svg)](LICENSE.md)
[![CircleCI](https://circleci.com/gh/timberio/timber-ruby.svg?style=shield&circle-token=:circle-token)](https://circleci.com/gh/timberio/timber-ruby/tree/master)
[![Coverage Status](https://coveralls.io/repos/github/timberio/timber-ruby/badge.svg?branch=master)](https://coveralls.io/github/timberio/timber-ruby?branch=master)
[![Code Climate](https://codeclimate.com/github/timberio/timber-ruby/badges/gpa.svg)](https://codeclimate.com/github/timberio/timber-ruby)
[![View docs](https://img.shields.io/badge/docs-viewdocs-blue.svg?style=flat-square "Viewdocs")](http://www.rubydoc.info/github/timberio/timber-ruby)

* [Timber website](https://timber.io)
* [Timber docs](https://timber.io/docs)
* [Library docs](https://hex.pm/packages/timber)
* [Support](mailto:support@timber.io)


## Overview

Timber automatically turns your raw text logs into rich JSON events that can be consumed by the
[Timber.io service](https://timber.io). Improving log data quality at the core, so you can cut
out the noise and solve problems faster.

For example, Timber turns this:

```
Sent 200 in 45.ms
```

Into this:

```
Sent 200 in 45.2ms @metadata {"dt": "2017-02-02T01:33:21.154345Z", "level": "info", "context": {"user": {"id": 1, "name": "Ben Johnson"}, "http": {"method": "GET", "host": "timber.io", "path": "/path", "request_id": "abcd1234"}}, "event": {"http_server_response": {"status": 200, "time_ms": 45.2}}}
```

Allowing you to run queries like:

1. `context.request_id:abcd1234` - View all logs generated for a specific request.
2. `context.user.id:1` - View logs generated by a specific user.
3. `type:http_response` - View specific events (exceptions, sql queries, etc)
4. `http_server_response.time_ms:>=1000` - View slow responses with the ability to zoom out and view them in context (request, user, etc).
5. `level:error` - Levels in your logs!


## Installation

1. In `Gemfile`, add the `timber` gem:

    ```ruby
    gem 'timber', '~> 1.0'
    ```

2. In your `shell`, run `bundle install`

3. In your `shell`, run `bundle exec timber install`


## Usage

<details><summary><strong>Basic logging</strong></summary><p>

Use `Logger` as normal:

```ruby
logger.info("My log message")

# => My log message @metadata {"level": "info", "context": {...}}
```

Timber will *never* deviate from the public `::Logger` interface in *any* way.

---

</p></details>

<details><summary><strong>Custom events</strong></summary><p>

Custom events allow you to extend beyond events already defined in
the [`Timber::Events`](lib/timber/events) namespace.

```ruby
Logger.warn "Payment rejected", payment_rejected: {customer_id: "abcd1234", amount: 100, reason: "Card expired"}

# => Payment rejected @metadata {"level": "warn", "event": {"payment_rejected": {"customer_id": "abcd1234", "amount": 100, "reason": "Card expired"}}, "context": {...}}
```

* Notice the `:payment_rejected` root key. Timber will classify this event as such.
* In the [Timber console](https://app.timber.io) use the query: `type:payment_rejected` or `payment_rejected.amount:>100`.
* See more details on our [custom events docs page](https://timber.io/docs/ruby/custom-events/)

---

</p></details>

<details><summary><strong>Custom contexts</strong></summary><p>

Context is additional data shared across log lines. Think of it like log join data.
Custom contexts allow you to extend beyond contexts already defined in
the [`Timber::Contexts`](lib/timber/contexts) namespace.

```ruby
Timber::CurrentContext.with({build: {version: "1.0.0"}}) do
  logger.info("My log message")
end

# => My log message @metadata {"level": "info", "context": {"build": {"version": "1.0.0"}}}
```

* Notice the `:build` root key. Timber will classify this context as such.
* In the [Timber console](https://app.timber.io) use queries like: `build.version:1.0.0`
* See more details on our [custom contexts docs page](https://timber.io/docs/ruby/custom-contexts/)

</p></details>

<details><summary><strong>Metrics</strong></summary><p>

Logging metrics is accomplished by logging custom events. Please see our
[metrics docs page](https://timber.io/docs/ruby/metrics/) for a more detailed explanation
with examples.

</p></details>


## Jibber-Jabber

<details><summary><strong>Which log events does Timber structure for me?</strong></summary><p>

Out of the box you get everything in the [`Timber::Events`](lib/timber/events) namespace.

We also add context to every log, everything in the [`Timber::Contexts`](lib/timber/contexts)
namespace. Context is structured data representing the current environment when the log line
was written. It is included in every log line. Think of it like join data for your logs.

---

</p></details>

<details><summary><strong>What about my current log statements?</strong></summary><p>

They'll continue to work as expected. Timber adheres to the default `Logger` interface.
Your previous logger calls will work as they always do.

In fact, traditional log statements for non-meaningful events, debug statements, etc, are
encouraged. In cases where the data is meaningful, consider [logging a custom event](#usage).

</p></details>

<details><summary><strong>How is Timber different?</strong></summary><p>

1. **It's just _better_ logging**. There are no agents, special APIs, or proprietary data
   sets that you can't access. And absolutely no lock-in. It's just clean log data that you own.
2. **Improved log data quality.** Instead of relying on parsing alone, Timber ships libraries that
   structure and augment your logs from _within_ your application. Improving your log data at the
   source.
3. **Human readability.** Timber _augments_ your logs without sacrificing human readability. For
   example: `log message @metadata {...}`. And when you view your logs in the
   [Timber console](https://app.timber.io), you'll see the human friendly messages
   with the ability to view the associated metadata.
4. **Long retention**. Logging is notoriously expensive with low retention. Timber
   offers _6 months_ of retention by default with sane prices.
5. **Normalized schema.** Have multiple apps? All of Timber's libraries adhere to our
   [JSON schema](https://github.com/timberio/log-event-json-schema). This means queries, alerts,
   and graphs for your ruby app can also be applied to your elixir app (for example).

---

</p></details>

---

<p align="center" style="background: #221f40;">
<a href="http://github.com/timberio/timber-elixir"><img src="http://files.timber.io/images/ruby-library-readme-log-truth.png" height="947" /></a>
</p>
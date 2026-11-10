# smart_message

A Ruby gem (v0.0.18 on `develop`; 0.0.17 on RubyGems, 2025-09-12) that turns messages
into self-describing `Hashie::Dash` objects which validate, address (from/to/reply_to),
version, and route themselves over pluggable transports (memory, stdout, file, FIFO,
Redis; RabbitMQ and others live in separate `sm-transport-*` gems). It adds a dispatcher
with filters, deduplication, a circuit breaker, and a file-backed dead letter queue.
README calls it alpha.

## Links

- GitHub: https://github.com/MadBomber/smart_message
- RubyGems: https://rubygems.org/gems/smart_message
- Docs: https://madbomber.github.io/smart_message/
- Issues: https://github.com/MadBomber/smart_message/issues

## Status

_Updated 2026-11-10 (first visit)._ `develop` (9371456) is 6 commits ahead of `main`;
0.0.18 (Ruby 4.1 support, `to_h` fix) is unreleased. Suite green on Ruby 4.1.0-preview1:
316 runs, 940 assertions, 0F/0E/27 skips (Redis tests run when localhost Redis is up).
No test CI before this visit (only a docs-deploy workflow and a stale `.travis.yml`).
First security audit and a brief dead-code pass ran (findings file).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything
else in this repo.

- (filled in below as this visit's PRs and issues are recorded)

## Open bomberbot PRs

- (none yet)

## Merged bomberbot PRs

- (none)

---
layout: post
title:  "sqlc v1.5.0 released"
date:   2020-08-05 13:10:42 -0700
categories: posts
tags: [release notes]
---

The new MySQL engine, code-named Dolphin, has finally landed. To try it out,
use `mysql:beta` for the engine value in `sqlc.json`. This uses the same
compiler infrastructure as the PostgreSQL engine, giving it feature parity and
making it easier to maintain. 

The old engine is official deprecated and will be placed behind the
`mysql:deprecated` name in v1.6.0. The engine will be completely removed in
v1.7.0. For more information, see the [MySQL
roadmap](https://github.com/sqlc-dev/sqlc/issues/633).

On the PostgreSQL side of things, I've used some magic to generate function
signatures for all built-in functions. This also includes many of the most
popular PostgreSQL extensions. You will need to make sure to have `CREATE
EXTENSION ...` in your schema to get this functionality.

## Changelog

### Core
* Add option to return empty slices (instead of nil slices) in :many queries
* Use "nullable" instead of "null" in configuration file ([#571](https://github.com/sqlc-dev/sqlc/pull/571))
* Add debugging support via SQLCDEBUG ([#573](https://github.com/sqlc-dev/sqlc/pull/573))
* Support calling functions with defaults ([#635](https://github.com/sqlc-dev/sqlc/pull/635))

### PostgreSQL
* Add support for the money type ([#552](https://github.com/sqlc-dev/sqlc/pull/552))
* Add support for 'cidr' and 'interval' types ([#601](https://github.com/sqlc-dev/sqlc/pull/601))
* Generate all functions in pg_catalog ([#550](https://github.com/sqlc-dev/sqlc/pull/550))

### MySQL
* Add the `mysql:beta` engine, which will be the default next release

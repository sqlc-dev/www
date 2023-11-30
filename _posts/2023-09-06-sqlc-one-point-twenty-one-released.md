---
layout: post
title:  "sqlc v1.21.0 released"
date:   2023-09-06 12:00:00 -0700
categories: posts
tags: [release notes]
description: >
  New features include MySQL CALL and IS [NOT] NULL support,
  plugin access to environment variables, and SQLite engine improvements.
---

We’ve just released sqlc version 1.21.0 which includes many bug fixes and a few new features, and coincidentally happens to be the first sqlc release built with Go 1.21. Read below for some highlights, or head straight to the [sqlc changelog](https://docs.sqlc.dev/en/latest/reference/changelog.html#v1-21-0).

We’ll be at GopherCon 2023 in San Diego from September 24th through 26th. If you’ll be there too and want to say hi we’d love to chat with you! Please get in touch and let us know.

We’re about to reach 9000 GitHub stars, so thanks to all of you who’ve supported the sqlc project thus far and please continue to tell your friends and colleagues about the work we’re doing.

Here are some highlights from the just-released [sqlc 1.21.0](https://github.com/sqlc-dev/sqlc/releases/tag/v1.21.0):

### MySQL engine improvements

`sqlc` previously didn't know how to parse a `CALL` statement when using the MySQL engine,
which meant it was impossible to use sqlc with stored procedures in MySQL databases.

Additionally, `sqlc` now supports `IS [NOT] NULL` in queries. And `LIMIT` and `OFFSET` clauses
now work with `UNION`.

### SQLite engine improvements

GitHub user [@orisano](https://github.com/orisano) continues to bring bugfixes and
improvements to `sqlc`'s SQLite engine. See the "Changes" section below for the
full list.

### Plugin access to environment variables

If you're authoring a [sqlc plugin](https://docs.sqlc.dev/en/v1.21.0/guides/plugins.html), you can now configure
sqlc to pass your plugin the values of specific environment variables.

For example, if your plugin
needs the `PATH` environment variable, add `PATH` to the `env` list in the
`plugins` collection.

```yaml
version: '2'
sql:
- schema: schema.sql
  queries: query.sql
  engine: postgresql
  codegen:
  - out: gen
    plugin: test
plugins:
- name: test
  env:
  - PATH
  wasm:
    url: https://github.com/sqlc-dev/sqlc-gen-test/releases/download/v0.1.0/sqlc-gen-test.wasm
    sha256: 138220eae508d4b65a5a8cea555edd155eb2290daf576b7a8b96949acfeb3790
```

### Changelog

The full list of changes is in the [sqlc changelog](https://docs.sqlc.dev/en/latest/reference/changelog.html#v1-21-0).

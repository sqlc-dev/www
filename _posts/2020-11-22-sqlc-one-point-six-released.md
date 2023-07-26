---
layout: post
title:  "sqlc v1.6.0 released"
date:   2020-11-22 13:10:42 -0700
categories: posts
tags: [release notes]
---

The new MySQL engine, code-named Dolphin, is now default. The old engine has
been removed. Your generated code will be different, but should be easy to
adapt. Please open an issue if your code is now broken. If you were using the
`mysql:beta` engine, you'll need to change that to `mysql`.

## Changelog

### Core
* Add option to return emit `db` tags for struct fields ([#656](https://github.com/sqlc-dev/sqlc/pull/656))
* Add support for variadic stored procedures and functions ([#798](https://github.com/sqlc-dev/sqlc/pull/798))
* Fix issue installing sqlc via `go get` ([#744](https://github.com/sqlc-dev/sqlc/pull/744))
* Add support for nontrivial import paths for overrides ([#785](https://github.com/sqlc-dev/sqlc/issues/785))

### Go
* You can now specify the `go_type` import path of an override using an object instead of a string. 

```
overrides:
  - db_type: "uuid"
    go_type:
      import: "gopkg.in/guregu/null.v3"
      package: "null"
      type: "Bool"
```

### MySQL
* Add function definitions for all built-in MySQL functions ([#796](https://github.com/sqlc-dev/sqlc/pull/796))
* Upgrade to the latest version of pingcap/parser ([#744](https://github.com/sqlc-dev/sqlc/pull/744))

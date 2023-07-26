---
layout: post
title:  "sqlc v1.7.0 released"
date:   2021-02-28 13:10:42 -0700
categories: posts
tags: [release notes]
---

sqlc has come to Windows! It's currently MySQL-only, but I hope to land
PostgreSQL support in the coming months.

## Changelog

### Core
* Initial Windows Support ([#886](https://github.com/sqlc-dev/sqlc/pull/886))
* UNION support ([#896](https://github.com/sqlc-dev/sqlc/pull/896))
* Publish documentation at https://docs.sqlc.dev
* cmd: Allow config file location to be specified ([#863](https://github.com/sqlc-dev/sqlc/pull/863))
* Exit with non-zero error code when parsing fails ([#870](https://github.com/sqlc-dev/sqlc/pull/870))

### MySQL
* Compile tinyint(1) to bool ([#873](https://github.com/sqlc-dev/sqlc/pull/873))
* Support joins in update statements ([#883](https://github.com/sqlc-dev/sqlc/pull/883))

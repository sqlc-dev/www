---
layout: post
title:  "sqlc v1.8.0 released"
date:   2021-05-02 13:10:42 -0700
categories: posts
tags: [release notes]
---

Sssssss. Experimental Python support has landed.

## Changelog

### Core
- cmd: Fix sqlc init ([#959](https://github.com/kyleconroy/sqlc/pull/959)) 

### MySQL
- Generate correct code for booleans
- Calling mysql LAST_INSERT_ID(expr) function ([#977](https://github.com/kyleconroy/sqlc/pull/977)) 

### PostgreSQL
- Support PostgreSQL 12 and 13 features by upgrading to pg_query_go/v2 ([#949](https://github.com/kyleconroy/sqlc/pull/949))
- Fix ALTER TYPE to update column types ([#973](https://github.com/kyleconroy/sqlc/pull/973))

### Python
- Add experimental Python support behind the `--experimental` flag

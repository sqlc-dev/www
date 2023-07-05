---
layout: post
title:  "sqlc v1.4.0 released"
date:   2020-06-17 13:10:42 -0700
categories: posts
tags: [release notes]
---

This release includes a complete refactor of the compiler internals. The
compiler now uses a database-agnostic SQL AST, which the PostgreSQL backend
uses by default.

I've tested the new path extensively, but you still
may run into bugs. If you do, you can use the old code path by setting the the
following environment variable: `SQLC_EXPERIMENTAL_PARSER=off`. The old code
path will be removed in v1.5.0, so please report any bugs you run into.

A new `:execresult` query command has been added. The generated methods will return `(sql.Result, error)`, the same as [DB.ExecContext](https://golang.org/pkg/database/sql/#DB.ExecContext). 

## Changelog

### Core

- Post the compiler to the new, database-agnostic SQL AST
- Add support for dbmate migrations ([#511](https://github.com/kyleconroy/sqlc/pull/511))
- Apply rename rules to enum constants ([#523](https://github.com/kyleconroy/sqlc/pull/523))
- Add the :execresult query annotation ([#542](https://github.com/kyleconroy/sqlc/pull/542))

### PostgreSQL

- Support columns from subselect statements ([#489](https://github.com/kyleconroy/sqlc/pull/489))
- Temporary fix for typecast function parameters ([#530](https://github.com/kyleconroy/sqlc/pull/530))
- Support functions with table parameters ([#541](https://github.com/kyleconroy/sqlc/pull/541))

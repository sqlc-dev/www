---
layout: post
title:  "sqlc v1.3.0 released"
date:   2020-05-12 13:10:42 -0700
categories: posts
tags: [release notes]
---

Don't panic! I've fixed a few cases where sqlc was struggling to handle DDL
statements. JSON columns are now properly represented as
<code>json.RawMessage</code> structs.

### Core
* Exclude golang-migrate .down.sql files in dirs + single files ([#445](https://github.com/kyleconroy/sqlc/pull/445))
* Add `emit_exact_table_names` config option ([#474](https://github.com/kyleconroy/sqlc/pull/474))

### PostgreSQL
* Add PostgreSQL support for TRUNCATE ([#448](https://github.com/kyleconroy/sqlc/pull/448))
* Emit json.RawMessage for JSON columns ([#461](https://github.com/kyleconroy/sqlc/pull/461))
* Fix panic walking CreateTableAsStmt (#[475](https://github.com/kyleconroy/sqlc/475))

---
layout: post
title:  "Mainly a bug fix release"
date:   2024-03-28 8:00:00 -0700
categories: posts
tags: [release notes]
description: >
  sqlc bug fix release
---

`sqlc` is a command line tool that generates type-safe code from SQL. Today we released [sqlc v1.26.0](https://docs.sqlc.dev/en/latest/reference/changelog.html#v1-26-0), mainly a bug fix release.

## What's new

This release is mainly a bug fix release. It also includes an [important security fix](https://github.com/sqlc-dev/sqlc/issues/3194) for users using output plugins.

### Bug Fixes

- (docker) Use distroless base image instead of scratch (#3111)
- (generate) Ensure files are created inside output directory (#3195)
- (mysql) BREAKING: Use `int16` for MySQL `SMALLINT` and `YEAR` (#3106)
- (mysql) BREAKING: Use `int8` for MySQL TINYINT (#3298)
- (mysql) Variables not resolving in ORDER BY statements (#3115)
- (opts) Validate SQL package and driver options (#3241)
- (postgres/batch) Ignore query_parameter_limit for batches
- (scripts) Remove deprecated test output regeneration script (#3105)
- (sqlite) Correctly skip unknown statements (#3239)

### Documentation

- (postgres) Add instructions for PostGIS/GEOS (#3182)
- Improve details on TEXT (#3247)

### Features

- (generate) Avoid generating empty Go imports (#3135)
- (mysql) Add NEXTVAL() to the MySQL catalog (#3147)
- (mysql) Support json.RawMessage for LOAD DATA INFILE (#3099)

### Build

- (deps) Bump github.com/jackc/pgx/v5 to 5.5.5 (#3259)
- (deps) Bump modernc.org/sqlite to 1.29.5 (#3200)
- (deps) Bump github.com/go-sql-driver/mysql to 1.8.0 (#3257)
- (deps) Bump github.com/tetratelabs/wazero to 1.7.0 (#3096)
- (deps) Bump github.com/pganalyze/pg_query_go to v5 (#3096)
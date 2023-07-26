---
layout: post
title:  "sqlc v1.2.0 released"
date:   2020-04-11 13:10:42 -0700
categories: posts
tags: [release notes]
---

This release brings with it an official Docker image for you whale fans out
there. A configuration file change allows file systems paths to be a list of
paths.

## Core
* Publish an official Docker container ([#422](https://github.com/sqlc-dev/sqlc/pull/422))
* Configuration values for `schema` and `queries` can now be a list of paths ([#426](https://github.com/sqlc-dev/sqlc/pull/426))
* Rename rules correctly apply to table names ([#435](https://github.com/sqlc-dev/sqlc/pull/435))

## SQLite
* Add support for ALTER TABLE DDL ([#414](https://github.com/sqlc-dev/sqlc/pull/414))

## PostgreSQL
* Generate correct type for SELECT EXISTS ([#411](https://github.com/sqlc-dev/sqlc/pull/411))
* Add support for ALTER TYPE RENAME / ADD VALUE ([#433](https://github.com/sqlc-dev/sqlc/pull/433))
* Properly quote reserved keywords when expanding `SELECT *` ([#436](https://github.com/sqlc-dev/sqlc/pull/436))

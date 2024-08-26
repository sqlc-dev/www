---
layout: post
title:  "Managed databases for any PostgreSQL server"
date:   2024-08-05 8:00:00 -0700
categories: posts
tags: [release notes]
description: >
  sqlc now supports managed databases
---

`sqlc` is a command line tool that generates type-safe code from SQL. Today we released [sqlc v1.27.0](https://docs.sqlc.dev/en/latest/reference/changelog.html#v1-27-0) with support for local managed databases.

## What's new

### Sunsetting hosted ephemeral databases

On September 4th, 2024, we'll be turning off support for hosted ephemeral
databases in sqlc Cloud. If you use managed databases or query verification with
sqlc Cloud, you'll need to update to 1.27.0 to avoid any issues. I've already
emailed you if you're using this feature. It's more than likely that you are
not!

### Bug Fixes

- (dbmanager) Add leading slash to db uri path rewrite (#3493)
- (verify) Include database engine in request (#3522)

### Features

- (golang) Add initialisms configuration (#3308)
- (compiler) Support subqueries in the FROM clause (second coming) (#3310)
- Managed databases with any accessible server  (#3421)
- (vet) Use new dbmanager client (#3423)
- (verify) Update verify to work with managed databases (#3425)

### Documentation

- Fix typo in config (#3358)
- Resolve a typo in configuration keys (#3349)
- Add sponsorship information to README (#3413)
- Update the language-support to include C# (#3408)
- Add migration guide for hosted managed databases (#3417)
- Fix readme links (#3424)
- Update the managed db and verify documentation (#3426)
- Add sponsor image (#3428)
- Add Ruby as supported language (#3487)
- Update migrating-to-sqlc-gen-kotlin.md (#3454)
- Fix typo in comment (#3316)
- Fix deprecated build tag format (#3361)

### Testing

- (endtoend) Re-use databases when possible (#3315)
- Enabled MySQL database (#3318)
- Remove internal/sqltest/hosted package (#3521)
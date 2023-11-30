---
layout: post
title:  "Database-backed query analysis in v1.23.0"
date:   2023-10-24 15:00:00 -0700
categories: posts
image: img/sqlc-v1-23-0-database-backed-analyzer.png
tags: [release notes]
excerpt: >
  sqlc can now use a database connection for improved query analysis.
---

`sqlc` is a command line tool that generates type-safe code from SQL. Today we released [sqlc v1.23.0](https://docs.sqlc.dev/en/latest/reference/changelog.html#v1-23-0), so let’s take a look at what’s new.

## Database-backed query analysis

At a high level, sqlc does three things: parse SQL statements, analyze those statements, and generate source code for applications to use those statements. The second step, analysis of statements, requires sqlc to understand or make assumptions about the internal workings of the database engines it supports.

Since its inception sqlc has relied on its own built-in static catalog information from various database engines in order to perform query analysis. It augments that with information gathered from your schema to determine things like the columns a query will return and the types of query parameters. But often this information isn’t sufficient for complicated queries, and sqlc will error when it shouldn’t or will generate source code that assumes a lowest-common-denominator untyped interface for parameters and return values.

We can do better, but attempting to recreate the entirety of a database’s analysis engine inside of sqlc is at best a difficult moving target and at worst a fool’s errand. So starting with version 1.23.0 sqlc can gather additional metadata information from a running database to enhance its query analysis. This resolves a [very large number of issues](https://github.com/sqlc-dev/sqlc/issues?q=is%3Aissue+label%3Aanalyzer) in the sqlc backlog, so if you have a thorny query that sqlc previously couldn’t understand it’s fairly likely the new analysis engine will handle it.

Here’s a [sqlc playground example](https://play.sqlc.dev/p/4349b0f90cbafd26780c96071f240287795464e882976616fa0009994af2efee) of a query that confused sqlc previously. Running `sqlc generate` for Go used to create a func that returned `interface{}` instead of `time.Time` for the timestamp values. No more!

```
-- name: ActivityInfo :one
SELECT
    COUNT(*) as NumOfActivities,
    MIN(event_time) as MinDate, 
    MAX(event_time) as MaxDate 
FROM activities;
```

The new database-backed analysis engine supports PostgreSQL today, with [MySQL](https://github.com/sqlc-dev/sqlc/issues/2902) and [SQLite](https://github.com/sqlc-dev/sqlc/issues/2903) support planned.

You will need to add a [database section](https://docs.sqlc.dev/en/latest/reference/config.html#database) to your sqlc configuration file to opt in. The easiest way to do that is with [sqlc Cloud’s managed databases](https://docs.sqlc.dev/en/stable/howto/managed-databases.html), but you can also point sqlc at any accessible database as long as you have a valid connection string and the database’s schema is up-to-date.

We’re very excited to have you try this out with some complicated queries and let us know what works and what doesn’t.

### Play around in the playground

We’ve updated the [sqlc playground](https://play.sqlc.dev) to support the new database-backed analyzer using [sqlc Cloud’s managed databases](https://docs.sqlc.dev/en/latest/howto/managed-databases.html), all without creating an auth token. Here’s a playground configuration you can use as a starting point (you don’t need to modify the `"<PROJECT_ID>"`, it’s a magic string that we replace):

```
version: "2"
cloud:
  project: "<PROJECT_ID>" # <- don't change this!
sql:
- schema: "schema.sql"
  queries: "query.sql"
  engine: "postgresql"
  database:
    managed: true
  gen:
    go:
      out: db
```

The `"<PROJECT_ID>"` value only works on the playground. To use managed databases with sqlc, you’ll need a project ID and an auth token from the [sqlc Cloud dashboard](https://dashboard.sqlc.dev/).

## New `createdb` command

Building on the ephemeral database infrastructure we created to support managed databases in sqlc generate and sqlc vet, in v1.23.0 we added a simple command called `createdb`.

When you have a cloud project configured, you can use the `sqlc createdb` command to spin up a fresh ephemeral database with your schema and print its connection string to standard output. This is useful for integrating with other tools that need short-lived access to a database with your schema, like [sqlx](https://docs.rs/sqlx/latest/sqlx/) and [pgtyped](https://pgtyped.dev/docs/).

In the simplest case, you can use psql to poke around and see that it works:

```
psql $(sqlc createdb)
```

Ephemeral databases only live for two minutes. Read more in the [managed databases](https://docs.sqlc.dev/en/latest/howto/managed-databases.html#with-other-tools) documentation.

## Support for pgvector

If you're using [pgvector](https://github.com/pgvector/pgvector), say goodbye to custom overrides! As of v1.23.0 sqlc now generates code using [pgvector-go](https://github.com/pgvector/pgvector-go#pgx) as long as you're using `pgx/v5` as your database driver. We’ve also made the pgvector extension available to [managed databases](https://docs.sqlc.dev/en/latest/howto/managed-databases.html).

## And more...

There were more than 30 substantive features and bug fixes included in this release, including several contributions from first-time contributors. The full list of changes is in the [sqlc changelog](https://docs.sqlc.dev/en/latest/reference/changelog.html#v1-23-0).
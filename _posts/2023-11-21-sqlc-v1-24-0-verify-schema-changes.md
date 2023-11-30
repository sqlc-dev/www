---
layout: post
title:  "Verifying database schema changes in v1.24.0"
date:   2023-11-21 15:00:00 -0700
categories: posts
image: img/sqlc-verify-database-changes.png
tags: [release notes]
description: >
  sqlc can now catch backwards-incompatible schema changes before they cause a production outage.
---

`sqlc` is a command line tool that generates type-safe code from SQL. Today we released [sqlc v1.24.0](https://docs.sqlc.dev/en/latest/reference/changelog.html#v1-24-0) which includes a new feature to verify the correctness of database schema changes.

## Catch unsafe database migrations with `sqlc verify`

Schema updates and poorly-written queries often bring down production databases. That’s bad.

Out of the box, `sqlc generate` catches some of these issues. Running `sqlc vet` with the `sqlc/db-prepare` rule catches more subtle problems. But there is a large class of issues that sqlc can’t prevent by looking at current schema and queries alone.

For instance, when a schema change is proposed, existing queries and code running in production might fail when the schema change is applied. Enter `sqlc verify`, which analyzes existing queries against new schema changes and errors if there are any issues.

Let's look at an example. Assume you have these two tables in production.

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY
);

CREATE TABLE user_actions (
  id UUID PRIMARY KEY,
  user_id UUID NOT NULL,
  action TEXT,
  created_at TIMESTAMP
);
```

Your application contains the following query to join user actions against the users table.

```sql
-- name: GetUserActions :many
SELECT * FROM users u
JOIN user_actions ua ON u.id = ua.user_id
ORDER BY created_at;
```

To configure `sqlc` to push, set the `project` key in your `cloud` configuration to the value of your project ID, obtained via the [dashboard](https://dashboard.sqlc.dev).

```yaml
version: '2'
cloud:
  project: '<PROJECT_ID>'
sql:
- schema: schema.sql
  queries: query.sql
  engine: postgresql
  gen:
    go:
      out: db
```

`sqlc` expects to find a valid auth token in the value of the `SQLC_AUTH_TOKEN`
environment variable. You can create an auth token via the [dashboard](https://dashboard.sqlc.dev) as well.

```shell
export SQLC_AUTH_TOKEN=sqlc_xxxxxxxx
```

Once those have been configured, run `sqlc push`:

```bash
$ sqlc push
```

So far, so good. Now let's add a new database migration. Here we're adding a new `created_at` column to the `users` table.

```sql
ALTER TABLE users ADD COLUMN created_at TIMESTAMP;
```

Running `sqlc generate` fails with this change, returning a `column reference "created_at" is ambiguous` error. You update your query to fix the issue.

```sql
-- name: GetUserActions :many
SELECT * FROM users u
JOIN user_actions ua ON u.id = ua.user_id
ORDER BY u.created_at;
```

While that change fixes the issue, there's a production outage waiting to happen. When the schema change is applied, the existing `GetUserActions` query will begin to fail. The correct way to fix this is to deploy the updated query before applying the schema migration.

`sqlc verify` was designed to catch these types of problems. It ensures migrations are safe to deploy by sending your current schema and queries to sqlc cloud. There, we run your existing queries against your new schema changes to find any issues.

Here `sqlc verify` alerts you to the fact that adding the new column isn't safe.

```sh
$ sqlc verify
FAIL: app query.sql

=== Failed
=== FAIL: app query.sql GetUserActions
    ERROR: column reference "created_at" is ambiguous (SQLSTATE 42702)
```

By the way, this scenario isn't made up! It happened to us a few weeks ago. We've been happily testing early versions of `verify` for the last two weeks and haven't had any issues since.

This type of verification is only the start. If your application is deployed on-prem by your customers, `verify` could tell you if it's safe for your customers to rollback to an older version of your app, even after schema migrations have been run.

### Pushing schema and queries

We've renamed the `upload` sub-command to `push`. We changed the data sent along in a push request. Upload used to include the configuration file, migrations, queries, and all generated code. Push drops the generated code in favor of including the [plugin.GenerateRequest](https://buf.build/sqlc/sqlc/docs/main:plugin#plugin.GenerateRequest), which is the protocol buffer message we pass to codegen plugins.

We also add annotations to each push. By default, we include these environment variables if they are present:

```
GITHUB_REPOSITORY
GITHUB_REF
GITHUB_REF_NAME
GITHUB_REF_TYPE
GITHUB_SHA
```

Like upload, `push` should be run when you tag a release of your application. We run it on every push to main, as we continuously deploy those commits.

## MySQL support in `createdb`

The `createdb` command, added in the last release, now supports MySQL. If you have a cloud project configured, you can use `sqlc createdb` to spin up a new ephemeral database with your schema and print its connection string to standard output. This is useful for integrating with other tools. Read more in the [managed databases](../howto/managed-databases.md#with-other-tools) documentation.

## Plugin interface refactor

This release includes a refactored plugin interface to better support future functionality. Plugins now support different methods via a gRPC service interface, allowing plugins to support different functionality in a backwards-compatible way.

By using gRPC interfaces, we can even (theoretically) support [remote plugins](https://github.com/sqlc-dev/sqlc/pull/2938), but that's something for another day.

## And more...

There were more than 20 substantive features and bug fixes included in this release, including several contributions from first-time contributors. The full list of changes is in the [sqlc changelog](https://docs.sqlc.dev/en/latest/reference/changelog.html#v1-24-0).
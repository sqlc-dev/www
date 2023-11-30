---
layout: post
title:  "sqlc v1.20.0 released"
date:   2023-07-31 9:00:00 -0700
categories: posts
tags: [release notes]
description: >
  New features include EXPLAIN output in sqlc vet, opting-out of lint rules,
  MySQL bulk insert and CAST support.
---

## Version [1.20.0](https://github.com/sqlc-dev/sqlc/releases/tag/v1.20.0)

### `kyleconroy/sqlc` is now `sqlc-dev/sqlc`

We've completed our migration to the [sqlc-dev/sqlc](https://github.com/sqlc-dev/sqlc) repository. All existing links and installation instructions will continue to work. If you're using the `go` tool to install `sqlc`, you'll need to use the new import path to get v1.20.0 (and all future versions).

```sh
# INCORRECT: old import path
go install github.com/kyleconroy/sqlc/cmd/sqlc@v1.20.0

# CORRECT: new import path
go install github.com/sqlc-dev/sqlc/cmd/sqlc@v1.20.0
```

We designed the upgrade process to be as smooth as possible. If you run into any issues, please [file a bug report](https://github.com/sqlc-dev/sqlc/issues/new?assignees=&labels=bug%2Ctriage&projects=&template=BUG_REPORT.yml) via GitHub.

### Use `EXPLAIN ...` output in lint rules

`sqlc vet` can now run `EXPLAIN` on your queries and include the results for use in your lint rules. For example, this rule checks that `SELECT` queries use an index.

```yaml
version: 2
sql:
  - schema: "query.sql"
    queries: "query.sql"
    engine: "postgresql"
    database:
      uri: "postgresql://postgres:postgres@localhost:5432/postgres"
    gen:
      go:
        package: "db"
        out: "db"
    rules:
      - has-index
rules:
- name: has-index
  rule: >
    query.sql.startsWith("SELECT") &&
    !(postgresql.explain.plan.plans.all(p, has(p.index_name) || p.plans.all(p, has(p.index_name))))
```

The expression environment has two variables containing `EXPLAIN ...` output, `postgresql.explain` and `mysql.explain`. `sqlc` only populates the variable associated with your configured database engine, and only when you have a [database connection configured](https://docs.sqlc.dev/en/stable/reference/config.html#database).

For the `postgresql` engine, `sqlc` runs

```sql
EXPLAIN (ANALYZE false, VERBOSE, COSTS, SETTINGS, BUFFERS, FORMAT JSON) ...
```

where `"..."` is your query string, and parses the output into a [`PostgreSQLExplain`](https://buf.build/sqlc/sqlc/docs/v1.20.0:vet#vet.PostgreSQLExplain) proto message.

For the `mysql` engine, `sqlc` runs

```sql
EXPLAIN FORMAT=JSON ...
```

where `"..."` is your query string, and parses the output into a [`MySQLExplain`](https://buf.build/sqlc/sqlc/docs/v1.20.0:vet#vet.MySQLExplain) proto message.

These proto message definitions are too long to include here, but you can find them in the `protos` directory within the `sqlc` source tree.

The output from `EXPLAIN ...` depends on the structure of your query so it's a bit difficult to offer generic examples. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/current/using-explain.html) and [MySQL documentation](https://dev.mysql.com/doc/refman/en/explain-output.html) for more information.

```yaml
...
rules:
- name: postgresql-query-too-costly
  message: "Query cost estimate is too high"
  rule: "postgresql.explain.plan.total_cost > 1.0"
- name: postgresql-no-seq-scan
  message: "Query plan results in a sequential scan"
  rule: "postgresql.explain.plan.node_type == 'Seq Scan'"
- name: mysql-query-too-costly
  message: "Query cost estimate is too high"
  rule: "has(mysql.explain.query_block.cost_info) && double(mysql.explain.query_block.cost_info.query_cost) > 2.0"
- name: mysql-must-use-primary-key
  message: "Query plan doesn't use primary key"
  rule: "has(mysql.explain.query_block.table.key) && mysql.explain.query_block.table.key != 'PRIMARY'"
```

When building rules that depend on `EXPLAIN ...` output, it may be helpful to see the actual JSON returned from the database. `sqlc` will print it When you set the environment variable `SQLCDEBUG=dumpexplain=1`. Use this environment variable together with a dummy rule to see `EXPLAIN ...` output for all of your queries.

### Opting-out of lint rules

For any query, you can tell `sqlc vet` not to evaluate lint rules using the `@sqlc-vet-disable` query annotation.

```sql
/* name: GetAuthor :one */
/* @sqlc-vet-disable */
SELECT * FROM authors
WHERE id = ? LIMIT 1;
```

### Bulk insert for MySQL

_Developed by [@Jille](https://github.com/Jille)_

MySQL now supports the `:copyfrom` query annotation. The generated code uses the [LOAD DATA](https://dev.mysql.com/doc/refman/8.0/en/load-data.html) command to insert data quickly and efficiently.

Use caution with this feature. Errors and duplicate keys are treated as warnings and insertion will continue, even without an error for some cases.  Use this in a transaction and use `SHOW WARNINGS` to check for any problems and roll back if necessary.

Check the [error handling](https://dev.mysql.com/doc/refman/8.0/en/load-data.html#load-data-error-handling) documentation for more information.

```sql
CREATE TABLE foo (a text, b integer, c DATETIME, d DATE);

-- name: InsertValues :copyfrom
INSERT INTO foo (a, b, c, d) VALUES (?, ?, ?, ?);
```

```go
func (q *Queries) InsertValues(ctx context.Context, arg []InsertValuesParams) (int64, error) {
	...
}
```

`LOAD DATA` support must be enabled in the MySQL server.

### CAST support for MySQL

_Developed by [@ryanpbrewster](https://github.com/ryanpbrewster) and [@RadhiFadlillah](https://github.com/RadhiFadlillah)_

`sqlc` now understands `CAST` calls in MySQL queries, offering greater flexibility when generating code for complex queries.

```sql
CREATE TABLE foo (bar BOOLEAN NOT NULL);

-- name: SelectColumnCast :many
SELECT CAST(bar AS BIGINT) FROM foo;
```

```go
package querytest

import (
	"context"
)

const selectColumnCast = `-- name: SelectColumnCast :many
SELECT CAST(bar AS BIGINT) FROM foo
`

func (q *Queries) SelectColumnCast(ctx context.Context) ([]int64, error) {
  ...
}
```

### SQLite improvements

A slew of fixes landed for our SQLite implementation, bringing it closer to parity with MySQL and PostgreSQL. We want to thank [@orisano](https://github.com/orisano) for their continued dedication to improving SQLite support in `sqlc`.

### Changelog

Full list of changes [here](https://docs.sqlc.dev/en/latest/reference/changelog.html#v1-20-0).

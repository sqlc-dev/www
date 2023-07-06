---
layout: post
title:  "sqlc v1.19.0 released"
date:   2023-07-06 13:10:42 -0700
categories: posts
tags: [release notes]
excerpt: >
  New features include linting support via sqlc vet, database connectivity and
  filtering out unused database structs.
---

## Version [1.19.0](https://github.com/kyleconroy/sqlc/releases/tag/v1.19.0)

### sqlc vet

[`vet`](https://docs.sqlc.dev/en/v1.19.0/reference/cli.html#vet) runs queries through a set of lint rules.

Rules are defined in the `sqlc` [configuration](https://docs.sqlc.dev/en/v1.19.0/reference/config.html#rules) file. They consist
of a name, message, and an expression. If the expression evaluates to `true`, an
error is reported. These expressions are evaluated using
[cel-go](https://github.com/google/cel-go).

While these examples are simplistic, they give you an idea on what types of
rules you can write.

```yaml
version: 2
sql:
  - schema: "query.sql"
    queries: "query.sql"
    engine: "postgresql"
    gen:
      go:
        package: "authors"
        out: "db"
    rules:
      - no-pg
      - no-delete
      - only-one-param
      - no-exec
rules:
  - name: no-pg
    message: "invalid engine: postgresql"
    rule: |
      config.engine == "postgresql"
  - name: no-delete
    message: "don't use delete statements"
    rule: |
      query.sql.contains("DELETE")
  - name: only-one-param
    message: "too many parameters"
    rule: |
      query.params.size() > 1
  - name: no-exec
    message: "don't use exec"
    rule: |
      query.cmd == "exec"
```

### Database connectivity

`vet` also marks the first time that `sqlc` can connect to a live, running
database server. This functionality will be expanded over time, but for now it
powers the `sqlc/db-prepare` built-in rule.

When a [database](https://docs.sqlc.dev/en/v1.19.0/reference/config.html#database) in configured, the `sqlc/db-preapre`
rule will attempt to prepare each of your queries against the connected
database. Any failures will be reported to standard error.

```yaml
version: 2
sql:
  - schema: "query.sql"
    queries: "query.sql"
    engine: "postgresql"
    gen:
      go:
        package: "authors"
        out: "db"
    database:
      url: "postgresql://postgres:password@localhost:5432/postgres"
    rules:
      - sqlc/db-prepare
```

To see this in action, check out the [authors
example](https://github.com/kyleconroy/sqlc/blob/main/examples/authors/sqlc.yaml).

Please note that `sqlc` does not manage or migrate the database. Use your
migration tool of choice to create the necessary database tables and objects.

### Omit unused structs

Added a new configuration parameter `omit_unused_structs` which, when set to
true, filters out table and enum structs that aren't used in queries for a given
package.

### Suggested CI/CD setup

With the addition of `sqlc diff` and `sqlc vet`, we encourage users to run sqlc
in your CI/CD pipelines. See our [suggested CI/CD setup](https://docs.sqlc.dev/en/v1.19.0/howto/ci-cd.md) for
more information.

### Simplified plugin development

The [sqlc-gen-kotlin](https://github.com/sqlc-dev/sqlc-gen-kotlin) and
[sqlc-gen-python](https://github.com/sqlc-dev/sqlc-gen-python) plugins have been
updated use the upcoming [WASI](https://wasi.dev/) support in [Go
1.21](https://tip.golang.org/doc/go1.21#wasip1). Building these plugins no
longer requires [TinyGo](https://tinygo.org/).

### Changelog

Full list of changes [here](https://docs.sqlc.dev/en/v1.19.0/reference/changelog.html#changes).
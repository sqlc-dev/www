---
layout: post
title:  "Announcing sqlc-gen-go"
date:   2023-11-06 11:00:00 -0700
categories: posts
tags: [announcement]
excerpt: >
  We’ve extracted the Go codegen package from sqlc and are publishing it as a plugin.
---

`sqlc` is a command line tool that generates type-safe code from SQL. Today we are publishing [sqlc-gen-go](https://github.com/sqlc-dev/sqlc-gen-go), a Go codegen plugin extracted from [sqlc's source](https://github.com/sqlc-dev/sqlc).

## A brief history of sqlc codegen plugins

At [sqlc’s inception](https://conroy.org/introducing-sqlc) it was a Go-specific tool. It was (and still is) written in Go, and Go developers used sqlc to generate Go code to talk to their databases. The Go community looked kindly upon sqlc, and decided that it was good.

Over time various users thought to themselves “Hey, sqlc is pretty good at generating Go code. I wonder if it could also generate code in other languages?” And thus was born sqlc’s [Kotlin](https://github.com/sqlc-dev/sqlc/pull/300) and [Python](https://github.com/sqlc-dev/sqlc/pull/923) support. The packages implementing that support sat alongside the Go codegen package, embedded in the sqlc source.

This setup was fine, but required contributors to write Go modifying the sqlc source code in order to make changes to the way sqlc generated Kotlin and Python. And it deterred potential contributors of new code generation packages who naturally wanted to write code in their native language rather than Go.

To fix those issues, in 2022 we developed [sqlc’s external codegen plugin interface](https://github.com/sqlc-dev/sqlc/pull/1406), defined by protobuf messages passed via stdin and stdout. This allowed plugin implementers to write a plugin as a standalone binary or WASM module with whatever tools they preferred, as long as the end result could deserialize and serialize protobuf.

Eventually the Kotlin and Python code generation packages embedded in sqlc were ported to WASM and extracted entirely from the sqlc source into their own plugin repositories ([Kotlin](https://github.com/sqlc-dev/sqlc-gen-kotlin), [Python](https://github.com/sqlc-dev/sqlc-gen-python)). But until today, Go codegen was still too deeply intertwined with sqlc to be extracted easily.

## Go codegen as a sqlc plugin

In the past week we have finished the last remaining cleanup items blocking the publishing of sqlc’s internal Go codegen package as an externally-usable plugin. We’ve created a [read-only sqlc-gen-go repository](https://github.com/sqlc-dev/sqlc-gen-go) that we’ll keep up to date with changes from sqlc’s internal Go codegen package. The extraction process doesn’t require code changes other than rewriting some import paths.

All of the configuration options you use today with sqlc to generate Go will work with this plugin using the same code path as sqlc’s embedded Go codegen package. So we hope you’ll find that it’s a drop-in replacement for our built-in Go code generation, but if not please file an issue.

In time we’ll likely remove the Go codegen package from the sqlc source, and at that point the sqlc-gen-go source will become the canonical implementation. But we intend to support sqlc-gen-go as a first-class plugin starting today, and we encourage you to fork and modify it to meet your needs.

## Migrating

Migrating is by no means required. But in case you need or want to, we’ve worked hard to make switching to sqlc-gen-go as seamless as possible. Let’s say you’re generating Go code today using a sqlc.yaml configuration that looks something like this:

```
version: 2
sql:
- schema: "query.sql"
  queries: "query.sql"
  engine: "postgresql"
  gen:
    go:
      package: "db"
      out: "db"
      emit_json_tags: true
      emit_pointers_for_null_types: true
      query_parameter_limit: 5
      overrides:
      - column: "authors.id"
        go_type: "your/package.SomeType"
      rename:
        foo: "bar"
```

To use the sqlc-gen-go WASM plugin for Go codegen, your config will instead look something like this:

```
version: 2
plugins:
- name: golang
  wasm:
    url: "https://downloads.sqlc.dev/plugin/sqlc-gen-go_1.0.1.wasm"
    sha256: "7aa8cd8c269822afa455ac4e7a56a0a12117512811a41f3457859770b315fc27"
sql:
- schema: "query.sql"
  queries: "query.sql"
  engine: "postgresql"
  codegen:
  - plugin: golang
    out: "db"
    options:
      package: "db"
      emit_json_tags: true
      emit_pointers_for_null_types: true
      query_parameter_limit: 5
      overrides:
      - column: "authors.id"
        go_type: "your/package.SomeType"
      rename:
        foo: "bar"
```

The differences are:
* An additional `plugins` list with an entry for the Go codegen WASM plugin. If you’ve built the plugin from source you’ll want to use a `file://` URL. The `sha256` field is required, but will be optional in the upcoming sqlc v1.24.0 release.
* Within the `sql` block, rather than `gen` with `go` nested beneath you’ll have a `codegen` list with an entry referencing the plugin name from the top-level `plugins` list. All options from the current `go` configuration block move as-is into the `options` block within the `codegen` entry. The only special case is `out`, which moves up a level into the `codegen` entry configuration itself.

### Global overrides and renames

If you have global `overrides` or `renames` configured, you’ll need to move those to the new top-level `options` field. Replace the `go` field name with the name you gave your plugin in the plugins list. We’ve used `golang` in this example.

If your existing configuration looks like this:

```
version: "2"
overrides:
  go:
    rename:
      id: "Identifier"
    overrides:
    - db_type: "timestamptz"
      nullable: true
      engine: "postgresql"
      go_type:
        import: "gopkg.in/guregu/null.v4"
        package: "null"
        type: "Time"
...
```

Then your updated configuration would look something like this:

```
version: "2"
plugins:
- name: golang
  wasm:
    url: "https://downloads.sqlc.dev/plugin/sqlc-gen-go_1.0.1.wasm"
    sha256: "7aa8cd8c269822afa455ac4e7a56a0a12117512811a41f3457859770b315fc27"
options:
  golang:
    rename:
      id: "Identifier"
    overrides:
    - db_type: "timestamptz"
      nullable: true
      engine: "postgresql"
      go_type:
        import: "gopkg.in/guregu/null.v4"
        package: "null"
        type: "Time"
...
```

## What’s next?

We’re releasing sqlc-gen-go as a separate codegen plugin so that it’s easier to make sqlc do what you want. We encourage you to fork and modify the sqlc-gen-go plugin to get the behavior you want in your own projects.

If you have an interest in developing a new plugin from scratch using Go, you can build on top of [sqlc's plugin sdk for Go](https://github.com/sqlc-dev/plugin-sdk-go). All of our external codegen plugins, including Go, rely on this sdk. And we’ll have an alpha [TypeScript plugin](https://github.com/sqlc-dev/sqlc/issues/296) based on this sdk coming soon.

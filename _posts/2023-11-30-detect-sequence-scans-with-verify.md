---
layout: post
title:  "Prevent full table scans in production"
date:   2023-11-16 11:00:00 -0700
categories: posts
tags: [news]
excerpt: >
  sqlc verify will warn you about potential slow queries
---

We just shipped an update to `sqlc verify` to emit a warning if any queries do a full table scan. Full table scans, which PostgreSQL calls a sequence scan, can be disastrous when performed against a large table. PostgreSQL will need to look at every row, which is a non-starter for tables with hundreds of thousands of rows.

Here's a quick example for how it works. We have the `authors` table and a query that selects all authors and orders them by name.

```sql
CREATE TABLE authors (
  id   BIGSERIAL PRIMARY KEY,
  name text      NOT NULL,
  bio  text
);

-- name: ListAuthors :many
SELECT * FROM authors
ORDER BY name;
```

Running `verify` will warn that the `ListAuthors` query will do a sequence scan.

```shell
PASS: sqlite query.sql
PASS: mysql query.sql
PASS: postgresql query.sql

=== Warning
=== WARN: postgresql query.sql ListAuthors (no-seq-scan)
    Running this query may result in a sequence scan against the authors table.
    To ignore this warning, add this comment to your query: -- @sqlc-verify-disable no-seq-scan

    -- name: ListAuthors :many
    SELECT id, name, bio FROM authors
    ORDER BY name
```

If the table you're querying is small, this probably isn't a big deal. You can silence the warning by adding an annotation to your query.

```sql
-- name: ListAuthors :many
-- @sqlc-verify-disable no-seq-scan
SELECT * FROM authors
ORDER BY name;
```

If the table is large, create an index on the name column to ensure that the `ListAuthors` query is speedy.

```sql
CREATE INDEX authors_name authors(name);
```

This example is intentionally simplistic. A sequence scan isn't inherently wrong, hence why this defaults to a warning.
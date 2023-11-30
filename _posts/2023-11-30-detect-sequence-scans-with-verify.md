---
layout: post
title:  "Prevent full table scans in production"
date:   2023-11-30 08:00:00 -0700
image: img/prevent-full-table-scan.png
categories: posts
tags: [changelog]
description: >
  sqlc verify will warn you about potential slow queries
---

We updated `sqlc verify` to emit a warning if any queries do a full table scan. Full table scans, which PostgreSQL calls a sequence scan, can be disastrous when performed against a large table, as the query will touch every row in the table.

Let's say you have an `authors` table and a `ListAuthors` query that orders authors by name.

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

Running `sqlc verify` will warn that the `ListAuthors` query will do a sequence scan.

```shell
=== Warning
=== WARN: authors query.sql ListAuthors (sequence-scan-in-query-plan)
    Running this query may result in a sequence scan against the authors table.
    To suppress this warning, add the following annotation to your query:
        -- @sqlc-verify-disable sequence-scan-in-query-plan

    -- name: ListAuthors :many
    SELECT id, name, bio FROM authors
    ORDER BY name
```

If the table you're querying is small, you can silence the warning by adding an annotation to your query.

```sql
-- name: ListAuthors :many
-- @sqlc-verify-disable sequence-scan-in-query-plan
SELECT * FROM authors
ORDER BY name;
```
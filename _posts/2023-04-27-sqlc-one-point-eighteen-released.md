---
layout: post
title:  "sqlc v1.18.0 released"
date:   2023-04-27 13:10:42 -0700
categories: posts
tags: [release notes]
description: >
  Bringing PostgreSQL support to Windows via remote code generation.
---

## Version [1.18.0](https://github.com/sqlc-dev/sqlc/releases/tag/v1.18.0)

### Remote code generation

_Developed by [@andrewmbenton](https://github.com/andrewmbenton)_

At its core, sqlc is powered by SQL engines, which include parsers, formatters,
analyzers and more. While our goal is to support each engine on each operating
system, it's not always possible. For example, the PostgreSQL engine does not
work on Windows.

To bridge that gap, we're announcing remote code generation, currently in
private alpha. To join the private alpha, [sign up for the waitlist](https://docs.google.com/forms/d/e/1FAIpQLScDWrGtTgZWKt3mdlF5R2XCX6tL1pMkB4yuZx5yq684tTNN1Q/viewform?usp=sf_link).

To configure remote generation, configure a `cloud` block in `sqlc.json`.

```json
{
  "version": "2",
  "cloud": {
    "organization": "<org-id>",
    "project": "<project-id>",
  },
  ...
}
```

You'll also need to the `SQLC_AUTH_TOKEN` environment variable.

```bash
export SQLC_AUTH_TOKEN=<token>
```

When the cloud configuration exists, `sqlc generate` will default to remote
generation. If you'd like to generate code locally, pass the `--no-remote`
option.


```bash
sqlc generate --no-remote
```

Remote generation is off by default and requires an opt-in to use.

### sqlc.embed

_Developed by [@nickjackson](https://github.com/nickjackson)_

Embedding allows you to reuse existing model structs in more queries, resulting
in less manual serilization work. First, imagine we have the following schema
with students and test scores.


```sql
CREATE TABLE students (
  id   bigserial PRIMARY KEY,
  name text,
  age  integer
)

CREATE TABLE test_scores (
  student_id bigint,
  score integer,
  grade text
)
```

We want to select the student record and the highest score they got on a test.
Here's how we'd usually do that:

```sql
-- name: HighScore :many
WITH high_scores AS (
  SELECT student_id, max(score) as high_score
  FROM test_scores
  GROUP BY 1
)
SELECT students.*, high_score::integer
FROM students
JOIN high_scores ON high_scores.student_id = students.id;
```

When using Go, sqlc will produce a struct like this:

```
type HighScoreRow struct {
	ID        int64
	Name      sql.NullString
	Age       sql.NullInt32
	HighScore int32
}
```

With embedding, the struct will contain a model for the table instead of a
flattened list of columns.

```sql
-- name: HighScoreEmbed :many
WITH high_scores AS (
  SELECT student_id, max(score) as high_score
  FROM test_scores
  GROUP BY 1
)
SELECT sqlc.embed(students), high_score::integer
FROM students
JOIN high_scores ON high_scores.student_id = students.id;
```

```
type HighScoreRow struct {
	Student   Student
	HighScore int32
}
```

### sqlc.slice

_Developed by Paul Cameron and Jille Timmermans_

The MySQL Go driver does not support passing slices to the IN operator. The
`sqlc.slice` function generates a dynamic query at runtime with the correct
number of parameters.

```sql
/* name: SelectStudents :many */
SELECT * FROM students 
WHERE age IN (sqlc.slice("ages"))
```

```go
func (q *Queries) SelectStudents(ctx context.Context, arges []int32) ([]Student, error) {
```

This feature is only supported in MySQL and cannot be used with prepared
queries.

### Batch operation improvements  

When using batches with pgx, the error returned when a batch is closed is
exported by the generated package. This change allows for cleaner error
handling using `errors.Is`.

```go
errors.Is(err, generated_package.ErrBatchAlreadyClosed)
```

Previously, you would have had to check match on the error message itself.

```
err.Error() == "batch already closed"
```

The generated code for batch operations always lived in `batch.go`. This file
name can now be configured via the `output_batch_file_name` configuration
option.

### Configurable query parameter limits for Go

By default, sqlc will limit Go functions to a single parameter. If a query
includes more than one parameter, the generated method will use an argument
struct instead of positional arguments. This behavior can now be changed via
the `query_parameter_limit` configuration option.  If set to `0`, every
generated method will use a argument struct. 

### Changelog

Full list of changes [here](https://docs.sqlc.dev/en/v1.18.0/reference/changelog.html#changes).


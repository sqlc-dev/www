---
layout: post
title:  "Preview: Generate TypeScript from SQL"
date:   2023-12-04 06:00:00 -0700
image: img/preview-typescript-support.png
categories: posts
tags: [announcement]
description: >
  Announcing the first release of sqlc-gen-typescript. Generate type-safe TypeScript from SQL.
---

I'm so excited to announce the preview release of our TypeScript plugin, [sqlc-gen-typescript](https://github.com/sqlc-dev/sqlc-gen-typescript). TypeScript now joins [Go](https://github.com/sqlc-dev/sqlc-gen-go), [Python](https://github.com/sqlc-dev/sqlc-gen-python) and [Kotlin](https://github.com/sqlc-dev/sqlc-gen-kotlin) as supported languages. Let's dive in.

## Quick tour

The [getting started guide](https://github.com/sqlc-dev/sqlc-gen-typescript#getting-started) has a complete [sqlc-gen-typescript](https://github.com/sqlc-dev/sqlc-gen-typescript) walkthrough, but I wanted to give you a peek at using the generated code. 

We'll start with the SQL. We have one table, `authors`, and a few queries.

```sql
CREATE TABLE authors (
  id   BIGSERIAL PRIMARY KEY,
  name text      NOT NULL,
  bio  text
);

-- name: GetAuthor :one
SELECT * FROM authors
WHERE id = $1 LIMIT 1;

-- name: ListAuthors :many
SELECT * FROM authors
ORDER BY name;

-- name: CreateAuthor :one
INSERT INTO authors (
  name, bio
) VALUES (
  $1, $2
)
RETURNING *;

-- name: DeleteAuthor :exec
DELETE FROM authors
WHERE id = $1;
```

After configuring sqlc to pull in the TypeScript plugin and running `sqlc generate`, you can use your newly-generated code like so:

```ts
import { Pool } from "pg";

// sqlc generated ./db/query_sql.ts which includes these exported functions
import {
  createAuthor,
  deleteAuthor,
  getAuthor,
  listAuthors,
} from "./db/query_sql";

async function main() {
  const client = new Pool({ 
    connectionString: process.env["DATABASE_URL"]
  });
  await client.connect();

  // list all authors
  const authors = await listAuthors(client);
  console.log(authors);

  // create an author
  const author = await createAuthor(client, {
    name: "Anders Hejlsberg",
    bio: "Original author of Turbo Pascal and co-creator of TypeScript",
  });
  if (author === null) {
    throw new Error("author not created");
  }
  console.log(author);

  // get the author we just created
  const anders = await getAuthor(client, { id: author.id });
  if (anders === null) {
    throw new Error("anders not found");
  }
  console.log(anders);

  // delete the author
  await deleteAuthor(client, { id: anders.id });
}
```

## Design considerations

We've learned a ton from building out our Go, Python and Kotlin support over the last four years. Our TypeScript codegen output incorporates much of that learning.

We generate individual functions, not methods on a class. This structure allows for easier composability.

We also take advantage of TypeScript's more advanced type-system. Each function accepts and returns interfaces, meaning that we don't have to have a single model definition for our methods.

## Limitations

We're launching with support for PostgreSQL via [pg](https://www.npmjs.com/package/pg) or [postgres](https://www.npmjs.com/package/postgres) and MySQL via [mysql2](https://www.npmjs.com/package/mysql2).

A reminder that this release is the **start** of our TypeScript support. We opted to release it early to gather feedback before the 1.0.0 release. Expect breaking changes, incomplete feature support, and bugs. So many bugs.

## Feedback

We want to hear from you! Please create an issue on GitHub, post a message on Discord, or send us an email at `hello@sqlc.dev`.


## How it's built

Unlike our previous plugins which are all written in Go, [sqlc-gen-typescript](https://github.com/sqlc-dev/sqlc-gen-typescript) is written in TypeScript. The reason is simple: ensure that TypeScript developers are comfortable jumping into the code, understanding how it works, and contributing back. We also wanted to take advantage of the TypeScript compiler itself, which offers APIs for generating code from an abstract syntax tree (AST). 

Because sqlc prefers to interact with WASM plugins, we needed a way to run JavaScript in a WASM context. Thankfully, this July [Shopify and the Bytecode Alliance announced Javy](https://bytecodealliance.org/articles/javy-hosted-project), a JavaScript-to-WebAssembly toolchain.

To take advantage of Javy we have to do a few things. First we transpile and bundle our TypeScript into a single JavaScript file using [esbuild](https://esbuild.github.io/). We then take that file and run it through Javy, which outputs a .wasm file. Not to shabby!

One downside to this approach is that the WASM blob is big. As of this writing, the 0.1.0 release of sqlc-gen-typescript clocks in at 69MB. Ouch! We're confident that we can dramatically reduce the size by splitting out the parts of the TypeScript compiler that we use from the rest of the TypeScript package, but that optimization will have to wait for a future release.
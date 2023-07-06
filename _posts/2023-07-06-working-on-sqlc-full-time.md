---
layout: post
title:  "Working on sqlc full-time"
date:   2023-07-06 13:10:42 -0700
categories: posts
tags: [announcement]
excerpt: > 
  After developing sqlc in our spare time since 2020, we're excited to share that we've
  working on it full-time.
---

**If you haven't heard of us, [sqlc](/) generates fully type-safe idiomatic code from SQL. Think of it as a reverse ORM. You write SQL queries, run sqlc generate, and then continue writing your code. It works great for Go and other language support is on the horizon.**

After developing sqlc in our spare time since
[2020](https://conroy.org/introducing-sqlc), we're excited to share that we've
incorporated a company, Riza[^1], and will be working on sqlc full-time.

We've got a bunch of great features planned, some of which shipped in
[v1.19.0][2]. Notably, we introduced the `sqlc vet` subcommand which checks your
queries against lint expressions to catch common mistakes in syntactically-valid
SQL.

`vet` also marks the first time that `sqlc` can connect to a running
database server. This functionality will be expanded over time, but for now it
powers the built-in `sqlc/db-prepare` rule.

When a
[database](https://docs.sqlc.dev/en/v1.19.0/reference/config.html#database) in
configured, the rule will prepare each of your queries against the connected
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

We're really excited to get your feedback on this, so if you have thoughts about
how to make `sqlc vet` more useful to you please [let us know][4]!

We created a new GitHub organization ([sqlc-dev][6]) to house existing and
future work. Most sqlc-related repositories have already been migrated; however,
we're waiting to move the [kyleconroy/sqlc][7] repo. The plan it to migrate it
in the next week or two. We hope this doesn't cause too much disruption but some
discomfort during the transition is possible. If you've moved or renamed a Go
project on GitHub before, we would definitely appreciate your tips on how to
make this as smooth as possible.

To keep up with future sqlc developments you can [subscribe to our newsletter][3].

Riza is a for-profit corporation, so naturally we'll be exploring ways to
make money in order to sustain the development of sqlc. We have no intention or
plans to change the license of sqlc. We expect to build adjacent services that
enhance the usefulness of sqlc when you opt-in and hopefully we'll do a
good-enough job to earn your trust as customers.

We're very open to feedback about this or anything else, so please send us a
message via email to [hello@sqlc.dev][4] or in the [sqlc Discord server][5] if
you'd like to chat.

Sincerely,
Kyle and Andrew

[^1]: The name doesn't mean anything in English but does mean ["root" in Greek][1]. We needed a name that wouldn't tie us too closely to "SQL" in case we work on other non-SQL tools in the future. It's a little confusing, we know. And honestly it's almost not worth mentioning, but we don't want you to be alarmed when you see the name "Riza" floating around in copyright notices.

[1]: https://en.wiktionary.org/wiki/%CF%81%CE%AF%CE%B6%CE%B1
[2]: /posts/2023/07/06/sqlc-one-point-nineteen-released
[3]: /newsletter/
[4]: mailto:hello@sqlc.dev
[5]: https://discord.gg/EcXzGe5SEs
[6]: https://github.com/sqlc-dev
[7]: https://github.com/kyleconroy/sqlc
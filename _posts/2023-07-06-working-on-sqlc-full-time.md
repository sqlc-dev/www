---
layout: post
title:  "Working on sqlc full-time"
date:   2023-07-06 13:10:42 -0700
categories: posts
tags: [announcement]
excerpt: > 
  After developing sqlc in our spare time since 2020, we're excited to share that we're
  working on it full-time.
---

**If you haven't heard of us, [sqlc](/) generates fully type-safe idiomatic code from SQL. Think of it as a reverse ORM: you write SQL queries in a text file, run `sqlc generate`, and then continue writing your code. It works great for Go and additional language support is on the horizon.**

After developing sqlc in our spare time for the last three years, we're excited
to share that we've incorporated our company, Riza[^1], and we'll be working on
sqlc full-time!

We've got a bunch of great features planned, some of which shipped in
[v1.19.0][2]. Notably we introduced the [`sqlc vet` subcommand][9] which checks your
queries against lint expressions to catch common mistakes in otherwise syntactically-valid
SQL.

The introduction of `sqlc vet` also marks the first time that sqlc will use a
[running database server connection][8] to enhance its functionality. We'll expand on this in
future releases, but for now database connections power the built-in `sqlc/db-prepare`
vet rule.

We're really excited to get your feedback on this, so if you have thoughts about
how to make `sqlc vet` more useful please [let us know][4]!

We also created a new GitHub organization ([sqlc-dev][6]) to house existing and
future work. We've already migrated most sqlc-related repositories, but
we're waiting to move the [kyleconroy/sqlc][7] repo. The plan is to migrate it
in the next week or two. We hope this doesn't cause any disruption but some
discomfort during the transition is possible. If you've moved or renamed a Go
project on GitHub before, we would definitely appreciate your tips on how to
make this change smoothly.

We'll attempt to set a monthly cadence for new sqlc releases from now on. If you'd
like to keep up with new releases and other future sqlc developments you can
[subscribe to our newsletter][3]. 

Riza is a for-profit corporation, so we’ll be exploring monetization strategies
to sustain sqlc development. That being said, we have no intention of
changing the license of sqlc. We expect to build auxiliary services that enhance
sqlc's usefulness when you opt-in, and hopefully we'll do a good-enough job to
earn your trust as customers.

We're very open to feedback about this or anything else, so please send us a
message via email to [hello@sqlc.dev][4] or in the [sqlc Discord server][5] if
you'd like to chat.

Sincerely,
Kyle and Andrew

[^1]: The name doesn't mean anything in English but does mean ["root" in Greek][1]. We needed a name that wouldn't tie us too closely to "SQL" in case we work on other non-SQL tools in the future. It's a little confusing, we know. And honestly it's almost not worth mentioning, but we don't want you to be alarmed when you see "Riza, Inc." floating around in copyright notices.

[1]: https://en.wiktionary.org/wiki/%CF%81%CE%AF%CE%B6%CE%B1
[2]: /posts/2023/07/06/sqlc-one-point-nineteen-released
[3]: /newsletter/
[4]: mailto:hello@sqlc.dev
[5]: https://discord.gg/EcXzGe5SEs
[6]: https://github.com/sqlc-dev
[7]: https://github.com/kyleconroy/sqlc
[8]: https://docs.sqlc.dev/en/v1.19.0/reference/config.html#database
[9]: https://docs.sqlc.dev/en/stable/reference/cli.html#vet
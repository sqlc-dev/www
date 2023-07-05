---
layout: post
title:  "Working on sqlc full-time"
date:   2023-07-06 13:10:42 -0700
categories: posts
tags: [announcement]
---

After developing sqlc in our spare time since 2020, we're very excited to share that we've formed a company to support us while we work on sqlc full-time.

The company is called Riza, Inc., which doesn't really mean anything in English but does mean ["root" in Greek][1]. We needed a name that wouldn't tie us too closely to "SQL" in case we work on other non-SQL tools in the future. It's a little confusing, we know. And honestly it's almost not worth mentioning, but we don't want you to be alarmed when you see the name "Riza" floating around in copyright notices.

We've got a bunch of great features planned for sqlc, and some we've already shipped in [sqlc release v1.19.0][2]. Notably, we introduced the new `sqlc vet` subcommand which checks your queries against lint expressions to help you ensure their "correctness" beyond pure syntax. And if you configure sqlc with a connection string for a running database, you can have `sqlc vet` actaully prepare your queries against that database to report errors. We're really excited to get your feedback on this, so if you have thoughts about how to make `sqlc vet` more useful to you please let us know!

We created a new GitHub organization called [sqlc-dev][6] to house future work, and we've already moved a lot of sqlc-related repositories in. We'll be moving the main sqlc git repository in the next week or two. We hope this doesn't cause too much disruption but some discomfort during the transition is possible, maybe even likely. If you've moved or renamed a Go project on GitHub before, we would definitely appreciate your tips on how to make this as smooth as possible.

To keep up with future sqlc developments you can [subscribe to our newsletter][3].

Riza is a for-profit corporation, so naturally we'll be exploring ways to make money in order to keep sustaining the development of sqlc. But we have no intention of changing the license for sqlc itself. We expect to build adjacent services that enhance the usefulness of sqlc when you opt-in, and hopefully we'll do a good-enough job to earn your trust as customers.

We're very open to feedback about this or anything else sqlc-related, so please send us a message via email to [hello@sqlc.dev][4] or in the [sqlc Discord server][5] if you'd like to chat.

Sincerely,
Kyle and Andrew

[1]:https://en.wiktionary.org/wiki/%CF%81%CE%AF%CE%B6%CE%B1
[2]:/posts/2023/07/06/sqlc-one-point-nineteen-released
[3]:/newsletter/
[4]:mailto:hello@sqlc.dev
[5]:https://discord.gg/EcXzGe5SEs
[6]:https://github.com/sqlc-dev
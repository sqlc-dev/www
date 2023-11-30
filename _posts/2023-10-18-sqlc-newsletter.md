---
layout: post
title:  "sqlc news, October 2023"
date:   2023-10-18 12:00:00 -0700
categories: posts
tags: [news]
description: >
  We’ve opened up the sqlc Cloud dashboard for early access.
---

We’ve got a major release coming soon that we’re very excited about, because it will include a new [query analysis engine](https://github.com/sqlc-dev/sqlc/pull/2805) that resolves a ton of outstanding issues. So stay tuned for that.

To go along with that release we’ve opened up our sqlc Cloud dashboard for early access, and you can sign up here: [https://dashboard.sqlc.dev](https://dashboard.sqlc.dev/). It’s pretty barebones at the moment, but you can create an organization which will include a default project and within that project you can create auth tokens to add to your sqlc configuration as needed.

The features of sqlc which require an auth token today are [managed ephemeral databases](https://docs.sqlc.dev/en/latest/howto/managed-databases.html) (for use with sqlc vet or your own testing purposes), [project uploads](https://docs.sqlc.dev/en/latest/howto/upload.html), and [remote codegen execution](https://docs.sqlc.dev/en/latest/reference/changelog.html#remote-code-generation).

We know that adopting these new features can be confusing, so please post in the [sqlc Discord server](https://discord.gg/EcXzGe5SEs) or send us an email at [hello@sqlc.dev](mailto:hello@sqlc.dev) if you have any questions or issues getting started.

## X/Twitter and BlueSky

We’ve signed up as [@sqlcdev](https://twitter.com/sqlcdev) on X/Twitter and [@sqlc.dev](https://bsky.app/profile/sqlc.dev) on BlueSky. Go ahead and give us a follow for more frequent updates.

## GopherCon 2023

It was so nice to meet so many of you at GopherCon a few weeks ago, and to hear about how you’re using sqlc (or in one case how your corporate overlords won’t let you use sqlc). We especially appreciated Johan Brandhorst-Satzkorn’s presentation about WASM and [WASI support in Go 1.21](https://go.dev/blog/wasi), which called out sqlc’s WASM-based plugin system as a leading use-case driving his work.

## Plugins

Speaking of plugins, we’ve been working a little bit on cleaning up the plugin interface in anticipation of expanding sqlc’s language support. And we’ve seen a few new plugins in the wild that you might find useful:

* sqlc contributor @orisano wrote a [TypeScript plugin for Cloudflare’s D1 serverless database](https://github.com/orisano/sqlc-gen-ts-d1). Check out the [companion blog post](https://orisano.hatenablog.com/entry/2023/09/06/010926) for an excellent walkthrough of the sqlc plugin development process.
* And here’s a [TypeScript plugin for deno-postgres](https://github.com/4513ECHO/sqlc-gen-deno-postgres), written in Rust.

Before we go, we’d like to call out a few new proposals that could use some more community feedback. If you have a minute to read, consider and comment on whether these enhancements are useful (or not) to you and your team we’d appreciate it.

* [Type annotations for query parameters](https://github.com/sqlc-dev/sqlc/issues/2800)
* [Support composite types](https://github.com/sqlc-dev/sqlc/issues/2760)

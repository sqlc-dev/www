---
layout: post
title:  "sqlc news, November 2023"
date:   2023-11-16 11:00:00 -0700
categories: posts
tags: [news]
description: >
  We have an alpha TypeScript plugin available for testing.
---

Just a quick update this month as we head into the holiday season!

## TypeScript plugin alpha
We’re excited to have an early alpha version of our TypeScript plugin available for testing. You can see example output and follow along with developments on the [TypeScript support GitHub issue](https://github.com/sqlc-dev/sqlc/issues/296), or configure sqlc to use the plugin and let us know how it goes.

## sqlc v1.23.0 release
The end of October saw the release of sqlc version 1.23.0 which includes a new database-backed query analysis engine. This resolved nearly 100 outstanding issues in the sqlc backlog, including many of our most thorny bugs. Go read the blog post with [release notes for v1.23.0](https://sqlc.dev/posts/2023/10/24/sqlc-v1-23-0-database-backed-analyzer/), in case you missed it.

## sqlc-gen-go release
In other plugin-related news, earlier this month we [announced the release of sqlc-gen-go](https://sqlc.dev/posts/2023/11/06/publishing-sqlc-gen-go/), sqlc’s internal Go codegen package extracted for use as an external plugin. We’re excited to see how sqlc users fork and modify the plugin for use in their own projects.

## MySQL 8 ephemeral databases
And finally, we’ve enabled MySQL 8 support for managed databases in sqlc cloud. Which means you’ll be able to use an ephemeral MySQL database for `sqlc vet` and `sqlc createdb` in the next sqlc release. Sign up for [sqlc cloud](https://dashboard.sqlc.dev/) and select "MySQL" when you boot your database server to get started.

That’s all for now! As always if you have questions or comments you can reach us on [Discord](https://discord.gg/tvG5kNpu), [@sqlcdev](https://twitter.com/sqlcdev) on X/Twitter, [@sqlc.dev](https://bsky.app/profile/sqlc.dev) on BlueSky, or by email at [hello@sqlc.dev](mailto:hello@sqlc.dev).

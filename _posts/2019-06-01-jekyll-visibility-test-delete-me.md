---
layout: post
title: "TEST — delete this post (Jekyll visibility check)"
subtitle: Safe to remove after you confirm the feed is updating
tags: [test, delete-me]
readtime: true
comments: false
---

This file is only a **diagnostic**: it uses an **old** post date (`2019-06-01`) so Jekyll will not treat it as a **future** post.

If this appears on **Projects** but your newest write-ups do not, the usual cause is **`future: false`** (Jekyll default): anything dated **after** the server date at build time is skipped. Fix options:

* add **`future: true`** to `_config.yml`, or  
* use post dates **on or before** “today” in the environment that runs `jekyll build` (e.g. GitHub Actions in UTC).

Delete this file (or set `published: false`) once you are done testing.

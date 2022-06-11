---
layout: post
title:  "The `brew install` parameter `--no-quarantine`"
date:   2020-02-18 00:00:00
categories: brew mac
---

Tired of seeing the Mac Security warning dialog box each time after you installed some `Awesome.app` using `brew`? There is a (semi-)hidden parameter to fix that - `--no-quarantine`, e.g.:

`brew install --no-quarantine awesome-app`

Note:

1. This works for `brew cask install` as well.
2. It applies to those brew formulae that install `Some.app (App)` Artifacts (check with `brew info your-formulae`)

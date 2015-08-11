---
layout: post
published: true
title: GSoC Status Update - Week 7
date: 2013-08-05 20:17:00 +0200
categories:
- GSoC
tags:
- KDE
---

{% include gsoc_header.md %}

Well, this is going to be kind of a boring update. I implemented unit tests for
everything, Importers infrastructure (checking the documented properties,
testing interactions) and concrete importers alike. There were some minor design
changes but nothing interesting to talk about.

{% include image.html img="assets/2013/08/testresults.png" title="Test results in console" caption="Who doesn't like to look at successful test results?" %}

While making tests, I found and fixed a couple of small issues. So, nothing out
of ordinary, stuff simply *works*. I don't feel that the tests are necessarily
complete (and frankly, no tests are *ever* complete), but I'll still be working
on them and adding new ones when I see fit. It's still barely past the GSoC
midterm!

Oh, yeah, these tests I wrote at the beginning of the project? Not in the
current branch. These were acceptance tests for synchronization - with new
importers, it's the StatSyncing framework's responsibility and I realized that
this kind of testing specifically for importers has no sense. So, these tests
may yet see a come-back, in one form or another, but not as a part of Importers'
test suite - rather as more generic StatSyncing framework tests. It's a low
priority thing, though.

As always, you can check out my progress on my [public Amarok clone]. The branch
is named `gsoc-importers`.

Thanks for reading! **Coming up next week - Amarok 2.x synchronization!**


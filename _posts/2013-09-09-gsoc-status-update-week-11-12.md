---
layout: post
published: true
title: GSoC Status Update – Week 11 & 12
date: 2013-09-09 18:08:00 +0200
categories:
- GSoC
tags:
- KDE
---

{% include gsoc_header.md %}

Last week I asked my mentor if I could skip that week's report and make the next
one (i.e. this one) a double one instead. The reason for this was that I was
working almost exclusively on tests, and tests more often than not make for a
dull post.

Well, after two weeks I have even more tests. I'd go as far as to say that
things are satisfyingly tested.

I made a base test suite that relies on convention; importers are expected to
have tracks with certain metadata in their databases, and then tests make sure
that this data is imported correctly. Tracks with the right metadata are pre-
created and stored in the source tree so creating a test database is a matter of
adding them to media player's collection.

There isn't much more to tests that's interesting, so let's skip to the next
topic. **GSoC 2013 is in the homestretch.** September 16 is "soft pencils-down"
and September 23 a "hard pencils-down" date. We're expected to have all the code
done on September 16, and to spend the following week on documentation. Having
that in mind, this is my much-more-detailed-than-usual plan for this week:

1. **Monday** Amarok 2.x importer two-way synchronization *(done)*
2. **Tuesday** FastForward importer two-way synchronization
3. **Wednesday** Banshee & Clementine importers two-way synchronization
4. **Thursday** Rhythmbox importer two-way synchronization
5. **Friday** iTunes importer two-way synchronization[^footnote]
6. **weekend** more tests! Supplement existing test suites.

Since I've been documenting as I went, the week between September 16 and 23 will
be devoted to small bugfixes, design tweaks, typo fixes. Also that's the week
where I remove old media-player importing capabilities and take a minute or two
to celebrate.

As always, you can check out my progress on my [public Amarok clone]. The branch
is named `gsoc-importers`.

Thanks for reading!

[^footnote]: iTunes database will then have to be imported back into iTunes.
             But hey, it's synchronization. Kind of.

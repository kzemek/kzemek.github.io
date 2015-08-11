---
layout: post
published: true
title: GSoC Status Update – Week 6
date: 2013-07-29 21:29:00 +0200
categories:
- GSoC
tags:
- KDE
comments:
- id: 388
  author: Anand
  author_email: anandrkris@gmail.com
  author_url: ''
  date: !binary |-
    MjAxMy0wNy0zMCAwNDo0MzoxMCAtMDQwMA==
  date_gmt: !binary |-
    MjAxMy0wNy0zMCAwMjo0MzoxMCAtMDQwMA==
  content: ! "Glad to hear on your progress. I have used Amarok to manage my iPod
    in the past (not w/o some hassles :-) and i think it is a nifty feature to sync
    data between collections.\r\nI have a query - Is Nepomuk Collection expected to
    replace the local collection in future? Currently, i think, Amarok has it own
    DB but that may change? In such a case, everything should sync to Nepomuk?"
- id: 390
  author: Christopher
  author_email: crantila@fedoraproject.org
  author_url: ''
  date: !binary |-
    MjAxMy0wNy0zMCAwNjoyMDo0NyAtMDQwMA==
  date_gmt: !binary |-
    MjAxMy0wNy0zMCAwNDoyMDo0NyAtMDQwMA==
  content: ! "This sounds great, and I'm really excited to recover my iTunes stats
    from years ago.\r\n\r\nDo you have solid unit and integration tests? Super important
    for less-often used code like this."
- id: 391
  author: admin
  author_email: konrad.zemek@gmail.com
  author_url: ''
  date: !binary |-
    MjAxMy0wNy0zMCAxMDowMjozOCAtMDQwMA==
  date_gmt: !binary |-
    MjAxMy0wNy0zMCAwODowMjozOCAtMDQwMA==
  content: Yes, Amarok currently uses a MySQL-type database for its local collection.
    There are no plans to replace it, instead both collections (and others as well)
    will happily coexist. Also, the Statistics Synchronization framework, which my
    importers use, works with different collections out of the box.
- id: 392
  author: admin
  author_email: konrad.zemek@gmail.com
  author_url: ''
  date: !binary |-
    MjAxMy0wNy0zMCAxMDowNTowMyAtMDQwMA==
  date_gmt: !binary |-
    MjAxMy0wNy0zMCAwODowNTowMyAtMDQwMA==
  content: I've prepared an initial test suite in the first weeks of my project. I'll
    be working to adapt it to the new infrastructure now, so that they test the new
    code  instead of the old importers.
- id: 439
  author: Pete
  author_email: noone@web.com
  author_url: ''
  date: !binary |-
    MjAxMy0wNy0zMSAxOToxNjozMSAtMDQwMA==
  date_gmt: !binary |-
    MjAxMy0wNy0zMSAxNzoxNjozMSAtMDQwMA==
  content: ! "Excellent work Konrad and nice to read your weekly progress :)\r\nI
    would like to see an Clementine import / sync  in the future."
galleries:
  1:
    -
      - { url: 'assets/2013/07/ff_itunes_rhythmbox.png', alt: 'Plugins window' }
      - { url: 'assets/2013/07/ff_itunes_rhythmbox1.png', alt: 'Collections choice window' }
    -
      - { url: 'assets/2013/07/ff_itunes_rhythmbox2.png', alt: 'Statistics synchronization window' }
      - { url: 'assets/2013/07/ff_itunes_rhythmbox3.png', alt: 'Synchronizations targets window' }
---

{% include gsoc_header.md %}

This week, I was meant to implement iTunes importer, bringing the total of
created importers to two.

{% include gallery.html gallery=1 columns=2 %}

So, as you can see, I kind of overshot my goal here.

What happened is, importers are now really easy and fast to implement. The only
really piece of programming required is to retrieve track data from whatever
source (in case of both iTunes and Rhythmbox the source is an xml file), and all
the rest is just filling the blanks.

As is visible on the screenshots, the icon used for iTunes is a generic double
eight note used elsewhere in Amarok as well - it will probably stay that way.
The fact is, Apple does not grant a blanket license for usage of their logos,
and different countries have different notions of what consists of "fair use" of
such an icon. So there's that.

I also managed to implement a prototype (i.e. it's almost done) ID3v2 importer,
reading POPM (Popularimeter) frame for statistics and otherwise acting as a
wrapper for existing collection's track. I'm conflicted about its usefulness,
though, as Amarok already reads POPM on collection rescan provided that
corresponding [FMPS tags] are not set - if they are, they have precedence.
Besides initial importing, which should be doable by simply removing FMPS tags
and doing a full rescan, there's also question of updating these statistics, but
I've yet to find a prominent media player using POPM as a primary way of storing
ratings and playcounts. And for a good reason - to begin with, there's no
standard as to what different ratings mean (the values range from 1 to 255), and
POPM is an ID3v2 feature only - not that useful from media player's point of
view.

So, the future is now. Some more importer targets can definitely be expected
(Clementine? Foobar2000? I guess I'm taking requests!). Aside from that, I'm
still not done yet. Here's a short, by no means comprehensive list of what I
have yet to address:

* **code deduplication:** right now declarations for importers' classes alone
  can easily be longer than actual logic,
* **Amarok 2.x importer**,
* **robustness, error handling:** if anything, all plausible errors should cause
  a warning in the terminal (and definitely *nothing* should break),
* **tests:** this can take some time to adapt to the new framework,
* **two-way synchronization:** this may very well introduce its own set of
  problems, with synchronization and whatnot.

As always, you can check out my progress on my [public Amarok clone]. The branch
is named `gsoc-importers`.

Thanks for reading!

[FMPS tags]: http://gitorious.org/xdg-specs/xdg-specs/blobs/master/specifications/FMPSpecs/specification.txt">

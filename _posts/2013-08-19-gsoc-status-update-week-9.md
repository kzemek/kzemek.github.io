---
layout: post
published: true
title: GSoC Status Update – Week 9
date: 2013-08-19 22:52:00 +0200
categories:
- GSoC
tags:
- KDE
---

{% include gsoc_header.md %}

Last week was free of surprises. I applied some more polish to both the
Importers framework and concrete importers themselves, deduplicated some code -
just general maintenance. I had a very technical to-do list containing - mostly
- very minor entries, so it's nothing write about in the post. The bottom line
is: the overall quality of my project is improving.<

The list keeps gaining new entries, so hopefully I'll still be having plenty to
do.

One thing that continues to give me a bit of trouble is Amarok 2.x embedded
database importer. There are surprisingly many problems with managing a single
server process, shared between method calls, in a multithreaded environment.
QProcess class has its own set of quirks on top of that which need to be taken
into account, especially when it comes to destroying the object, *particularly*
in multithreaded environment. I've got ideas, but it's something for the next
post.

In other news: Banshee importer!

{% include image.html img="assets/2013/08/banshee-importer.png" title="Importers window" caption="It still has some issues, but nothing that won't be solved by this time next week" %}

Alright, that's me done for the post. It's been a short one, but as I said - no
major problems, no major changes. A steady march to high quality.

As always, you can check out my progress on my [public Amarok clone]. The branch
is named `gsoc-importers`.

Thanks for reading!

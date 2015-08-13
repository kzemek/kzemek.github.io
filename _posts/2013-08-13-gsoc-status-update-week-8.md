---
layout: post
published: true
title: GSoC Status Update – Week 8
date: 2013-08-13 21:06:00 +0200
categories:
- GSoC
tags:
- KDE
- Amarok
---

{% include gsoc_header.md %}

## My GSoC project is done!

{% include image.html img="assets/2013/08/plugins_amarok_2x.png" title="Plugins list" caption="So many plugins!" %}

Alright, not really. **But**, I already reached my hard goals and am well on the
way to finishing up on soft goals as written in my proposal: tests and two-way
synchronization. Reaching all the hard goals is something to celebrate!

## Amarok 2.x importer

{% include image.html img="assets/2013/08/add_amarok_2x_external.png" caption="External connection config" %}

The Amarok 2.x importer is up and running, and it supports both kinds of Amarok
database: external and embedded. The external one is pretty much your standard
SQL-based importer. The embedded one is *much more fun*.

{% include image.html img="assets/2013/08/add_amarok_2x_embedded.png" caption="Embedded connection config" %}

Obviously there's no daemon running when dealing with embedded Amarok database;
yet we *have* to start a MySQL server because data-mining its files is a
particularly bad idea. The most straightforward approach would be to simply run
an embedded MySQL server, but due to technical reasons we are limited to one
embedded server per process. This means that if current Amarok process stores
its collection in an embedded database - which is the default - we're quite out
of luck.

The solution I opted for is not perfect, but better than any other I could come
up with: start a server as a sub-process. The child is fed delicious arguments,
making it suitable to run in a multi-server environment, and under typical
desktop user's privileges. We then connect to the server and treat it in the
same way as external one: that's exactly what it is. The code is written in a
way that starts the server on demand and shuts it down after a period of
inactivity. Neat.

There are several drawbacks to this approach, the most obvious one being that
mysqld binary has to be present on the system. A less obvious one, but which
proved to be most troublesome, is lack of answer to a simple question: has the
server started up yet? We can't poll the output for equivalent of "ready to
serve" message, as it can be written in any language... or can it? There *may*
be another way to determine if the server is fully started, but I haven't found
one yet. So for now, after mysqld process starts, it's given a small time window
to get ready.

## The future

In addition to things outlined in previous posts, I've got a few small tasks on
my to-do list - just some overall edge-rounding and a little problem-solving.
The process-handling part of Amarok 2.x importer will also be receiving a bit
more love, and hopefully I'll find a better solution to the problem mentioned
above. I'll also be looking into implementing **Banshee importer**.

As always, you can check out my progress on my [public Amarok clone]. The branch
is named `gsoc-importers`.

Thanks for reading!

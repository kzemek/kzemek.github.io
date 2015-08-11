---
layout: post
published: true
title: Hello World! Me, the blog, and GSoC
date: 2013-06-15 22:22:00 +0200
categories:
- General
- GSoC
tags:
- KDE
---

Hey! My name is Konrad Zemek, I'm a student at AGH University of Science and
Technology, Kraków, Poland. I study Computer Science, currently finishing my
second year. I'm a *programmer*. Mainly a C++ programmer, but that's just
because I write in C++ professionally - I know a few other languages and I'm
fast to pick up new ones. I could write about this a lot more, but I guess it
all just comes to that: I'm a programmer, and a good one.

And I have other traits, too! I love gaming, particularly video gaming. I eat
through books, mostly of the fantasy kind. I ride a bicycle almost every single
day, and I'm learning to play the electric guitar, dreaming that I could someday
justify buying a [Stratocaster].

I'll get to placing my photo, not made hastily with a cellphone, somewhere
around here - in the meantime I redirect you to [my Google+ page].

## The blog

I really tried not to commit too much time to deploy this blog, as I'm behind my
schedule already. And believe me, it was hard - I'm the type of person who
spends weeks reading about color theory and typography before deploying a
website. This time, at least from the visual side, everything is pretty generic
- still, I see great many hours of tweaking and customizing this blog in my
future.

Just not now.

It's a bit more interesting from the technical side. I deployed the WordPress
application on the Amazon cloud, AWS; its code (the "application" part) resides
in a git repository, which I push to "Elastic Beanstalk" (a Platform-
as-a-Service) after every update, which in turn spins up a generic instance of
GNU/Linux and deploys the application there. Nothing on this instance is
persistent, so no actual *content* can be stored there. The blog connects to a
SQL database, which is provided by another part of AWS. Then there's persistent
content that is neither a part of the application nor it belongs in the
database, like uploaded images and other post attachments - these are stored by
Amazon S3 service. [CloudFlare] provides DNS, caching, and some anti-bot
screening.

There are a lot of things on the technical side that I'm itching to talk about,
but I've got more important thing to write about, that being...

## Google Summer of Code

The main purpose of this blog, or rather the reason it came into existence, is
to write about [Google Summer of Code], more specifically about my own [GSoC
project]. The title of this proposal is **Reimplement Amarok 1.4 (FastForward) &
iTunes importers on top of Statistics Synchronization framework, and add Amarok
2.x and Rhythmbox as synchronization targets.** [Amarok] is a legendary music
player, part of the KDE software suite (I'd say it's a Linux music player, but
that's not entirely true).

Every but the most basic music player collects data about music being played -
it's called personal metadata, and includes information like number of times a
given track has been played, or user's rating of the track. For users who use
those features, it's quite a big deal - it allows for playing favorite tracks,
or maybe the tracks which were not listened to enough, all without any need to
spend time setting up custom playlist. Or perhaps you like to share your most-
loved tracks through a service like [Last.fm]? None of that would be achievable
without personal metadata.

Currently, if you're using iTunes or an old Amarok version, you are able to
share this metadata with Amarok, although there's no easy way to keep it
synchronized. At a basic level, my project aims to add that very capability - to
easily resynchronize Amarok with iTunes, previous Amarok versions, and
Rhythmbox, Ubuntu's default music player. There are also some stretch goals,
like being able to do a two-way synchronization (update metadata on the other
player), and I'm also quite confident of reaching those. I will post my weekly
progress updates here during the course of next three months, and I think my
planned schedule will end up in a widget somewhere on this site. There also will
be more details coming. I'll get to it, I promise!

So that's me done for this post, before I run out of things to say in the next
one. Thanks for reading. :)

[Stratocaster]: http://assets.fender.com/frl/1e81a4473f5b6d3b790bee5b5e61aa7d/generated/86ceb3c861ac3d7b0737b2ccde488ca3.png
[my Google+ page]: https://plus.google.com/113823919880489418232
[Cloudflare]: https://cloudflare.com
[Google Summer of Code]: https://www.google-melange.com/gsoc/homepage/google/gsoc2013
[GSoC project]: https://www.google-melange.com/gsoc/project/details/google/gsoc2013/kzemek/5662278724616192
[Amarok]: https://amarok.kde.org
[Last.fm]: https://last.fm

---
layout: post
published: true
title: GSoC Status Update - Week 4
date: 2013-07-15 20:16:00 +0200
categories:
- GSoC
tags:
- KDE
---

{% include gsoc_header.md %}

Let me clear some things out first. Scheduled: **"Reimplementing FastForward
importer. At the end of the week I expect to have a functional importer,
possibly with some quirks to iron out. Implementing unit tests to assert
correctness."** Done: I could argue the former; the Amarok 1.4 importer *does*
work, with some quirks to iron out. The latter? Tests are untouched.

Yet this was by far the most intensive week of coding so far, and the task I
focused on is the hardest part - the *most important part* - of the whole
project. First, to give some context, let's talk about how I design programming
frameworks.

## How do I do it

I'm not much of a fan of the [waterfall model]. All of its other shortcomings
aside, I need to see how things actually *work* together, what is the overall
*feel* of the system. Designing by gut feeling may seem strange, but I'm sure
that many programmers will know what I'm talking about here; sometimes a
particular mechanism just feels awkward, particular responsibility ill-placed,
method redundant. This "methodology" is probably described and named, somewhere.
It's something like "incremental development meets rapid prototyping."

I start with a very rough draft of the system, and implement it using empty
classes, stubs, etc. (I like when things compile.) Then I pick a component -
maybe a single class, maybe a small family - and flesh it out. During this
process, I invariably become aware of multiple problems with the sketch, so I
rework it to address them. Then I pick another component, and the process
repeats. On each iteration I end up with a better design, and it can be vastly
different from the previous one.

The moral here is: as not every component is done yet, the system that I wrote
*may* undergo major changes, and definitely *will* undergo minor-ish ones. So,
on to the actual design!

## The actual design

A picture is worth a thousand words, so here's a simple class diagram (please
note that only some associations are displayed; e.g. in reality `Controller` has
some kind of association with most of shown classes). I *wrote* this diagram
using [PlantUML] - give it a look!

{% include image.html img="assets/2013/07/diagram.png" caption="Simple class diagram" %}

First, importers are now plugins. That means that they need to be registered
with the plugin framework (using a semi-convenient macro), and they need to
provide a [.desktop file] containing some meta-information. On the flip side we
get automatic loading, which can be turned on and off at user's leisure.

{% include image.html img="assets/2013/07/amarok-plugin-display.png" title="Amarok plugin display" caption="Hey, it's a plugin! (Please disregard the description)" %}

The plugin infrastructure takes care of instantiating and initializing
`Plugins::PluginFactory` class, which in case of importers is a subclass of
`ImporterFactory`, which in turn is a subclass of `ProviderFactory`. The
`ImporterFactory` and `ImporterProvider` classes are serving only as a common
base for importers, to reduce boilerplate code and duplication - all of the
relevant interface methods are specified in `ProviderFactory`.

The `ProviderFactory` contains, among others, methods returning: icon, name,
description, and configuration widget for that particular provider type. The
configuration widget is of type `ProviderConfigWidget`, which is an interface
with one method: `config()`, returning a `QVariantMap` populated with values set
by user. The returned `QVariantMap` is in turn used by another method of
`ProviderFactory`, `createProvider( const QVariantMap &config )`.

{% include image.html img="assets/2013/07/sequence.png" caption="Provider creation sequence diagram" %}

I've also extended `StatSyncing::Provider` with two methods:
`ProviderConfigWidget *configWidget()`, which returns a configuration widget for
this provider instance, if any; and `bool isConfigurable()`, which returns
`true` if provider is configurable. The reason for this is to enable
reconfiguration of importers, and putting it in a base class makes sense (in
future it could be used for Last.fm provider, for instance). The implementer of
`StatSyncing::Provider` is responsible for reconfiguring the provider instance
after the configuration is done. The question of *how* he will know that it's
done will be answered later, but reconfiguring can be done in much the same way
that initial configuration is done.

{% include image.html img="assets/2013/07/new-buttons-metadata.png" caption="New buttons, and an added synchronization target" %}

As I mentioned, `ImporterProvider` and `ImporterFactory` serve to reduce
boilerplate. They have some sane defaults which greatly reduce amount of code
needed to implement new providers, and I hope to reduce it yet further. For
starters, `ImporterProvider` by default delegates queries for description, icon,
configuration widget, to corresponding `ImporterFactory`. That means many
attributes need only be set in the `ImporterFactory` subclass, and not in the
Provider, and it will just work. While the reconfiguration part is not yet
programmed in, it will be done automatically by the framework; the same
`ProviderConfigWidget` will be used, prepopulated by `ImporterFactory`. The
factory will then create new provider using the modified config, and
transparently swap it with the old provider, immutable-style.

## Current status

### What works

* FastForward Provider
* registering plugins
* provider creation dialog, complete with creating new provider
* loading up configuration at startup

### What doesn't

* saving configuration at shutdown
* reconfiguring provider
* forgetting provider
* dialogs being pretty - there's a reason for the lack of configuration dialogs'
  screenshots
* FastForward Provider - the tracks are found but displayed as unique, even
  though the metadata seems identical

### Plans for the future

Obviously my schedule is now a bit skewed, as by now I was supposed to finish up
FastForward Provider complete with tests, and start implementing iTunes
Provider. Right now the plan is to finish up the importers framework, so
everything that's needed is *usable* - doesn't have to be perfect yet. If things
will go as I anticipate, implementing iTunes Provider should be a breeze, and
creating every subsequent provider even easier.

As always, you can check out my progress on my [public Amarok clone]. The branch
is named `gsoc-importers`.

Thanks for reading!

[waterfall model]: https://en.wikipedia.org/wiki/Waterfall_model
[PlantUML]: http://plantuml.com
[.desktop file]: http://standards.freedesktop.org/desktop-entry-spec/desktop-entry-spec-latest.html

---
layout: post
published: true
title: GSoC Status Update - Week 5
date: 2013-07-22 12:06:00 +0200
categories:
- GSoC
tags:
- KDE
- Amarok
galleries:
  1:
    -
      - { url: 'assets/2013/07/pluginEnabled.png', caption: 'One plugin now, three left to go' }
      - { url: 'assets/2013/07/metadataPluginDisabled.png', caption: 'The plugin is disabled – related providers are disconnected. Note that the "Add" button is grayed out' }
    -
      - { url: 'assets/2013/07/metadataPluginEnabledConfigure.png', caption: 'The plugin is now enabled. The "Add" button is enabled as well, and we can configure selected provider' }
      - { url: 'assets/2013/07/createTargetDialogMySQL.png', caption: 'Provider creation dialog – configure the new provider' }
    -
      - { url: 'assets/2013/07/configureDialog.png', caption: 'The provider configuration dialog' }
      - { url: 'assets/2013/07/createTargetDialog.png', caption: 'Provider creation dialog – choose provider type' }
    -
      - { url: 'assets/2013/07/synchronizeDialog.png', caption: 'StatSyncing Synchronization dialog, choose providers to synchronize' }
      - { url: 'assets/2013/07/synchronizeDialog2.png', caption: 'StatSyncing Synchronization dialog – it works! Disclaimer: song ratings are test values' }
---

{% include gsoc_header.md %}

I'm happy to report that every component of the Importers infrastructure is now
working. There were no major design changes from what I described in my [last
status update]. The most significant minor-ish ones are:

* provider creation and configuration dialogs management was moved from
  `StatSyncing::Controller` to the GUI (but the `Controller` is still tasked
  with instantiating the dialog),
* `ImporterFactory` was renamed to `ImporterManager` to better reflect its
  responsibilities.

Otherwise, I implemented what remained to be implemented, squashed some small
bugs and added some polish. What I like to call "Importers framework" (but what
is probably too monumental of a name) is ready for service, although it will be
polished and improved still throughout the summer.

## 8076 words

{% include gallery.html gallery=1 %}

## The future

This week I'm going to start and finish implementing iTunes importer. After that
I'm going to spend some time on tests, repurposing them for the new Importers
framework. If most things go well, everything that I have planned should be
ready for the GSoC Midterm Evaluation, along with some things I haven't (i.e.
the "framework").

After that, well, I don't know yet. I will get a taste of implementing a
concrete importer this week, and will see how much work it requires right now.
Depending on that, more importing targets than just Amarok 2.x and Rhythmbox may
be expected from the second half on my project.

As always, you can check out my progress on my [public Amarok clone]. The branch
is named `gsoc-importers`.

Thanks for reading!

[last status update]: {% post_url 2013-07-15-gsoc-status-update-week-4 %}

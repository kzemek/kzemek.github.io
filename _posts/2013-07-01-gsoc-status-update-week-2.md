---
layout: post
published: true
title: GSoC Status Update - Week 2
date: 2013-07-01 23:52:00 +0200
categories:
- GSoC
tags:
- KDE
- Amarok
---

{% include gsoc_header.md %}

I didn't think writing tests could be so much fun! Neither did I think they
could take me so much time. What I scheduled for the past week was
**"implementing the initial test suite for importers (exam week),"** and it was
supposed to be a fast & easy task, allowing me to focus on my exams. And yet
after my last test that week I found myself spending every day intensively
coding. Most of the time was spent on figuring out how to approach testing and
writing the code to make that chosen approach *possible*; writing tests
themselves actually *was* the fast & easy task I thought it to be.

At the end, I ran `git diff --stat` for my changes: `26 files changed, 1246
insertions(+), 69 deletions(-)`. I was surprised.

I managed to test things the way I wanted to. I created two test suites,
`TestITunesImporter` and `TestFastForwardImporter`, both inheriting from
`TestImportersCommon`, which holds common tests and utility functions. I
*slightly* modified both importers so that I could statically provide them with
arbitrary database paths to read from and arbitrary collections to write to -
one of my goals for this week was *not to modify existing importers in any major
way.

I created two collections: `localCollection` and `fileCollection`; the latter
fulfilling a role of `FileTrackProvider`, giving importers information about
tracks located at nonexistent paths; the former being the collection tracks were
synchronized to. To easily check, clear, and modify their contents I extended
capabilities of `Collections::CollectionTestImpl` class, which stores collection
in-memory. The 'init' and 'clean' test procedures take care of filling the
`fileCollection` with necessary data, clearing `localCollection`, and resetting
statistics after each test.

The code has no idea which importers it's dealing with, operating on
`DatabaseImporter`, a superclass of both. The tests *don't know even that*. A
test sets preconditions by modifying collections' contents, calls
`blockingImport()` method of `TestImportersCommon`, and then checks the
resulting state. An elegant, implementation-agnostic way, which fulfills my
second goal for the week: *write tests for current importers in a way that would
make them easily reused for reimplemented importers*. Done and done.

So here's something I learned that week: how to run an asynchronous task in Qt
and block until it's done. That's a useful thing for testing! Here's the full
implementation of `blockingImport()`:

```c++
void
TestImportersCommon::blockingImport()
{
    QScopedPointer<DatabaseImporter> importer( newInstance() );
    QEventLoop loop;

    connect( importer.data(), SIGNAL(importSucceeded()), &loop, SLOT(quit()), Qt::QueuedConnection );
    connect( importer.data(), SIGNAL(importFailed()), &loop, SLOT(quit()), Qt::QueuedConnection );
    connect( importer.data(), SIGNAL(importError(QString)), &loop, SLOT(quit()), Qt::QueuedConnection );
    connect( importer.data(), SIGNAL(importFailed()), this, SLOT(importFailed()) );
    connect( importer.data(), SIGNAL(importError(QString)), this, SLOT(importFailed()) );

    importer->startImporting();
    loop.exec();
}
```

And this is as personal as it gets when it comes to interacting with importers
in tests. (As you can see, I'm playing with a new plugin for code snippets.
Configuring it fully is yet another thing I have planned for later.)

You can check out my progress on my [public Amarok clone]. The branch is named
`gsoc-importers`.

Thanks for reading!

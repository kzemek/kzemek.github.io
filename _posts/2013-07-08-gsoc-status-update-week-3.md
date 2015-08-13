---
layout: post
published: true
title: GSoC Status Update – Week 3
date: 2013-07-08 15:02:00 +0200
categories:
- GSoC
tags:
- KDE
- Amarok
---

{% include gsoc_header.md %}

For the previous week I had scheduled **"Reimplementing FastForward importer,
cleaning up data-reading module. At the end of the week I expect to have working
implementation of FastForwardProvider."** That's done now, and I can't say it
took a lot of coding.

It took a fair amount of thinking, though. Classes that inherit
`StatSyncing::Provider` and `StatSyncing::Track` have to fulfill certain
conditions for working in multi-threaded environment. In short, the former must
be reentrant, and the latter thread-safe.

Surprisingly, there are various definitions of reentrancy and thread-safety.
[The Qt's one][Qt reentrancy] basically boils down to this: a class is reentrant
when different threads can safely work on different objects, and thread-safe
when they can work on the same object. [Wikipedia disagrees][Wikipedia
reentrancy]. In it's definition, a method is reentrant when it can be
interrupted in the middle and started again. In that sense, the function in the
following example (courtesy of Wikipedia) is thread- safe, but not reentrant; an
invocation interrupted in the function body would leave the mutex locked, and
any subsequent invocation would hang on `mutex_lock()`.

```c++
int function()
{
    mutex_lock();
    // ...
    function body
    // ...
    mutex_unlock();
}
```

But leaving this small tangent aside, that wasn't the only thing I had to think
through. I had a small case of premature optimization going on: I didn't want to
open a new SQL connection for every request (Amarok stores it's main
collection's data in SQL database). It sounds simple enough to do, but coupled
with the fact that ["a connection can only be used from within the thread that
created it"][Qt reentrancy], and multithreaded nature of providers, this was an
interesting problem to get right. So I rediscovered what every other Qt
developer already knew: **if you need something to run in a specific thread, use
Signals & Slots mechanism**. With `Qt::BlockingQueuedConnection` and using `emit
foo()` instead of `slotFoo()`, we basically get an effect of executing a
function - synchronously - in a different thread, and with an added benefit of
not having to bother with synchronization ourselves.

It gets a little bit more awkward when both caller and callee can happen to be
in the same thread, as `Qt::BlockingQueuedConnection` blocks caller's thread
until called slot is done:

```c++
    connect( this, SIGNAL(retrieveAllData()), SLOT(slotRetrieveAllData()), Qt::BlockingQueuedConnection );
    // ...
    if( thread() == QCoreApplication::instance()->thread() )
        slotRetrieveAllData();
    else
        emit retrieveAllData();
```

But hey, the pros outweigh the cons, and it's what matters. And after I was done
with `FastForwardProvider` and `FastForwardTrack`, I decided to add some lazy
initialization to the mix...

So, to wrap this up. Aside from multi-threaded fun and figuring out the right
file system hierarchy, I also spent a lot of time thinking about the bigger
picture, that is how and when importers would actually load up the configuration
and register themselves with right controllers. It's something I initially
overlooked in my proposal, and something I'll try to address this week, possibly
skewing my schedule a little bit. More on this topic coming next week.

Oh, and I'm not done with `FastForwardProvider`, not really. It's more of a
prototype. As more providers come to life, they will see a lot of changes aiming
at deduplication and easing the creation of new ones.

Thanks for reading!

[Qt reentrancy]: http://qt-project.org/doc/qt-4.8/threads-reentrancy.html
[Wikipedia reentrancy]: https://en.wikipedia.org/wiki/Reentrancy_%28computing%29

---
layout: post
published: true
title: GSoC Status Update - Week 1
date: 2013-06-24 20:20:00 +0200
categories:
- GSoC
tags:
- KDE
comments: []
---

{% include gsoc_header.md %}

Long days and pleasant nights.

The first week of GSoC has passed. What I had scheduled for that week (and I'm
still getting around to placing the schedule around here) was "Drafting up test
cases for FastForward and iTunes importers."

I admit that it doesn't sound too exciting, and neither does the next one which
is all about implementing these drafted tests. The reason for that is that I'm
currently in the middle of final exams; as of writing this posts, three of them
are behind me and three are left to go. The last exam is July 2nd, so all of the
real fun will start after that.

So, let's write about what I actually did that week, which amounts to this: I
went through the current importers implementation and noted what they currently
do. I then compared this with what the reimplemented importers should do, and
then noted on my whiteboard what kind of thing I should then be testing. I also
familiarized myself with QTestLib and KDE helper methods for testing and
prepared a blank test suite, so I'm basically all set to implement things. And
that's about all.

I want to write a bit about *why* I want to implement these tests at all. Their
purpose isn't to test the implementation of current importers, because they're
being replaced anyway. What I want to do is assert how the logic works right
now, and then carry on these assertions to the new implementation. The bottom
line is: they'll make sure that the new implementation is no worse than the old
one, which is one of my project's goals. :)

Signing off before I write everything I have about tests, and then have nothing
to write about next week. Thanks for reading!

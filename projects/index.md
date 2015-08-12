---
layout: page
title: Projects
---

## libmacaroons-erlang <sup>[<span class="octicon octicon-logo-github" title="libmacaroons-erlang repository on GitHub"></span>][libmacaroons-erlang]</sup>

An Erlang wrapper for [libmacaroons], a library which provides an implementation
of [macaroons]. Macaroons are flexible authorization tokens that work great in
distributed systems. Like cookies, macaroons are bearer tokens that enable
applications to ascertain whether their holders' actions are authorized. But
[macaroons are better than cookies!]

## libmacaroons-cpp <sup>[<span class="octicon octicon-logo-github" title="libmacaroons-cpp repository on GitHub"></span>][libmacaroons-cpp]</sup>

A C++11 wrapper for [libmacaroons].

## Erlang TLS <sup>[<span class="octicon octicon-logo-github" title="erlang_tls repository on GitHub"></span>][erlang_tls]</sup>

Created as a part of my employment (© [ACC Cyfronet AGH]), Erlang TLS is an
alternative implementation of IP and TLS layers available in Erlang
distribution.

Erlang TLS is a C++ extension for Erlang, written to take full advantage of
hardware acceleration of AES encryption. The implementation achieves **up to 8
times higher bandwidth than Erlang's `gen_ssl`-based stack** on a single core,
and scales well with multiple cores.

README and documentation coming soon!

## Razer DeathAdder Chroma driver for razercfg <sup>[<span class="octicon octicon-logo-github" title="hw_deathadder_chroma.c file on GitHub"></span>][hw_deadhadder_chroma.c]</sup>

A low-level hardware access for the Razer DeathAdder Chroma mouse built on
[libusb]. The driver was created (and is merged into) for [razercfg], an open
source Razer device configuration tool for Linux.

## TCP/IPv6 stack <sup>[<span class="octicon octicon-logo-github" title="TCPIPv6 repository on GitHub"></span>][TCPIPv6]</sup>

An implementation of TCP/IPv6 stack intended to be portable between different
hardware and operating systems by implementing a set of low-level functions. An
implementation for x86 Linux-based platform is provided as an example.

The stack was created as an academic research project. It doesn't implement the
whole TCP/IPv6 specification - particularly no optional IPv6 headers are
supported - and the TCPv6 implementation is incomplete (although functional).
The code is not intended for production use.

## Dice <sup>[<span class="octicon octicon-logo-github" title="Dice repository on GitHub"></span>][Dice]</sup>

Dice library is a small, high-performant, thread-safe, idiomatic and unit-tested
implementation of gaming dice written in C++11.

[libmacaroons-erlang]: https://github.com/kzemek/libmacaroons-erlang
[libmacaroons]: https://github.com/rescrv/libmacaroons
[macaroons]: http://research.google.com/pubs/pub41892.html
[macaroons are better than cookies!]: https://github.com/kzemek/libmacaroons-erlang#macaroons-are-better-than-cookies
[libmacaroons-cpp]: https://github.com/kzemek/libmacaroons-cpp
[erlang_tls]: https://github.com/kzemek/erlang_tls
[ACC Cyfronet AGH]: http://www.cyfronet.krakow.pl/en
[hw_deadhadder_chroma.c]: https://github.com/mbuesch/razer/blob/master/librazer/hw_deathadder_chroma.c
[libusb]: http://www.libusb.org
[razercfg]: http://bues.ch/cms/hacking/razercfg.html
[TCPIPv6]: https://github.com/kzemek/TCPIPv6
[Dice]: https://github.com/kzemek/Dice

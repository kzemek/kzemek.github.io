---
layout: post
published: true
title: Asio, SSL, and scalability
date: 2015-08-16 15:06 +0200
categories:
- Tech
tags:
- Asio
- C++
- SSL
- concurrency
charts:
  1:
    type: Line
    options:
      datasetFill: false
      multiTooltipTemplate: '<%=value%> MB/s'
    data:
      labels: ['1 thread', '2 threads', '3 threads', '4 threads', '5 threads', '6 threads', '7 threads', '8 threads']
      datasets:
        -
          label: '1 connection'
          data: [459.074, 632.431, 675.63, 672.179, 668.852, 672.405, 673.355, 670.691]
          strokeColor: '#1f77b4'
          pointColor: '#1f77b4'
        -
          label: '4 connections'
          data: [457.206, 723.17, 447.107, 205.415, 196.04, 194.989, 192.864, 193.013]
          strokeColor: '#ff7f0e'
          pointColor: '#ff7f0e'
        -
          label: '10 connections'
          data: [457.624, 723.327, 424.106, 207.065, 196.052, 192.404, 193.633, 194.382]
          strokeColor: '#2ca02c'
          pointColor: '#2ca02c'
  2:
    type: Line
    options:
      datasetFill: false
      multiTooltipTemplate: '<%=value%> MB/s'
    data:
      labels: ['1 thread', '2 threads', '3 threads', '4 threads', '5 threads', '6 threads', '7 threads', '8 threads']
      datasets:
        -
          label: '1 connection'
          data: [482.439, 832.224, 931.793, 925.669, 925.754, 933.358, 867.83, 834.028]
          strokeColor: '#1f77b4'
          pointColor: '#1f77b4'
        -
          label: '4 connections'
          data: [479.616, 913.242, 1229.86, 1190.19, 1124.86, 942.596, 891.663, 864.902]
          strokeColor: '#ff7f0e'
          pointColor: '#ff7f0e'
        -
          label: '10 connections'
          data: [504.566, 937.998, 1375.89, 1332.98, 1350.26, 1280.41, 1057.75, 977.517]
          strokeColor: '#2ca02c'
          pointColor: '#2ca02c'
  3:
    type: Line
    options:
      datasetFill: false
      multiTooltipTemplate: '<%=value%> MB/s'
    data:
      labels: ['1 thread', '2 threads', '3 threads', '4 threads', '5 threads', '6 threads', '7 threads', '8 threads']
      datasets:
        -
          label: 'thread-per-core w/ OpenSSL'
          data: [443.012, 691.563, 422.498, 193.02, 190.523, 185.653, 189.007, 191.928]
          strokeColor: '#1f77b4'
          pointColor: '#1f77b4'
        -
          label: 'thread-per-core'
          data: [480.152, 926.183, 1288.33, 1221.75, 1192.29, 1097.48, 890.274, 875.254]
          strokeColor: '#ff7f0e'
          pointColor: '#ff7f0e'
        -
          label: 'io_service-per-core'
          data: [493.772, 779.195, 1286.59, 1375.71, 1502.46, 1687.27, 1821.33, 1620.35]
          strokeColor: '#2ca02c'
          pointColor: '#2ca02c'

---

Let's say you want to build an SSL server in C++. Transmitting data is going to
be a major part of your application, so you need it to be fast and efficiently
use system resources, especially processor cores.

You may have heard of [Asio] \(possibly better known as [Boost.Asio]). Asio is a
"cross-platform C++ library for network and low-level I/O programming that
provides developers with a consistent asynchronous model". It's widely used and
mature, and in the future [may be a part of C++17 standard library][n4370]. Note
that this post is not a tutorial or an introduction to Asio, but rather a study
on how scalable it is in our use case, why it scales poorly and how to improve
it.

## Benchmarking

Asio includes SSL support using [OpenSSL] library. I've created an [example,
"naive" benchmark][benchmark_naive] that sets up a server with a given number of
threads, creates a given number of connections and measures the time it takes
each of them to send `M` messages of size `N`. The code uses Asio 1.10.6, which
is the current stable version. Here's how I compile it on OS X:

```sh
$ clang++ -std=c++11 -O3 benchmark_naive.cpp -o benchmark_naive \
          -Iasio-1.10.6/include/ -I/usr/local/opt/openssl/include/ \
          -L/usr/local/opt/openssl/lib/ -lssl -lcrypto
```

And here are example benchmark results on my machine (that has 4 cores with
Hyper-threading, adding up to 8 virtual cores):

```sh
$ # 1 thread, 1 connection, 1000 messages * 10 MB
$ ./benchmark_naive 1 1 1000 $((10 * 1024 * 1024))
10000 megabytes sent and received in 22.392 seconds. (446.588 MB/s)
$ # 8 threads, 4 connections, 250 messages * 10 MB
$ ./benchmark_naive 8 4 250 $((10 * 1024 * 1024))
10000 megabytes sent and received in 52.821 seconds. (189.319 MB/s)
```

Let's put all of my results into a chart:

{% include chart.html id=1 %}

As you can see, the chart shows an increase in bandwidth for two threads, which
we would expect even for a single connection (as one endpoint has to encrypt,
and other decrypt the data). After that point, **the bandwidth rapidly falls off
with the number of threads**, reaching about 200 MB/s when used with multiple
connections. Connections can potentially run in parallel, so the ideal chart
would show a linear speedup with the number of threads, capping at the ratio of
2 threads/connection.

Summarizing the experiment, Asio with OpenSSL not only does not scale with the
number of threads, it actually *slows down considerably* when the number of
concurrent operations is high enough. This suggests a heavy lock congestion.
Let's check our theory in a profiler:

{% include image.html img="assets/2015/08/asio_profile.png" title="Profiling results" caption="Inverted call tree" %}

Most of the time of our application is spent waiting for locks, most of the
locking occurs in `ERR_get_state` OpenSSL function, and it can most often be
found in a call tree of `asio::ssl::detail::engine::perform`. A look at
`engine::perform` implementation in [engine.ipp] quickly shows us that...
everything is fine. Sure, you can maybe make a few small optimizations, but in
the larger picture OpenSSL is used correctly. There's no error there that would
result in the lock congestion. We conclude that the **bottleneck in scalability
lies in OpenSSL itself**, more specifically in its error handling functions. So
what now?

## Trying BoringSSL

After [Heartbleed], a few OpenSSL forks like [LibreSSL] and [BoringSSL] have
appeared with a vision to trim down, modernize and secure OpenSSL's code while
remaining mostly source-compatible with the original. A quick look at
[LibreSSL's err.c] file shows it's largely unchanged from the [OpenSSL's
version][OpenSSL's err.c]. But [BoringSSL's err.c] has obviously been reworked
and now **uses a thread-local storage instead of locking a global mutex data
access**!

As I mentioned, BoringSSL is *mostly* source-compatible with OpenSSL.
Unfortunately, there are [a few changes to make][asio_boringssl.patch] before we
can use it with Asio. There are [open][Asio #52] [issues][Asio #74] in Asio
GitHub repository to integrate these changes into upstream, but even then
compatibility with BoringSSL is currently a moving target as the code is still
being cleaned up.

Let's compile the example with BoringSSL and run our benchmarks:

```sh
$ clang++ -std=c++11 -O3 benchmark_naive.cpp -o benchmark_naive \
          -Iasio-1.10.6/include/ -Iboringssl/include/ \
          -Lboringssl/build/ssl/ -Lboringssl/build/crypto/ \
          -lssl -lcrypto
$ # 8 threads, 4 connections, 250 messages * 10 MB
$ ./benchmark_naive 8 4 250 $((10 * 1024 * 1024))
10000 megabytes sent and received in 11.591 seconds. (862.738 MB/s)
```

{% include chart.html id=2 %}

Well, that's *much better*. You may end reading here, knowing that as of this
time OpenSSL's error handling causes it to scale poorly with the number of
threads, while **BoringSSL scales much better indeed**. But there's still a
fall-off in bandwidth when the number of threads increases, so let's try to
answer that as well.

## Cores, threads and io_services

If you want to find a bottleneck, profiling is usually the best answer:

{% include image.html img="assets/2015/08/asio_boringssl_profile.png" title="Profiling results with BoringSSL" caption="Inverted call tree with BoringSSL" %}

Looks like Asio's internal thread synchronization is the bottleneck this time.
There are two main approaches to get scalability in Asio: `thread-per-core` and
`io_service-per-core`. In the "naive" example I'm using the `thread-per-core`
approach as it always seemed more natural to me - we're basically creating a
threadpool that - if needed - can dedicate one or more threads to a single
connection, as opposed to `io_service-per-core` where each connection would be
served by at most one thread. But in the light of our profiling results I've
[modified the example to use the second approach][benchmark_asio.cpp].

I've added a new class, `IoServices`, objects of which hold multiple
`io_service`s. When we need an `io_service` - e.g. for a new client- or
server-side socket - we call `ioServices.get()` which will return one of the
stored `io_service` objects on a round-robin basis.

Let's put the results into a final chart. This time we'll just focus on 8
threads, 20 connections, and directly compare our different benchmark
applications. Note that I'm cheating here, if just a little bit:
`io_service-per-core` will perform better when there are multiple connections
per `io_service`, as it will result in a more balanced CPU load. Since we're
considering a server scenario, though, 20 connections is still a very low
number.

{% include chart.html id=3 %}

This still isn't the ideal chart (of course, due to threads' overhead no
implementation can exist that would produce the ideal results), but it actually
**scales up with the number of used threads**.

That's it for the post. Now that you know how to make Asio-based SSL server
scale, you can go build a faster and safer applications.

Thanks for reading!

[Asio]: https://think-async.com
[Boost.Asio]: http://www.boost.org/doc/libs/1_58_0/doc/html/boost_asio.html
[n4370]: http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/n4370.html
[OpenSSL]: https://www.openssl.org
[benchmark_naive]: https://gist.github.com/kzemek/9f7a8d94171197a9f88e
[engine.ipp]: https://github.com/chriskohlhoff/asio/blob/asio-1-10-6/asio/include/asio/ssl/detail/impl/engine.ipp#L233
[Heartbleed]: https://en.wikipedia.org/wiki/Heartbleed
[LibreSSL]: http://www.libressl.org
[BoringSSL]: https://boringssl.googlesource.com/boringssl
[LibreSSL's err.c]: https://github.com/libressl/libressl/blob/ba04546060b835e279c0e8a26261e73a1964927c/src/crypto/err/err.c
[OpenSSL's err.c]: https://github.com/openssl/openssl/blob/OpenSSL_1_0_2d/crypto/err/err.c
[BoringSSL's err.c]: https://boringssl.googlesource.com/boringssl/+/74279b63428d9b25052207dd81121b67a847c20e/crypto/err/err.c
[asio_boringssl.patch]: https://gist.github.com/kzemek/37aa2a2138b2651f2c55
[Asio #52]: https://github.com/chriskohlhoff/asio/issues/52
[Asio #74]: https://github.com/chriskohlhoff/asio/issues/74
[benchmark_asio.cpp]: https://gist.github.com/kzemek/166e2af5f799d4f833a3

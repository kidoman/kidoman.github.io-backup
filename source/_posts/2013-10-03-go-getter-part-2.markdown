---
layout: post
title: "Go Getter Part 2"
subtitle: "Now with C++ optimizations"
date: 2013-10-03 04:30
comments: true
category: programming
tags:
  - business card
  - c++
  - go
  - golang
  - optimization
  - performance
  - raytracer
  - raytracing
---

__*Update: __ I have now posted a [second follow up article](/programming/go-getter-part-3.html) with the benchmarks rerun with a multi-threaded optimized C++ version

Apples? Oranges?
---

This is a follow up article to the initial [Go Getter](/programming/go-getter.html) article which focused purely on optimizing the Go solution. The comparision was not apples-to-apples (and still isn't; as we are talking about two very different platforms here) and was never meant to be. Instead it was focused on:

* Learn idiomatic Go
* Document optimizations which help make the Go solution faster
* Fire up all cylinders (erm cores) and see how the performance scales
* Discover any avenues of optimizing Go further (after all, we are just at version 1.2rc1)
* Fun?

It was never meant to mislead people into believing that Go was faster than a fully optimized C++ solution, or to deceive people into adopting Go as a result. Since its been a while (7 years) I went knee deep into C++, I had left it upto more experienced hands to properly optimize the C++ version. Evidently, it was wishful thinking.

Full Steam Ahead
---

I spent the last couple of hours applying the optimizations learnt from the Go story to the C++ version: [diff of the optimizations](https://github.com/kid0m4n/rays/compare/bbb8395aa999883a595267fd0230087b1ddf646c...940c91f601ef840e6d75ddf272ab6cd3eb8d5531)

Needless to say, the C++ performance is **exciting** again. Mind you, although I tried using OpenMP to bring in some multi-threaded love, it didn't work out so well. So I will truly have to leave that upto more capable hands.

{% img /images/go-vs-cpp-after-both-optimized.png "Go vs C++ after both are optimized" %}

*It was compiled by "c++ -O3" using G++ 4.7.3 and benchmarked on a Core i7 2600 16 GB dedicated Hetzner server running an updated Ubuntu 13.04 installation*

Road Ahead
---

I hope to takes these numbers to the Go community and try and close the gap as much as possible. Go suffers from relatively slower performance because it tries to be as safe as possible when used in a concurrent scenario (for example, the default "global rand" is synchronized and good to access from multiple goroutines.) That is something I would definitely desire when doing real world coding. There is definitely scope for improvement, but considering everything else (GC, compilation speed, goroutines, channels, etc.) that Go brings to the table, I guess it will always be a game of balance.

As usual, reachable at kidoman@gmail.com / karanm@thoughtworks.com / [@kid0m4n](https://twitter.com/kid0m4n)

[Reddit discussion thread](http://www.reddit.com/r/golang/comments/1nlgbq/business_card_ray_tracer_go_faster_than_c/)

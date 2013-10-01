---
layout: post
title: "Golang: testing the waters"
date: 2013-07-26 02:44
comments: true
category: engineering
tags: go golang talk
---

![Gopher](//1-ps.googleusercontent.com/x/s.golang-org.appspot.com/golang.org/doc/gopher/xfrontpage.png.pagespeed.ic._-JkwHsaKq.webp)

[Go](http://golang.org/) has caught my fancy. There, I said it.

The Rubyist in me loves the static duck typing. Loves the promise of never having to navigate 'murdered by design-patterns' code (AbstractFactoryFactory anyone?) Loves the near C/C++ speeds (ok, I know Ruby is not the fastest language, but I can always wish, right?) Loves the ability to compile large code bases in seconds. Loves the channels and the freedom they provide in implementing good/crisp CSP.

Sure, there are a few rough edges but nothing sharp enough to stop me from proposing to build the next enterprise application in Go. I mean, if Google betting YouTube and their primary [download](http://www.oscon.com/oscon2013/public/schedule/detail/28669) [service](http://talks.golang.org/2013/oscon-dl.slide#1) on it isn't precedence enough, I don't know what can be.

I do not want ThoughtWorks to be late to the party ([again](http://nodejs.org/).) The time is ripe; the community is still taking shape and extremely malleable (in a good way.) So if a little/lot/hardcore evangelizing is what it takes, I am up for the challenge.

Link to the talk
================

[click](http://goo.gl/geuWOP)

The Talk
========

The talk was never intended to be a hands on session (although it transfigured into one towards the end.) More "daze and amaze." I think I succeeded on the first part at least.

The Amaze
---------

I wanted to highlight the best bits of Golang from the start. No stringing along for 40 slides whilst pretending to know a lot more than I did.

* Static duck typing
* No explicit inheritance + focus on composing
* Pointers for efficient access
* Built in language support for channels
* Super fast compilation
* Garbage collection
* Gofmt (no more arguments about indentation)

The Daze
--------

Go will probably make inroads faster into some areas vs others. Main areas of focus are:

* Creating a highly efficient backend service based on RPC / JSON RPC
* Best possible platform for implementing an API end point
* Load balancer / database type applications
* (world is Go's oyster)

Testing Story
-------------

The core members of Go are ensuring that people do not associate Go with not having to test their code. In fact, the best place to learn testing in Go is to browse through the implementation of Go packages (written by the creators of Go themselves.)

* ["testing"](http://golang.org/pkg/testing/) package makes testing really simple; some may say the approach taken is naive
* [GoSpec](https://github.com/orfjackal/gospec) aims to solve that. Provides a BDD type testing interface for Golang
* Speed of running tests makes the whole process a joy

Deployment
----------

Deployment couldn't be easier. Go spits out a statically linked executable, making dependency management a thing of the past. Package up your executable into a RPM/DEB/what-not and throw it up. Couldn't be simpler.

Conclusion
----------

There are still nascent areas of Go which will require attention from the community to gain traction and get smoothened out. However this shouldn't stop us from deploying Go as a API backend for a RoR powered AngularJS based single page application. Use the best bits of the various platforms to get going in the fastest manner possible. Throw the rot out as better Go based alternatives become available. :)

Resources
---------

Listing the various resources and good to read links (in no particular order):

* [CSP](http://www.usingcsp.com/) ([wiki](http://en.wikipedia.org/wiki/Communicating_sequential_processes)), a must read for understand CSP
* [Official homepage](http://golang.org/)
* [A web based Go runner](http://play.golang.org/)
* [Official blog roll](http://blog.golang.org/)
* [MongoDB Going with Go](http://blog.mongodb.org/post/60359054233/going-with-go)
* [Google Tech Talks YouTube channel](http://www.youtube.com/channel/UCtXKDgv1AVoG88PLl8nGXmw)
* [Seli](https://github.com/diptanu/seli) (written by Diptanu)
* [GoSpec](https://github.com/orfjackal/gospec), BDD in Go
* [GroupCache](https://github.com/golang/groupcache), a distributed hybrid client/server memcache alternative
* [Awesome language metrics by Github for Go](https://github.com/languages/go)
* [Brad Fitzpatrik's Github Profile](https://github.com/bradfitz)
* [Golang Github Profile](https://github.com/golang)
* [Docker](http://www.docker.io/) an awesome LXC management tool ([github](https://github.com/dotcloud/docker))
* [Heka](https://github.com/mozilla-services/heka)
* [Flynn](https://flynn.io/), a Docker based OSS Heroku clone
* [Revel](http://robfig.github.io/revel/), emerging defacto Go web framework
* [Techempower Benchmarks](http://www.techempower.com/benchmarks), comparing Go's performance to various other web frameworks
* [dl.google.com in Go](http://talks.golang.org/2013/oscon-dl.slide#1), slides from Brad Fitzpatrik's talk at oscon about reimplementing dl.google.com in Go
* [Go running on a Cubieboard2](http://dave.cheney.net/2013/08/06/go-1-1-on-the-cubieboard-2)
* Must watch videos on YouTube
  * [Go Concurrency Patterns](http://www.youtube.com/watch?v=f6kdp27TYZs)
  * [Tour of Go](http://www.youtube.com/watch?v=MzYZhh6gpI0)
  * [Go in Production](http://www.youtube.com/watch?v=kKQLhGZVN4A)

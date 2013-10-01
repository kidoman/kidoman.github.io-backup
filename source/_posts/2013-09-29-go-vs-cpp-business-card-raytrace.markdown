---
layout: post
title: "Go vs C++: Business Card Raytracer"
date: 2013-09-29 14:38
published: false
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

{% img /images/42.png A Raytraced Image %}

Few days ago, I chanced upon [this blog post](http://fabiensanglard.net/rayTracing_back_of_business_card/index.php) on [Hacker News](https://news.ycombinator.com/news).

I loved the breakdown which the author had provided, and frankly speaking, since the subject was about Raytracing, it didn't take much time for me to get fully engrossed in it. I mean, a [complete ray tracer](https://gist.github.com/kid0m4n/6708750), succint enough to fit at the back of the business card, measuring up to a grand total of 1337 bytes... what's not to like?

Like any programmer worth his salt, I immediately decided to port this brilliant piece of art to my favorite programming language, Go. Did I hear you ask "Why Go?"

* Go is poised to be fast, system level programming language
* Performance is one of the key factors
* Scaling to multiple cores is supposed to be a breeze
* Just more opportunity to write more Go code (I am learning the language after all)

So a couple of hours later, I got the [first version](https://github.com/kid0m4n/gorays/blob/0e2c2c467221d6e5ee27fcf95f8a9412c6a8b21d/main.go) up and running. I modified both the C++ and the Go versions slightly to generate the same [exact image](http://i.imgur.com/yFicPrE.png) (512 x 512) so that it was as close a comparision as possible (no disrespect to prodigal aek.)

This is how the numbers stacked up:

* C++ version: 11.803 s
* Go version: 28.883 s

Not too shabby for a few hours of coding, but obviously I was not going to let it rest there. I decided to seek help from the community.

Note: All initial testing was done on a Late-2011 MacBook Pro 15 with a Intel i7 2675QM processor, equipped with 16GB of RAM, running Mac OS X 10.9

Links
---

* Golang-nuts discussion thread: [link](https://groups.google.com/forum/#!topic/golang-nuts/mxYzHQSV3rw)
* gorays Github repo: [link](https://github.com/kid0m4n/gorays)
* C++ version gist: [link](https://gist.github.com/kid0m4n/6680629)
* Various parallel options tried: [link](https://github.com/kid0m4n/gorays/commits/parallel)

Aside
---

There seems to be a misconception (by and large) that there is a lack of community support around Go. But it could not be far from the truth. 2013 has been the best year for Go as far as I can tell. The [first "big" conference](http://www.gophercon.com/) around Go has been announced + the energy around Go in the [community](https://groups.google.com/forum/#!forum/golang-nuts) is at a new high. The amount of code being written in Go is also on a rise. Just have a look at the [language stats page](https://github.com/trending?l=go) in Github to get a brief overview.

My first step was to collate all the information I had about my problem statement into a post at [golang-nuts](https://groups.google.com/forum/#!forum/golang-nuts): [the post](https://groups.google.com/d/msg/golang-nuts/mxYzHQSV3rw/dOA78aeVLgEJ)

The amount of constructive advice I got in the first couple of hours was amazing. Let me summarize some of the biggest performance leaps for you:

{% img /images/go-improvements.png "Graph of Go performance improvements" Go Go Go %}<br />
(raw data available [here](/images/go-raw-data.png))

Move to go1.2rc1
---

Change# 1<br/>
Before: 28.883 s<br/>
After: 25.644 s<br/>
Change: **11.2 %**<br/>

The initial benchmark run was on go1.1.2. But it didn't make sense to continue benchmarking on this old/stable release as the **1.2** release is just around the corner and **1.2rc1** is already available; with performance improvements in tow.

This is one awesome property of Go. Every new release, we are magically given a new leash on life in form of additional performance _without having to make a single line of change_. And since we are guaranteed that a well formed Go1 code will continue to work and compile with all Go1.x releases, this is "sone pe suhaga" (for my English speaking brethren: icing on the cake.) For example, the Go1.1 release saw as much as a 30% boost in performance for a lot of Go programs over Go1.0.

In this particular case, it was a 11.2 % win. With zero changes to code. Hurray!

Global to Local Rand
---

Change# 2 ([commit](https://github.com/kid0m4n/gorays/commit/5f16e4131faf9f712e716b04038523bd57bbca9b))<br/>
Before: 25.644 s<br/>
After: 23.816 s<br/>
Change: **7.13 %**<br/>

This one was a doozy. I will mark this up to my inexperience with Go in general and lack of sleep in particular. The convenience of the in built [math/rand](http://golang.org/pkg/math/rand/) package's rand.Float64() caught me of guard. What [Sebastien Binet](https://groups.google.com/d/msg/golang-nuts/mxYzHQSV3rw/lRxaH8z2IfoJ) rightly pointed out, and what is amply clear from the documentation, is that rand.Float64() is thread safe and is overkill in single threaded/goroutined/gophered scenario. It uses locks internally to ensure that multiple goroutines (parallel to threads in other dimensions) can access it without mucking up (in general.)

Needless to say, using a local rand (i.e. calling rand.New(...) and using the returned instance everywhere) netted me a cool 7.13 % boost.

Buffer to Win
---

Change# 3 & 4 ([commit 1](https://github.com/kid0m4n/gorays/commit/e9c418ec3a77d014ced05bcbd52f38aa3ef7c2af) and [commit 2](https://github.com/kid0m4n/gorays/commit/1d09eac86697d7f50cdf5866fd9a6988f4cf6e84))<br/>
Before: 23.816 s<br/>
After: 22.818 s<br/>
Change: **4.23 %**<br/>

This was a two parter (as are all good movies.) The first part involved not writing to [os.Stdout](http://golang.org/pkg/os/#pkg-variables) inside the inner loop of the ray tracer. I guess writing to os.Stdout is not bad in general, but placing it inside a super tight inner loop (which essentially iterates through every pixel of the rendered image) is a big __NO-NO__! Getting rid of that was easy. Just use a bytes.Buffer (hat tip to [Robert Melton](https://groups.google.com/d/msg/golang-nuts/mxYzHQSV3rw/kJkpTdN7uP0J).)

The real win was avoiding the [allocation of a byte slice](https://github.com/kid0m4n/gorays/blob/e9c418ec3a77d014ced05bcbd52f38aa3ef7c2af/main.go#L73) inside the inner loop. Its common knowledge in the Go world: lesser the garbage you create, the more performant your application becomes. By avoiding the allocation of the byte slice per pixel (thanks [Nigel Tao](https://groups.google.com/d/msg/golang-nuts/mxYzHQSV3rw/3Z0vi5pilF8J)), we optimized things further and brough the overall execution time down to 22.818 s (2x compared to the C++ version.) Not too shabby.

Engage Warp Engines
---

Change# 5 ([commit](https://github.com/kid0m4n/gorays/commit/249f229ba8c769c38d7dc018acfdf29cc86d6e43))<br/>
Before: 22.818 s<br/>
After: 12.747 s<br/>
Change: **44.14 %**<br/>

I did myself a solid by looking closely at the [tracer()](https://github.com/kid0m4n/gorays/blob/249f229ba8c769c38d7dc018acfdf29cc86d6e43/main.go#L149) function. It was being called a gazillion times and required some much needed love. Instead of looping through all possible "potential" spheres every time **tracer** was called, why not precompute the various possible sphere locations (information which is readily available) and avoid the [double loop](https://github.com/kid0m4n/gorays/blob/1d09eac86697d7f50cdf5866fd9a6988f4cf6e84/main.go#L142) in the first place?

This minor optimization saw the most massive jump thus far. A 44.14 % boost. And now, we were just 0.944 s shy of the C++ program's raw speed. Was this a apples-to-apples comparison anymore? Well no, but it was never meant to be. Attempt was to use the best of what **Go** has to offer to extract maximum performance. And we were barrelling down that road.

Scotty, is that all you got?
---

Change# 6 ([commit](https://github.com/kid0m4n/gorays/commit/9066519c24a092b7f672b71327f5c825f84a77a4))<br/>
Before: 12.747 s<br/>
After: 12.644 s<br/>
Change: **0.81 %**<br/>

This is a significant change (thanks to the suggestion from [kortschak](https://groups.google.com/d/msg/golang-nuts/mxYzHQSV3rw/zMvk18jvbyYJ)) but probably subdued as it came late in the game. Yep, thats right, its the damn rand function again. Apparently, it's okay to __NOT__ use a random generator when generating a raytraced image. The already well oiled engine quickened a little further to 12.644 s (an improvement of 0.81 %)

Warp Speed Ahead
---

Change# 7 & 8 ([commit1](https://github.com/kid0m4n/gorays/commit/7420ef3f94be2dd0d1887d98cdbec67a14a07f9f) and [commit2](https://github.com/kid0m4n/gorays/commit/ddfe825f0902877c02467a4f65f46c4044bc7939))<br/>
Before: 12.644 s<br/>
After: 2.947 s<br/>
Change: **76.69 %**<br/>

Concurrency ([not parallelism](http://blog.golang.org/concurrency-is-not-parallelism)) is the forte of Go. It makes it easy to think in concurrent terms. In fact, you can end up coding a [replicated search client](http://talks.golang.org/2012/concurrency.slide#50) which reduces tail latency without having to use a lock, conditional variable or callback.

However, ray tracing (at least in its currently coded form) is inherently a parallel problem. So how did Go fair? Very well, thanks for asking.

I tried a [couple](https://github.com/kid0m4n/gorays/commit/8df629c60998400c0bdfdb549552005eff36816c) [of](https://github.com/kid0m4n/gorays/commit/ab0dd18274694e68aa9205fd1a1855230749d725) [approaches](https://github.com/kid0m4n/gorays/commit/45567013de4c58f484d72d40179a179c578268ba) and this one worked the best. One reason why I could try all these approaches so quickly is because of how easy it was to express my intent in Go. And that is an understatement.

Final approach used: Fire up N* goroutines and have them on standby; with each one capable of rendering a full row of the image. Then queue up all the rows that need rendering in a channel which feeds into all the available goroutines. There is no starvation as each goroutine has work available the moment it gets done with its current row. Awesome right? No locks, conditional variables or callbacks!

*N = runtime.NumCPU()

Smart Pow
---

Change# 9, 10 & 11 ([commit1](https://github.com/kid0m4n/gorays/commit/527e08317c9307316e2a7a8e9379cf40778eeaa1), [commit2](https://github.com/kid0m4n/gorays/commit/6bc7a2c4c635c269d364fddd68fa999877a0c98c) and [commit3](https://github.com/kid0m4n/gorays/commit/86329c598d79e3d366ce83bba3fc80a6e8a69edd))<br/>
Before: 2.947 s<br/>
After: 2.593 s<br/>
Change: **12.01 %**<br/>

Michael Jones rightly [pointed](https://groups.google.com/d/msg/golang-nuts/mxYzHQSV3rw/sGxylrYx638J) ([twice](https://groups.google.com/d/msg/golang-nuts/mxYzHQSV3rw/blcuZtZSiAEJ)) that multiplications are way better that [math.Pow](http://golang.org/pkg/math/#Pow) when the exponents are known and optimization friendly (like 4 and 99.) Having exponentiation in the language itself would definitely help as the compiler would be able to do a good job of optimizing the multiply chains.

Commit 11 also optimized things further by skipping computing __r__ if not required (because of a potential early return.)

Base Test: Revisited
---

Remember this:

* C++ version: 11.803 s
* Go version: 28.883 s (single core)

Now that we have done ALL these optimizations, how do these numbers change:

* C++ version: 11.803 s
* Go version: 10.349 s (single core)
* Go version: 2.593 s (multi core)

We started the journey being 144.7 % slower than an equivalent C++ version to being 12.32 % faster, in single threaded mode. With multiple cores enabled, we ended up being **78.03 %** faster than a functionally equivalent C++ version.

I also did some additional testing on a dedicated machine powered by Ubuntu 13.04 (kernel 3.8.0.26) i7 2600 with 16 GB RAM. The results were very heartwarming to say the least:

{% img /images/go-vs-cpp-after-optimizations.png Go vs C++ after optimizations %}

* 1C = 1 Core
* 8C = 8 Core

Conclusion
---

Without further adieu:

* This much improvement would not have been possible without support from the Go community. You guys rock!
* Asking a genuine constructive question on golang-nuts has always gotten people a +ve response. Even if it is a "Sorry, it would be a difficult to do this in Go right now"
* Go is maturing quickly. Every new version brings in new backward compatible performance; just recompile your code and voila!
* It was a breeze to do the various optimizations suggested by the community, and it is very possible that some of these optimizations would not be required in the near future (like say with a language build in exponentiation)
* The succinctness and simplicity of the resultant Go code is a big BIG win!
* We are comparing C++ to a language which not only has garbage collection built into the statically compiled binary, it also has the ability to handle multiple cores / synchrony baked right into the language
* I am looking forward to redoing these tests with an optimized C++ version (single-core, multi-core) if someone is willing to contribute those changes

Links:

* Golang-nuts discussion thread: [link](https://groups.google.com/forum/#!topic/golang-nuts/mxYzHQSV3rw)
* gorays Github repo: [link](https://github.com/kid0m4n/gorays)
* C++ version gist: [link](https://gist.github.com/kid0m4n/6680629)
* Various parallel options tried: [link](https://github.com/kid0m4n/gorays/commits/parallel)

That's all folks. Thanks for reading. If you have any comments, feel free to leave them here on the blog or you can always email me at karanm@thoughtworks.com / kidoman@gmail.com. Ciao!

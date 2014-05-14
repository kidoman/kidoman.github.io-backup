---
layout: post
title: "Async IO - Part 1"
date: 2014-05-13 16:24
comments: true
category: programming
tags:
  - golang
  - concurrency
  - io
  - node.js
---

I was recently reading a [series](http://venkateshcm.com/2014/04/Reactor-Pattern-Part-4-Write-Sequential-Non-Blocking-IO-Code-With-Fibers-In-NodeJS/) on "Write Sequential Non-Blocking IO Code With Fibers in NodeJS" by [Venkatesh](http://venkateshcm.com/).

Venki was essentially trying to emphasize that writing non-blocking code in NodeJS (either via callbacks, or using promises) can get hairy really fast. For example, this code demonstrates that aptly:

```javascript Callback driven NodeJS
var express = require('express');
var app = express();

app.get('/users/:fbId', function(req, res) {
  var id = req.params.id;
  var key = 'user:' + id;
  client.get(key, function(err, reply) {
    if (err !== null) {
      res.send(500);
      return;
    }

    if (reply === null) {
      res.send(404);
      return;
    }

    res.send(200, {id: id, name: reply});
  });
});

```

The exact code is available on [GitHub](https://github.com/kidoman/fibrous/blob/master/nodejs/callback.js#L59-L72) (so is the [promises driven version](https://github.com/kidoman/fibrous/blob/master/nodejs/promise.js#L55-L65), but I won't bother inlining it.)

What we actually wanted to write (if it were possible, was):

```javascript Not plain old JavaScript
var express = require('express');
var app = express();

app.get('/users/:fbId', function(req, res) {
  var id = req.params.id;
  var key = 'user:' + id;

  try {
    var reply = client.get(key);
    if (reply === null) {
      res.send(404);
      return;
    }

    res.send(200, {id: id, name: reply});
  }
  catch(err) {
      res.send(500);
  }
});
```

The magic would happen in line number 9 (above.) Instead of having to provide a cascade of callbacks (what if we wanted to do another lookup after we got the value back from the first), we could just write them serially, one after the other.

Well. Apparently we can!

## Fibers

> A fiber is a particularly lightweight thread of execution. Like threads, fibers share address space. However, fibers use co-operative multitasking while threads use pre-emptive multitasking. Threads often depend on the kernel's thread scheduler to preempt a busy thread and resume another thread; fibers yield themselves to run another fiber while executing.

Fibers allow exactly this kind of black magic in NodeJS. It is still callbacks internally, but we are exposed to none of it in our application code. Sure you will end up writing a bunch of wrappers (or have some tool generate them for us), but we would have the sweet sweet pleasure of writing async IO code without having to jump through all the hoops. This is how the wrapper code for redis client looks like:

```javascript
var Fiber = require('fibers');
var client = require('./redis-client');

exports.get = function(key) {
  var err, reply;
  var fiber = Fiber.current;
  client.get(key, function(_err, _reply) {
    err = _err;
    reply = _reply;
    fiber.run();
  });
  Fiber.yield();
  if (err != null) {
    throw err;
  }
  return reply;
};
```

(the [real code](https://github.com/kidoman/fibrous/blob/master/nodejs/fiber.js#L52-L60) is here in case you are curious)

I liked how the code looked. Having survided a 'promising' node.js project, I was definitely curious about this new style. Maybe this can be the saving grace (before generators and **yield** take over the JS world) for real world server side JavaScript.

## Fibers you say

But the code (and the underlying technique which makes it tick) sounded very familiar, and reminded me of a similar technique which is used in Go to allow writing beautiful async IO code. For example, the same function from above in Go:

```go
m.Get("/users/:id", func(db *DB, params martini.Params) (int, []byte) {
  str := params["id"]
  id, err := strconv.Atoi(str)
  if err != nil {
    return http.StatusBadRequest, []byte{}
  }

  u, err := db.LoadUser(id)
  if err != nil {
    return http.StatusNotFound, []byte{}
  }
  return http.StatusOK, encoder.Must(enc.Encode(u))
})
```

Sure, there is a little more happening in here (Go is statically typed), buts its the exact same thing as the fibers example, without all the manual wrapping. Any call which does IO (like line 8) blocks the currently executing goroutine (just like a fiber, a lightweight thread.) The natural question to ask is, if the goroutine gets blocked, how do other requests get processed? Its quite simple actually. The Go runtime automatically schedules any other goroutine which is ready to run (their IO call is done) on the thread on which the current goroutine was running.

Since goroutines are light weight (stack size is just 4 KB in Go 1.3beta1 compared to the much larger ~2 MB thread stacks), it is not unusual to have hundreds of thousands of goroutines actively running in a single process, all humming along together. The best part, since the threads have to do less context switching (the same physical thread can continue running on the processor core, just the instruction pointer keeps changing as the goroutines shuffle in and out, just as in method calls), we are able to extract a lot more efficiency from the same unit of hardware than otherwise. Otherwise IO calls, which would otherwise cause the thread to block and wait, could cripple the system and bring it down to its knees. Read [this](http://venkateshcm.com/2014/05/How-To-Determine-Web-Applications-Thread-Poll-Size/) article for more context on this.

## Performance

A fellow ThoughtWorker asked me, "Does performance matter when choosing a framework?"

I know where he was coming from, and how we shouldn't make decisions purely based on performance (we would all be doing assembly if that was the case.) While it is true that as a startup (or even in the case of a well established player), building the MVP and getting it to the users is paramount, you really dont want to face the situation where you suddenly have a huge influx of users (say it goes viral) and you are caught between a ROCK (scale horizontally by throwing compute units at the problem) and a HARD PLACE (have to rewrite the solution in a technology more amenable to scaling.) Both of these options are expensive, and can potentially be a deal breaker.

Therefore, provided everything else is more or less equal, choosing the more performant one is never a bad thing.

With this context, I decided to compare the two solutions for their performance, given that they more or less looked the same. I decided to allow the system under test to use as many cores as they wanted, and then hit them with 100 concurrent users, each of which is going full tilk for around 20 seconds (used the awesome [wrk](https://github.com/wg/wrk) tool for benchmarking.)

The results:

Golang  | &nbsp;
--------|---------
Stdlib  | [134566](https://github.com/kidoman/fibrous/blob/master/go/stdlib.go) (3.81ms)
Martini | [51330](https://github.com/kidoman/fibrous/blob/master/go/martini.go) (9.51ms)

node.js   | &nbsp;
----------|------------------------------------------------------------------------------------
Stdlib    |[54510](https://github.com/kidoman/fibrous/blob/master/nodejs/stdlib.js) (7.78ms)
Callbacks*|[36107](https://github.com/kidoman/fibrous/blob/master/nodejs/callback.js) (10.84ms)
Fibers*   |[27372](https://github.com/kidoman/fibrous/blob/master/nodejs/fiber.js) (18.76ms)
Promises* |[22665](https://github.com/kidoman/fibrous/blob/master/nodejs/promise.js) (17.15ms)

\* The Callbacks, Fibers and Promises versions are created using Express. The Stdlib versions use the **http** support in the corresponding standard libraries.

All the numbers are in **req/s** as given by wrk (higher is better.) The latency details are in brackets (lower is better.) Clicking the numbers will take you to the corresponding code in the [GitHub repo](https://github.com/kidoman/fibrous) (the [README](https://github.com/kidoman/fibrous/blob/master/README.md) has the detailed numbers.)

The tests were done on an updated Ubuntu 14.04 box with a Intel i7 4770 processor, 16 GB of RAM and a SSD.

As you can see, the **fibers** method of doing async IO in **node.js** comes with a perceivable loss in throughput compared to the pure **callbacks** based approach, but looks relatively better than the **promises** version for this micro-benchmark.

At the same time, the default way of doing IO in Golang does very well for itself. More than **134,000 req/s** with a **3.81 ms** 99th percentile latency. All this without having to go through crazy callbacks/promises hoops. How cool is that?

## How the tests were run?

### Software versions

* Go 1.3beta1
* node.js 0.10.28
* wrk 3.1.0

### Command used to run

A more detailed description is available in the [README](https://github.com/kidoman/fibrous) but I will explain a simple version here:

* Start the program (by say running ./start_martini.sh)
* Run the benchmark (by running ./bench.sh)
* Record the result
* Rince and repeat 3 times and take the best run

### Notes

* All cores on the Intel i7 4770 were set to the performance governor
* Redis was not tweaked
* ulimit was not raised

## Summary

This is part 1 in a multipart series looking at how async IO (and programming in general) is done in various languages/platforms. We will be going indepth into one language/platform with the every new article in the series. Future parts will look at Scala, Clojure, Java, C#, Python and Ruby based frameworks and try and present a holistic view of the async world.

But one thing is very clear, async IO is here to stay. Not embrassing it would be foolhardy given the need to stay lean. Hope these articles help you understand gravity of the decision.

While some might argue that what we did in Golang was not really async, as the call was blocking in nature. But the net result achieved, and the reason why Go is still able to provide an awesome throughput despite blocking IO calls, is because the Go runtime essentially does the heavy lifting for you. When one goroutine is busy waiting for the results of a IO call to come back, other goroutines can take their place and not waste CPU cycles. The fact that this mechanism allows us to get away with fewer threads that would be required otherwise, is the icing on top.

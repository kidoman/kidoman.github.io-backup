---
layout: post
title: "EMBD: Behind the Scenes"
subtitle: "The backstory of the <strong>EMBD</strong> framework"
date: 2014-05-06 06:55
comments: true
category: life
tags:
  - golang
  - embedded
  - hardware
  - raspberry-pi
  - bbb
  - hal
---

For the impatient, TheBot was an experiment to kickstart a hardware engineering culture at **ThoughtWorks**. We choose the Raspberry Pi (RPi) as the prototyping platform and Golang as the language in which to create the firmware. We felt the need for a solid hardware abstraction layer (HAL) which would allow us to not only target the RPi, but soon expand to other platforms as well. We also wanted to make it dead easy to talk to a variety of sensors. Since there were no such existing frameworks for [Golang](http://golang.org/), we ended up writing our own, and we called it [EMBD](https://embd.kidoman.io/) ([Github](https://github.com/kidoman/embd)).

> **EMBD** enables you to get started on your project quickly by providing build in support for the various [platforms](https://github.com/kidoman/embd#platforms-supported), [protocols](https://github.com/kidoman/embd#protocols-supported), [sensors](https://github.com/kidoman/embd#sensors-supported), [controllers](https://github.com/kidoman/embd#controllers). It also allows your prototyping code to survive through to production because of the built in hardware abstraction layer and Golang's [versatility](https://github.com/kidoman/embd/wiki/Why-Go). Thus significantly shortening the **time to market**.

## A little history

A 20 year old company, **ThoughtWorks** has been primarily into software. We have made our mark in the ability to create high quality custom enterprise grade software over the years. Data analytics is a key focus areas, but there are others with deeper pockets already investing. Mobile development is also a conquered field. So when our chairman, [Roy](http://en.wikipedia.org/wiki/Neville_Roy_Singham), asked ThoughtWorkers to preempt the next big thing, we naturally started looking at the Internet of Things (**IoT**) as one of the avenues for innovation. Approaching it from a purely hardware perspective would have meant a lot of playing catch up, as we would need to seed a lot of talent in spaces before we could catch up:

* material science
* industrial design
* manufacturing, etc.

We wanted to get started quickly, leveraging on abilities we have honed over the last two decades. And one of the low hanging fruits to explore was the software side of internet connected devices, firmware for the rovers and quadcopters, the user experiences of these [new contraptions](https://nest.com/thermostat/life-with-nest-thermostat/).

## Enter, TheBot

We deliberately choose to build something simple - to push the odds in our favor. However, the simplicity proved to be a boon as it allowed us a lot of scope for innovation in the software side of things. We also wanted to take this opportunity to study. To glean experiences from. From many angles, this is just scratching the surface of what is to come, but you have got to start somewhere. Think of it as bulb # 1.

{% youtube iMXjkZ4B3EM %}

(We first showcased this to public on Jan 10, 2014 and dedicated the effort to Aaron, a good soul and a ThoughtWorker, who passed away on Jan 11, 2013.)

{% img center /images/thebot-small.jpg TheBot %}

It was a lot of work. A lot of things you took for granted suddenly needed to be taken care of in code and in hardware. The fact that the code now had to actually "run" on the hardware brought in additional challenges as well. Concurrency is the name of the game. Most of the events which you needed to handle and react to (ex: a obstacle suddenly apprearing infront of the car, while the car is turning on its own) would come concurrently, in no particular guarenteed order. The firmware had to be both efficient and easy to reason about.

## Golang to the rescue

> Created by a team at Google in 2007, Golang aimed at making software development pleasurable again. Software that built quickly, ran well on multi-core hardware in networked environments.

(see the official [FAQs](http://golang.org/doc/faq#What_is_the_purpose_of_the_project) for a more complete picture)

Golang has excellent support for concurrency in the core language. The RaspberryPi is single threaded and we needed the car to handle multiple real world interactions at the same time. Using threads would have forced us to use mutexes, etc, for synchronization. The ‘goroutines+channels’ architecture in Golang helped us focus on the “actual” interactions. (Goroutines are light weight threads which are executed via the Go runtime on real threads via a M:N mapping. Channels are a typed mechanism for passing messages between goroutines). The resulting code is much easier to read, reason with and understand.

> “Simply running the binary was always enough. This helped tremendously in shortening our development/build/deploy cycles and made the process even more gratifying.”

Golang is a statically typed, garbage collected and compiled programming language. However, in use, it feels like a FAST (slightly) verbose scripting language which has support for systems programming and duck typing. Since the cross compiled binary was entirely self contained, no runtime was needed. Simply running the binary was enough, which helped tremendously in shortening our development, build and deploy cycles and made the process even more gratifying.

(a recent article by [SpaceMonkey](https://www.spacemonkey.com/blog/posts/go-space-monkey) details their story of switching from Python to Golang for the firmward of their embedded storage device. Its a must read to get a better idea of what Golang has to offer in this space.)

## Summary

These and various other reasons led us to create [EMBD](https://embd.kidoman.io/) and release it to the world.

**PS:** Earlier, this article was part of the [Introducing EMBD](/framework/embd.html) piece. However, based on feedback, I decided to split them so that they the original article focused solely on the new framework and did not pull focus to other tertiary things.

## Links

Homepage: https://embd.kidoman.io/<br/>
Github: https://github.com/kidoman/embd

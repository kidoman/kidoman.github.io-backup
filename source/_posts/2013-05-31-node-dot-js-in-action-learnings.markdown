---
layout: post
title: "node.js in action: Learnings"
date: 2013-05-31 01:58
comments: true
category: engineering
tags: node.js talk
---

{% img /images/node.js.png nodejs %}

I recently gave a talk on node.js at work... the talk was surprisingly well attended. It was scheduled to start at 1 PM. At around 1:02 PM, I was alone in the room with not another soul in sight. The room filled to the brim in the next 10 minutes. Last count = 33

Link to the talk
================

[click](http://goo.gl/Pcz2wQ)

The Talk
========

The theme of the talk was to get people excited about the node.js work we were doing for a large enterprise client. Almost 9 months into development, we had figured out a tonne of patterns and idioms and had actually started becoming productive with it. Talking about the hurdles faced during the non avoidable learning curve seemed like a sensible thing to do.

Tech Stack
----------

Although I cannot reveal any actual URLs atm, the stack of the application we are developing is as follows:

* node.js (Platform)
* Express (Web Server)
* Sequelize (ORM)
* MySQL (Database)
* Q (Promises)
* Mocha/Sinon/Chai (and their -as-promised bretheren, for TDD)
* Grunt (Command runner)
* Coffee-Resque (For background jobs)
* Socket.io (For realtime tracking of connected clients)
* Redis (As a datastore for our generated socket.io handshake tokens)
* connect-assetmanager (For managing our assets; __update__ we have now replaced this with our own hand rolled solution)

Promises to Keep: Q
-------------------

I think it is essential for any new node.js project to base itself on a solid promises library. As indicated above, the library of choice for us was Q. I would go out on a limb and say this - "Avoiding callback hell is probably the least interesting feature of Q."

Q allows us to beautifully structure our code without excessive "pyramiding." You still end up with a few nested promises from time to time, but that is also avoidable with judicious usage of Q.spread.

Testing
-------

Equally important is the need to get the TDD/BDD pattern flowing right from get go. Mocha/Sinon/Chai allow BDD in JavaScript to look almost as elegant as RSpec (which I consider to be the holy grail of developer friendly BDD.) Being smart and using the -as-promised utility node modules will also save you a lot of grief and restore a sense of sanity to the test cases (you can then essentially start returning promises of future asserts from your test cases, instead of having to call a __done()__ at the end to signal the end.)

Scaling and Deployment
----------------------

Our dreams of serving a million requests from a single node.js process were quickly shattered when we discovered a pegged 100% CPU (on a single core.) Besides trying to fix the main issue (which turned out to be the MySQL driver being used by sequelize) we also spent some time getting the [unicornification](http://unicorn.bogomips.org/) of node.js right. We ended up with a master-slave arrangement which allowed us to scale our application to N-1 cores (where N is the total number of cores available in the machine.) Common sense dictated the __-1__ part (to leave a core aside for the kernel and various sub systems.)

Figuring our deployments was also fun. Although there was some talk of building and deploying a .rpm (our target Linux distribution was CentOS 6.4), the general lack of time lead us to adapt a git based deployment mechanism. Any SHA is a good candidate for deployment to any of our environments. We obviously ended up tagging the good ones.

Conclusion
----------

We really went out on a limb here by using node.js for the project when we did not actually have much in house expertise on the same. However, the effort has redeemed itself many fold. A lot of people have successfully managed to __get__ "concurrent programming" as a result. Coming out from the comfort zone of RoR, .NET and Java also possibly has changed the course of their lives for ever! :)

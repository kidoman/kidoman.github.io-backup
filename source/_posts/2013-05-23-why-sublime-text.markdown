---
layout: post
title: "Why Sublime Text?"
date: 2013-05-23 02:17
comments: true
category: programming
tags: chef emacs sublime text textmate vim
---

{% img center /images/sublime.png %}

I wanted to pen down the results of my 5 month long "select the best text editor available" thought experiment. So here we go...

tl;dr
-----

Although **vim** gets the best text editor award, I choose to continue using Sublime Text **3** for all my development needs. Read on to find out why...

The Experiment
--------------

This journey started with me being infatuated with the [RailsCasts](http://railscasts.com/about) theme which Ryan Bates made famous via his [podcasts](http://railscasts.com/). Coming from a data warehousing / .NET background, this felt surreal.

As a budding Rubyist, I fell in love with [TextMate](http://macromates.com/). It was fast and elegant. The fact that the grubby little fingers of my .NET-abled friends could not get at it made it more wonderful. It provided just enough control... the balance produced elegant harmony.

So naturally, when I had to code Ruby for a Windows infrastructure automation project (based on [Chef](http://www.opscode.com/chef/)) we were doing for our client, I pounced at Sublime Text. It brought in the elegance of TextMate and acted as a necessary survival tool in a Microsoft dev environment. The awesome snippet system eliminated any need I **possibly** could have felt for an IDE (RubyMine, I am looking at you.) And need I mention the beautiful Cmd + P fuzzy file lookup implementation (Ctrl + P for the devs who call Seattle their home.)

Its now been a few months since I have had to boot up Windows for *work*. Now that I had used Sublime Text for a good few months, I wanted to evaluate the other options available with Mac OS X and adopt the _ultimate_ nirvana...

A quick research showed me the following available tunnels:

* vim (Inspired from [Gary Bernhardt](http://blog.extracheese.org/), of [DAS](https://www.destroyallsoftware.com/) fame)
* emacs (Inspired from [Jim Weirich](http://onestepback.org/), of Rake fame)
* MacVim (Inspired from the ever loveable [tenderlove](http://tenderlovemaking.com/), a Ruby and Rails core committer)
* Sublime Text (Based on my own experience)
* TextMate 1 / 2 (Inspired from watching Ryan Bates work his magic in the screen casts)

I am not going to elaborate too much about these choices but just provide an abstract about some of them to help you find the light.

(Mac)vim & emacs
----------------

vim felt like **God's Own Editor**. Seeing Gary fly through the code was a revelation. In his own words (paraphrasing), "I don't want to show you my key strokes (in the recorded podcasts, contrary to how Ryan does it in his) because they will fly by so fast that you won't have time to see them."

This reveals a lot about the core vim. A fully done up vim instance (perhaps decorated with [Janus](https://github.com/carlhuda/janus)) is so "busy", that there is hardly any time for you to be motionless. You are doing something all the time... As you learn more of the legendary editor, you manage short cuts and macros which would make Chuck Norris turn in his bed. Hell, after a while, you ever start using the wonderful vim script [Fugitive](https://github.com/tpope/vim-fugitive) to make tender love to Git! All from within your text editor... what could be better than that?

The same can also be said for emacs too (some people even use emacs as their shell / OS replacement!)

One man's meat is another man's poison
--------------------------------------

And I am not saying this in the passing. After using vim/MacVim for a good 3+ months (I had picked up decent speed with the editor and figured out a bunch of essential plugins which helped me almost avoid having to step out to [iTerm](http://www.iterm2.com/)) I realized that it had not made me any faster at programming. Sure, I could "edit" a document at the speed of light, [motion](https://github.com/Lokaltog/vim-easymotion) around effortlessly... but when it came to solving a real hard business problem, I found myself retracting my hands from the keyboard... a **setTimeout(.., 0)** of sorts!

Although a lot of people claim that they can multitask super efficiently, scientific studies have proven that it is human to have your work deteriorate when multi-tasking. It was hard to organize my thoughts with the constant rattle of keyboard which vim encourages.

For me, the realisation dawned one lazy afternoon when trying to trace down a memory leak problem as I witnessed myself involuntarily quit vim and open up the same project directory in Sublime Text.

**The think time that was afforded to my brain when using a less efficient editor was missing when using a ninja text editor like vim. vim kept the brain so busy because of its efficiency and throughput (keys pressed per second) that the left lobe hardly got any time to go [tickless](http://lwn.net/Articles/549580/) and actually focus on the problem at hand.**

The same happened when I had to use the mouse to shift through files as well... the time slices were larger because of the easy nature of navigation in a simple text editor which hardly took any thinking. So the brain could subconsciously continue working on the problem at hand instead of trying to figure out the best possible way to do the required action in vim.

Summary
-------

Sublime Text / TextMate are hardly the best editors out there. But, when it comes to actually letting you focus on the job at hand (remember, our forte is smart programming, not smart editing of source files); they **SHINE**.

This is why I shifted back to Sublime Text (now in its **3rd** incarnation) after my 5 month **thought** experiment and I couldn't be happier.

Give it a honest shot (just like you had to for your full featured IDE or vim/emacs) and you will understand how short and rewarding the learning curve is. The sweet sensation you will feel at the tip of your tongue after your first multi line edit is totally worth it! Trust me.

**PS**: For folks stuck in Java / .NET, I feel for you!

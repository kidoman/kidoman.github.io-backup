---
layout: post
title: "God save the JS"
date: 2014-05-10 03:15:15 +0530
comments: true
category: programming
tags: javascript, golang
---

Golang has has this feature right from the start. Very innocuously named ```gofmt``` this tool (distributed as part of the Go compiler toolchain) ensures that all Go code have a common look and feel to it. It does that by enforcing a few things:

* Ensure that the imports are all sorted alphabetically
* Removes all unneeded semicolon from the code
* Aligns ```const```, ```var``` and ```struct``` constructs so that the variable names all line up nicely

Since ```gofmt``` is integrated into most text editors used to work on Golang, using the tool becomes ubiquitous. Essentially, it can take Go code which looks like this:

```go
package main

import "github.com/go-martini/martini"

import "log"

func main() {
  m := martini.Classic()
  log.Print("Starting...");
    m.Run()
}
```
and make it this:

```go
package main

import (
    "log"

    "github.com/go-martini/martini"
)

func main() {
    m := martini.Classic()
    log.Print("Starting...")
    m.Run()
}
```

Unless otherwise instructed, it also does all indentation using real tabs. Before you turn away in disgust, let me tell you this. Its actually quite amazing how TABS, when done right, can actually be a benefit. Since all Go code gets run through ```gofmt``` anyways, all Go code end up using real TABs. And that allows for people to have their own indentation widths without effecting the actual sourde code. Want more spacing, sure go ahead and ask your favorite editor to represent the TAB as 8 spaces, DONE! Coming from Ruby land, 2 spaces for a TAB it is then. Might sound too good to be true, but it just works.

## Enter JavaScript

This Github repo showed up in HackerNews front page today.

https://github.com/rdio/jsfmt

Looks like an attempt to bring the same ```gofmt``` magic over to the JavaScript land. And I am excited about the sanity that will ensue if this becomes popular among JavaScript programmers. One true/universal way for all JavaScript code formatting. Having suffered ```IntelliJ```'s shoddy JavaScript default formatting enough, this feels like the light at the end of a tunnel.

So ```go spreadTheWord()```

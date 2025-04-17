---
layout: post
title:  "Uni Manifesto"
date:   2025-04-17 12:00:00 +0000
categories: programming
---

# Uni Manifesto

Literate programming is programming in a format where comments are not just minor additions to the code, but the code forms one half of a complete, human-readable document alongside full natural language explanatory prose.

Interactive programming is programming where you interact with a running system rather than the traditional "edit-compile-debug" cycle.

They have become entwined in the most popular modern form of both: "programming notebooks", most prominently in the Jupyter Notebook format.

## Problems With Literate Programming

* Literate source is not the program source, adding an extra "build step" between literate source and code source. This adds extra friction with tools etc.
* Alternatively, markdown-in-code-comments is horrible to write (commenting every line) and read the source directly.

## Problems With Interactive Programming

* Badly behaved code, for example code that goes into an infinite loop, usually causes the interactive environment to slow to a crawl or lockup completely. If you're lucky you can manually interrupt the running code via ^c or similar.
* Code that produces voluminous output usually causes the interactive environment to slow to a crawl or lockup completely.

These are not theoretical problems, I have run into them hard when trying to use Clerk or Jupyter Notebooks to solve Advent of Code problems. AoC solutions involve data sizes that are pushing you to write reasonably performant code, and if you put a foot wrong you can end up running for a very long time or producing a million lines of output.

## Prior Art

"Clerk" programming notebooks are close to what I want. They exhibit some of the "bad experience with badly behaved code" problems I'm describing. Also not a fan of some aspects of Clojure and the general overheads it introduces.

In general, notebook/literate/interactive environments that are written in the target language experience these problems, because they can't control their own execution robustly enough.

## Solution

Build my own Clerk-style notebook programming environment. Design it to function well with badly behaved code. Design the language so that the literate part and code parts intermix with minimal friction.

## Plot twist

This is that environment. I can run some code:

```
(2 :+ 3)
```
```
5
```


In the source, this is just the expression `(2 :+ 3)` on a newline, with no additional ceremony needed. In the compiled output, the code is parsed and executed. The code itself is converted to a markdown ` ``` ` code block and pasted into that part of the output, and the result is inserted afterwards in another code block.

## The Language

Lisp syntax, with class-based object oriented semantics. The lisp syntax is mainly there to make it quick and easy to get off the ground with minimal work on parsing. Instead of the classic lisp `(function arg0 arg1 ...)` style, it's `(receiver :method arg0 arg1 ...)` to call a method, as the basic unit of evaluation. `:method` is a keyword naming a method, and can be an expression that evaluates to a keyword instead of a literal keyword.

So in `(2 :+ 3)`, we are calling the `:+` method on the object `2`, a number object constructed via literal, and supplying the argument `3`. This is evaluated as you'd expect. So even though this is lisp we get infix operators (albeit still with no precedence, must be fully bracketed).

```
(2 :+ (6 :* 7))
```
```
44
```


This is implemented as a simplistic tree-walking interpreter for now, although it's designed to handle errors cleanly and not take too long to error and die if the code is looping forever (or just being slow). Implemented in C++ for speed and potential future construction of platforms that can "do stuff" outside of just the markdown output (fantasy console is my biggest plan).

## The Tooling

Even though this is not strictly interactive programming, we simulate it by instantly reevaluating and reloading the output in the browser on save of the source. This is done via a server written in Go which watches the source, calls the compiler when it changes, hosts the output html on a local http server, and hosts a websocket endpoint which is used to tell the page to reload itself when the source is rebuilt.

This results in a pretty snappy experience. Pressing Ctrl+S to save, the page appears to reload immediately with the new source. Remains to be seen how this will hold up to bigger documents and more complex/longer running code, but it's a good baseline.

The only reason the server is written in Go and calls out to the compiler as an external program is that I had about 95% of the Go code I needed packed into a similar tool I'd been experimenting with previously. Ideally I'd convert it to C++ also and run the compiler in-process. That's a future project, as the server does have some non-trivial moving parts (http+websocket server, filesystem watcher).

## Future Improvements

Well first I need to implement the rest of the language to a reasonable level of completeness.

I am envisioning this as somewhat of a playground to experiment with language design, so I may make the language part swappable to try out different things.

Rewrite the server in C++ and properly integrate the compiler.

Rewrite the compiler to evaluate the code with a better strategy, probably compile to bytecode with some basic optimisations and then run that.

Build a fantasy console "shell" which can embed in the html output, so you can write a game as a literate "notebook" and play the game on the resulting notebook page. Ambitious but exciting.

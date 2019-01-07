---
layout: post
title:  "Adventures in Programming Language Design"
date:   2019-01-07 08:00:00 +0000
categories: languages
---

Recently I've been venturing into the world compilers and programming language design. I thought I'd write a bit about my adventures for anyone interested.

The Before Times
---

Before the current bout of learning, I knew relatively little about the subject. I had read the classic [Structure and Interpretation of Computer Programs (SICP)](https://mitpress.mit.edu/sites/default/files/sicp/index.html), which has the chapter on a "meta-circular evaluator" - that is a Lisp/Scheme interpreter written in Lisp/Scheme. I had also written some very small and simple (and slow) Lisp and Forth interpreters as an experiment. That's about it though. Parsing "real" syntax, more complex than Lisp s-expressions or Forth stream-of-words, seemed like a big hurdle.

The Book
---

What kickstarted me is the book ["Crafting Interpreters" by Bob Nystrom](http://www.craftinginterpreters.com/). Available for free online, I got into reading it. Unlike some books on the subject I had seen before, it takes a practical approach using modern languages (Java and C). It's quite readable and I found it easy to understand. Also notable is the approach of providing all the code for working interpreters, there's no "exercise for the reader" (although the book does have exercises to test understanding or implement extensions, which is nice).

Initially I followed the code fairly closely, but translated into Rust. I think translating the code like this is quite a good way to really learn the material, where with simple reading I might not internalise it properly. I titled this initial version "Notlox" after the book's Lox language.

Going Off Book
---

Almost immediately however, I was straying from what the book set out. 

It was clear to me how to modify the code to work for a different language, so I was quick in coming up with my own design. Thinking about a scripting language for embedding in Rust projects, I designed it to resemble Rust code in syntax, but with semantics more like Lua. The semantics were fairly similar to the book's Lox language, with some tweaks, but the syntax was quite different. I had some adventures in figuring out some of the finer details of how Rust's parser works and porting this over.

I also ended up modifying the interpreter architecture. I used the parse-to-an-AST approach from Part 1 of the book, as this seemed like a much nicer and easier to extend parser. However, I then output bytecode for a VM as in Part 2 of the book, for speed. It wasn't too hard to work out how to produce bytecode from the AST representation.

As a result, both the language and code are now pretty much unrecognisable from the book's Lox (although some traces are still present in the code). After a lot of agonising, I renamed this version "Nail".

Trial by Christmas
---

Coincidentally, I happened to start this project in November. Every year in December is [Advent of Code](https://adventofcode.com), which I enjoy a lot. This sparked a great idea: get my compiler up to scratch and do Advent of Code in a language I wrote myself.

Well, I managed it. I put in some hacks, notably around for loops which are hardcoded to work with ranges, arrays, and maps only. There are also a few missing features, like garbage collection (currently you just produce garbage and run out of memory if you produce too much). Despite these limitations I have been able to solve most of the early Advent of Code problems without great drama (although I did have to quickly hack in a few extra stdlib functions and operators I had missed). Really the writing in the language feels a lot like writing Lua or Go, which is intended. (I did slip back into mostly writing in Rust for the later/harder AoC problems).

My solutions are available on [Github](https://github.com/m-r-hunt/aoc2018). This is the most substantial body of Nail example code that exists at the moment.

Wrapping Up
---

I'm shelving Nail for now. It's been a very satisfying and educational project, but I think I want to move on to other things. Nail definitely isn't finished, it still lacks garbage collection and a few features. I might come back to it after some time to reflect and work on other things.

A project I might try with Nail is to write a simple game engine in Rust and integrate Nail as the scripting language. Something like a simple 2D vector/polygon drawing engine, with some keyboard interation, with Nail defining the game logic. I may also modify Nail to put in some specialised primitives, like 2D coordinates and polygons, with proper support so they resemble the other builtin Nail types.

At some point, I'd like to try some more language design experiments. Writing a frontend for LLVM and using it to JIT or AOT compile a language seems like a doable project, which should allow really good performance "for free" from LLVM's optimisation. Of course there are other issues to tackle like static typing, and I'm sure I'll run into more problems I haven't thought of.

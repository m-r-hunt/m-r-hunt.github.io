---
layout: post
title:  "GBA Development Log 2026"
date:   2026-01-01 10:00:00 +0000
categories: gamedev
---

See [New Year's Resolutions 2026](https://mechtoast.com/blog/life/new-years-resolutions-2026/) for a bit of context.

## Preamble (Before 2026)

* Set up development environment with [meson-gba](https://github.com/LunarLambda/meson-gba). Reluctantly using MSYS2 since all GBA stuff seems to assume MSYS2 on Windows by default.
* Got hello world with [tonc](https://gbadev.net/tonc/) set up. Planning to use tonc as my main dependency since it seems to be the most widely used and solid option.
* Started work on converting the hello world into a "Snake" demo, using 1px snake in the bitmap display mode which the hello world uses.
* Had to refamiliarise myself with GBA memory space layout, IWRAM and EWRAM since I want snake to be able to cover the whole display and I need EWRAM space to do that.

## 1/1/26

* Got this post setup to log for the year.
* Fix dumb bug in code to wrap the screen.
* Slowed down the snake some more and added a simple speed control
* Respawn apple after eating with a very simple fixed offset movement
* Stop you doubling directly back on yourself

## Design Notes Interlude

Anyone playing along may have noticed that the project doesn't seem to have a real design for what we're making so far, only the snake demo (which is very much not the real game).

I have been thinking about it and I have a slightly more concrete idea to make a tower defence game. Reasons why this is a good idea:

* I like them and have been thinking about them recently
* There aren't really any TD games on the GBA (because the genre hadn't got mainstream popular enough to penetrate through at that point)
* I think it's technically doable but will raise interesting technical challenges, especially to scale up the gameplay

## 2/1/26

* Check for collisions with self to lose
* Add a (simple for now) death state/animation

## 3/1/26

* Split out to start working on a tile graphics version of Snake
* Spent a long time trying to compile and get an image converter to work. I am not a fan of how this image converter is built/structured and even after compiling it was annoying. Only afterwards did I find the names of some other tools in the tonc tutorial which I will try out to see if they suit me better.
* Anyway I did eventually get a basic snake sprite tiled/converted and display it on screen in display mode 0:

![Snek on screen]({{site.url}}/assets/gba2026/snek1.png)

## Current TODOS/Possible tasks

* Improve my dev workflow to have a one click build + run the game
* Figure out why sometimes the game doesn't seem to rebuild properly unless I delete the file first. Is it getting cached somewhere by mgba? (Might be due to rebuilding while mgba is still open?)
* Better apple movement - PRNG or some kind of system with 2 tables of offsets that move out of sync
* Better death with random dripping
* Get a better image converter (or even write my own???)
* Make graphical snake work

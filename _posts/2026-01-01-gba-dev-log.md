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

## Current TODOS/Possible tasks

* Improve my dev workflow to have a one click build + run the game
* Figure out why sometimes the game doesn't seem to rebuild properly unless I delete the file first. Is it getting cached somewhere by mgba? (Might be due to rebuilding while mgba is still open?)
* Stop you doubling directly back on yourself
* Death when you hit yourself
* Better apple movement - PRNG or some kind of system with 2 tables of offsets that move out of sync

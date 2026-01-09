---
layout: post
title: "GBA Development Log 2026"
date: 2026-01-01 10:00:00 +0000
categories: gamedev
---

See [New Year's Resolutions 2026](https://mechtoast.com/blog/life/new-years-resolutions-2026/) for a bit of context.

## Preamble (Before 2026)

- Set up development environment with [meson-gba](https://github.com/LunarLambda/meson-gba). Reluctantly using MSYS2 since all GBA stuff seems to assume MSYS2 on Windows by default.
- Got hello world with [tonc](https://gbadev.net/tonc/) set up. Planning to use tonc as my main dependency since it seems to be the most widely used and solid option.
- Started work on converting the hello world into a "Snake" demo, using 1px snake in the bitmap display mode which the hello world uses.
- Had to refamiliarise myself with GBA memory space layout, IWRAM and EWRAM since I want snake to be able to cover the whole display and I need EWRAM space to do that.

## 1/1/26

- Got this post setup to log for the year.
- Fix dumb bug in code to wrap the screen.
- Slowed down the snake some more and added a simple speed control
- Respawn apple after eating with a very simple fixed offset movement
- Stop you doubling directly back on yourself

## Design Notes Interlude

Anyone playing along may have noticed that the project doesn't seem to have a real design for what we're making so far, only the snake demo (which is very much not the real game).

I have been thinking about it and I have a slightly more concrete idea to make a tower defence game. Reasons why this is a good idea:

- I like them and have been thinking about them recently
- There aren't really any TD games on the GBA (because the genre hadn't got mainstream popular enough to penetrate through at that point)
- I think it's technically doable but will raise interesting technical challenges, especially to scale up the gameplay

## 2/1/26

- Check for collisions with self to lose
- Add a (simple for now) death state/animation

## 3/1/26

- Split out to start working on a tile graphics version of Snake
- Spent a long time trying to compile and get an image converter to work. I am not a fan of how this image converter is built/structured and even after compiling it was annoying. Only afterwards did I find the names of some other tools in the tonc tutorial which I will try out to see if they suit me better.
- Anyway I did eventually get a basic snake sprite tiled/converted and display it on screen in display mode 0:

![Snek on screen]({{site.url}}/assets/gba2026/snek1.png)

## 4/1/26

- Getting the same snake gameplay working in tile mode. Little extension to what was there before, but mostly the same logic. We can't do a full screen-filling snake in this mode because there aren't enough sprites (128 vs 600 needed to fill the screen). The solution for that would be to use a tile background layer for the snake since we're drawing him grid aligned anyway. This version is also way more playable already.

![Snek sprite playable]({{site.url}}/assets/gba2026/snek_tile1.gif)

Still needs some work around rotating/flipping the sprites correctly to make him look correct when not travelling down +x direction.

## 5/1/26

- Tidied up some code warnings that have been popping up for a while
- Switch over to use the s32/u32 style notation already used by tonc for sized integers. Try to use it consistently in my code (and ditch default ints)
- Set up a simple script to build and run the game

## 6/1/26

Not much time today (may be true on Tuesdays in general)

- Reading about how tile backgrounds work
- Got a new graphics converter - grit. Seems to be widely used and was easy to compile from source. Need to try it properly but seems promising.

## 7/1/26

- Figured out grit, added to repo
- Switched to using grit to convert the test snake image and 4bpp format. May need a teeny bit of scripting to mangle the output from grit into the right form since the C array output doesn't compile out of the box. Still like it more than the previous converter I tried though.
- Thinking about palettes and bit depth. I think I will use 4bpp/16 colours per sprite in general as it is a nice limited number. Too many colours in pixel art is scary. I can probably come up with a consistent set of palettes (up to 16x16 colour palettes which feels like a lot), load them at startup and just use throughout. Same applies to background tiles.

## 8/1/26

- Got correct orientation of snake pieces to make it look a lot nicer. Haven't implemented corner pieces which would be kinda fiddly, this is enough for this demo for now.

## 9/1/26

- Set up a basic background with background tiles. This is straightforward to do with the graphics tech we've already learned for sprites, just have to set up the right control registers and write a little bit of data for the tilemap into VRAM.

![Snek with background]({{site.url}}/assets/gba2026/snek_tile2.gif)

## Current TODOS/Possible tasks

- Figure out why sometimes the game doesn't seem to rebuild properly unless I delete the file first. Is it getting cached somewhere by mgba? (Might be due to rebuilding while mgba is still open?)
- Better apple movement - PRNG or some kind of system with 2 tables of offsets that move out of sync
- Better death with random dripping
- Graphical snake death state

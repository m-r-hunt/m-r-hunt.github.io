---
layout: post
title:  "The Current State of My Life as a Hobbyist Game Developer"
date:   2020-03-23 12:00:00 +0000
categories: gamedev
---

I'm writing this blog post largely to get my brain in order and decide what gamedev projects I'm going to work on in the near future.

## This Year

Around this time last year was when I really began to get serious about my spare time gamedev projects. I've been trying to get into spare time gamedev for a while but always bounced off, largely due to bad "production"/"project management": the usual big dreams about epic game projects that I worked on for around a week and then never touched again.

I finally figured out how to set reasonable deadlines and scopes and started to actually produce finished games and get them out there for people to play. Getting games out the door and playable is great, even if they are (not yet) the masterpieces I want them to be.

Initially I was working using Rust and a couple of different libraries (Specs, Quicksilver and ggez). Eventually I switched over to using a proper engine, Godot, and have seen massive benefits in productivity. There's also been some bits of Pico-8 mixed in because I still love making little Pico-8 games.

I also got into doing a lot more game jams. For the past few years I have been doing 7 Day Roguelike (7DRL) but since december I've been participating in 8 Bits to Infinity jams. The nice community and guaranteed feedback on stream makes these very useful. During 2020 I've been hitting a target of one game jam a month which seems to be a nice cadence.

Also, since around the middle of last year I've been making a coherent attempt to improve my art skills by doing daily pixel art practice, chronicled on Futureland. I do feel I've made a lot of progress here and can generally produce art that looks like what it's supposed to depict and functions well, although I have no delusions that I'm a real artist. I also started doing a daily gamedev journal on Futureland to try and get a bit more consistent there too.

Finally, with some friends who are also interested in gamedev I've started to attend a regular (every few months) LAN party in which participants are encouraged to bring a game they made for everyone to play. I've enjoyed this a lot and it provides a nice longer term deadline to work to for some of my projects.

## Projects
A more or less complete list of game projects I've worked on over the last year-and-a-bit and actually released in some form:

* [Super Cake Cutter](https://mechtoast.com/Pico-8-Carts/cakecut.html) - Unusual Pico-8 puzzle game about dividing cakes equally. This is probably still one of my most polished games with a nice original core mechanic (although I'm not sure it has the legs to be much bigger than this P8 game).
* [7DRL 2019 - Beneath the Sands](https://mrhthepie.itch.io/beneath-the-sands) - Roguelite shooter vaguely in the vein of Enter the Gungeon. Pretty unpolished.
* Minigolf Adventure (Available on request) - Minigolf game with some adventure elements. Didn't turn out how I originally envisaged it but did get two "releases" which worked with two full 18-hole courses. This is where I learned that level design is hard.
* [Flappy Butt](http://mechtoast.com/flappy_butt/) - My first Godot game, a Flappy Bird clone made in a day. Kind of convinced me switching to Godot was a good idea.
* [Overgrown](https://mrhthepie.itch.io/overgrown) - Local multiplayer competetive farming simuator made to be played at the above mentioned LAN parties. Version 2 is imminently going to be released for the second LAN party. We had some fun playing this there.
* [Exit, A Dragon](https://mrhthepie.itch.io/exit-a-dragon) - Twine (choose your own adventure) made for 8 Bits Text Only Jam. Fairly happy with this although it was more creative writing than gamedev.
* [Planet Grid](https://mechtoast.com/Pico-8-Carts/planet_grid.html) - Small Pico 8 puzzler inspired (heavily) by the board game Barenpark.
* [Power Grid](https://mrhthepie.itch.io/power-grid) - Arcade puzzle(?) game thing I made for 8 Bits Shape Jam Part 1. Has only placeholder graphics and sound as per the rules of the jam. Sadly no one picked this to reskin so it doesn't have a "full version".
* [Mod Con (reskin)](https://mrhthepie.itch.io/mod-con) - Reskin of Mod Con By Joe Strout I did for 8 Bits Shape Jam Part 2. This was mostly an exercise in art and sound, which I think came out fairly well.
* [Elliptical Bob and Conical Chel](https://mrhthepie.itch.io/elliptical-bob-and-conical-chel) - Puzzle platformer based around swapping dimensions with two differently-controlled characters, made for 8 Bits Asymmetrical Jam. Quite happy with this one, and it won the jam too!
* [7DRL 2020 - Hostile Skies](https://mrhthepie.itch.io/hostile-skies) - Roguelite game where you fly an airship around and defend it with cannons. Somewhat inspired by old "defense" style flash games. Bit off a bit too much with all the systems in this and it ended being buggy and badly balanced at the deadline. Still, the world map came out quite well I think.

Here's a list of half finished projects that I've not managed to release/complete:
* TGBGM - Tetris clone for the original Gameboy (written in Assmbler and in the style of "The Grandmaster" Tetris games rather than Gameboy Tetris). Fun experiment in writing assembler, ended up pretty functional but not really complete/released.
* Zeldish - Zelda clone I made while still really figuring out Godot. Has one dungeon mostly complete although lacks a full game structure around that. Ended up getting shelved while I started work on Overgrown. Might come back to the general idea (straight Zelda clone) although I'd probably start from scratch on the code.
* Wizard School - Godot demo for an idea I started and didn't get very far with. Was thinking to big too early and ended up bombing out.
* GDP - Pico 8 Metroidvania with grappling hook platforming
* FFR - Classic style JRPG in Godot that I started fiddling with

## Where To Next

As mentioned above I'm about to release version 2 of Overgrown, which has been my big ongoing project for a while. I'm think that this will be the end of development on it for now. Here's the list of major projects I might do next:

* Exapnd Elliptical bob and Conical Chel (my most recent jam game)
* Reskin/expand Power Grid (since no one picked it to)
* Finish GDP
* Go back to Zeldish and make a full Zelda clone (but probably not using the original Zeldish bits)
* Start some other relatively big game project

Tech wise, I'm considering moving away from Godot. It's been a great tool for me and I've produced quite a few games with it now. However, it's maybe a bit too editor focused, whereas I'm a programmer at heart. GDScript is quite nice and I've been quite productive with it but it has some issues as a language (and C# support is still unfinished and I'm not sure I want to use C#). I might try using Love2D and building a bit of an "engine" layer on top of it - using some ideas that I like from Godot like nodes and scene instances. I might also try out Defold for a game jam although I have some misgivings about using a proprietary engine.

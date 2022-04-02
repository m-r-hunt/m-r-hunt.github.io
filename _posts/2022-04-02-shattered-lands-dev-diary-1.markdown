---
layout: post
title:  "Shattered Lands - Dev Diary #1"
date:   2022-04-02 12:00:00 +0000
categories: gamedev
---

Starting up a new project.

## The Plan

### Lessons Learned from 7DRL

This year's 7DRL felt like it went really well for me. I want to try and take some of the things I did well from it and see if I can use that to be succesful in a larger non-jam game project.

#### Working and Motivation

I did a lot of work during the week of 7DRL. Obviously working a full week of >full time hours is not something I can do very often, probably not more than once a year. What I did that really worked well was having dedicated work time and setting a goal (in terms of #hours each day).

In the past my game project work strategy has been either 1) just work whenever I have time and feel like it or 2) try and do a bit every day. Neither has lead to me being able to sustain a long term project.

So now I'm going to try and do something similar to 7DRL but outside of the intense game jam structure. Whenever I have significant free time (an evening or a weekend day), I'll designate that as game work time, and pick a work goal. Then work as I did during 7DRL, and finish up by writing up a blog post (like this one).

I'm not stopping myself for doing other odd bits of work whenever I have little bits of time, but I'm no longer relying on that as my main work effort. Hopefully this strategy will be sustainable over a longer term.

#### Using Existing Assets

In the past I've been a bit wary of using existing assets, particularly free ones, in my game projects. The reasoning is that these assets are sometimes not that great (at least free ones), multiple assets from different sources rarely hang together to create a coherent look, and your game usually ends up looking kind of generic. Well, during 7DRL I did crack and use assets from the internet, and with the work to find decent ones it went OK. These critisms are still justified, but it does open up a lot of space to work without needing to dedicate a lot of time just to creating assets.

So for my new project I'm planning to use mainly existing assets, at least for the beginnning of the project. I've found a good (paid) asset pack that should cover my needs for quite a while and let me get on with making the game.

### The New Project

For a while I've been wanting to build a game that feels more like a world the player inhabits and interacts with rather than "levels" to complete or similar. I did some of this in one of my unfinished prototypes and that aspect was a lot of fun (I don't think that game was going anywhere though).

So I'm going for a relatively ambitious RPG. The core gameplay will be a bit like a classic roguelike, with grid based tactical combat. However, rather than a randomly generated roguelike dungeon, it will be set in a predesigned open(ish) world for the player to explore.

My idea for the higher level game structure is to model it after Dark Souls, with resources replenished at checkpoints while enemies also respawn. The goal being to make progress delving into various dungeons without dying or running out of resources. I'm expanding the "estus flasks" model to include all the player's consumable items, so you can have e.g. throwable bombs which get replenished at checkpoints. By limiting the number of items you can take with you, interesting choices will hopefully emerge, while the fact that items always get replenished at checkpoints should encourage experimentation and discourage hoarding consumables. I'm still unsure how "levelling up" will work, both having no levels and only equipment affect your build, and having a permanent choice system have their appeals. All of this stuff is quite far off of course.

This project is pretty ambitious, but I have an idea of an initial scope which is very acheivable, comparable to a game jam project. I'm going to shoot for getting that playable and then pick bits to build out in greater depth.

## Today's Work

### Map Editor

I started off by building a map editor. Building the world is going to be a big chunk of work on this project. I've used generic map editors like Tiled in the past, but the problem with these is that they are too generic, and you have to manually impose whatever structure your game needs on the layers and tools available. Building the map editor "in-engine" is less work than you might think, since you get all of Godot's tools to play with (Godot's editor is built as a Godot project so a lot of useful stuff is available), and you get to share code between the map editor and the actual game. It's still a bit of work but I think it will pay off in even the medium term.

I've implemented the basic functionality of creating/saving/loading maps, placing individual tiles, and placing entities on a seperate layer. Each map is rendered out as a single image which will be drawn at runtime rather than a dynamic tilemap which will be good for performance. Ultimately the plan will be to have the "overworld" chunked into small bits which can be loaded/unloaded dynamically to allow the player to wander without load screens. For now, I'm just editing single maps to get the basic gameplay.

![Editor]({{site.url}}/assets/sl_map_editor_1.png)

There's a lot more which could be done on the editor, and I'll go back to it periodically as I need things.

### Programming [Yak Shaving](https://en.wiktionary.org/wiki/yak_shaving)

I decided to spend a bit of time implementing some systems on the code side, so I've got a solid foundation to work from. This is derived from my work on Knights in the Round. On that project, I used global/static events and data for communication between disparate parts of the code. (E.g. to share the value of the player's HP between the gameplay code and the UI). This worked well for decoupling these bits of code, but the use of globals is generally bad as most programmers will tell you and I was starting to bump up against some issues caused by that.

To counteract that I've implemented a simple Dependency Injection system. This means that the previously global events and state are now injected into classes that need them, keeping everything decoupled but avoiding actual global variables. There is still actually some used, in that the dependency implementations are stored globally, but the key is that this can be changed without needing to update every user of the system. This should make everything cleaner and allow easy testing if I need it.

I added some reflection magic to make all this easy to use and I think it will be very handy. It's mostly based on some existing code I had lying around so it didn't take too much effort to implement. There are some other code bits from my other Godot and C# projects I might need, but I can pull those in as I need them.

### Initial Gameplay Work

Finally I started to work on gameplay prototyping. I only got as far as loading a map created in the map editor for today, but it's a promising start.

![Gameplay]({{site.url}}/assets/sl_gameplay_1.png)

## Time Spent

I dedicated 4 hours today to working. In addition, the project has been brewing in my head and I've done some small bits over the past week or so.

## Next Steps

Keep working on gameplay prototype and implement basics of gameplay and combat.

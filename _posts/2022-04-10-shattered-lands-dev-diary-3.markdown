---
layout: post
title:  "Shattered Lands - Dev Diary #3"
date:   2022-04-18 12:00:00 +0000
categories: gamedev
---

Third work session with some bits of time over the long easter weekend.

## Gameplay Prototyping

First up I got basic combat working. I have a lot of ideas for where I eventually want to go with the combat system, trying to implement something that captures the feel/decision making in soulslike action games but in the top down tactical setting. However, for now I've just put in a simple move-hit system like that found in simple roguelikes. It's up and running on a basic level. I also put in a simple HP display which came along with some work to get UI and gameplay code to talk to each other cleanly.

Next I put in the beginnings of an item/inventory system with a simple "healing flask" - for now it's just one use to heal but once I put in bonfires/checkpoints they will refill. I've got the infrastructure in to support other refilling items eventually too. Haven't got any UI for displaying your available items yet, that needs doing still.

Then I added a staircase and the ability to change floors to a new map. This took a bit more thought as I wanted a solid system for handling a world made of many individual maps. I got this in, and now you can walk up the stairs to a seperate room. I ended up implementing a small hack where the stairs is an entity placed on top of the staircase background tile, which works for now.

![Current Gameplay]({{site.url}}/assets/sl_gameplay_2.gif)

Progress feels kind of slow because it feels like every time I come to add something new I need to put in a bunch of systems work to support it. I think that this is a hump I'll get over fairly soon, and that everything I'm doing will pay off even in the medium term. I'm approaching all the gameplay features I had down for my initial version and then I'll just have to build some content and finish up that first release.

## AI Work

I want to have more sophisticated AI in this game than I've put into other projects. I think this will add a lot to the combat experience. The canonical way to do this in game development is [behaviour trees](https://en.wikipedia.org/wiki/Behavior_tree_(artificial_intelligence,_robotics_and_control)). I've been doing [some research](https://ia804501.us.archive.org/29/items/pdfy-G4Wm9sq1LU298rm3/behavior-trees-ai.pdf) and thinking, and my current theory is that I will set up a behaviour tree system and use it to script all the enemies/NPCs. This is, of course, another big foundational system I need to get working before I can move on. Haven't started coding it yet but I think I'm just about ready.

## Time Spent

Spent 6 hours over the weekend (+ other odd bits). Was busy for some of it but I did want to get a little more in.

## Next Steps

Implement bonfires/checkpoints, add better UI (particularly for items/flasks), and work on AI behaviour trees.

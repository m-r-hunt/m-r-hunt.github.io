---
layout: post
title:  "Shattered Lands - Dev Diary #2"
date:   2022-04-10 12:00:00 +0000
categories: gamedev
---

Second day of work on this project.

## Gameplay Prototyping

Today I was mainly working on getting a prototype of the gameplay online. I started off by making the player character move around the screen, staying aligned to the tile grid. Then I sorted out a turn order/queue system, allowing me to have two players alternating (both controlled by the keyboard).

Next I added collision on the tilemap. This took a bit of work through the map editor pipeline. Each tile in the tileset can have collision flagged on or off.

![Collision Flags]({{site.url}}/assets/sl_editor_col.gif)

Then when you save out the map, collision for each tile is looked up and exported to go with it. Then in the actual game being played, collision is simply loaded directly and checked for each tile to prevent entities from walking over them. This goes along with my general design so far, where the map editor does any preprocessing so the actual runtime gameplay code is as simple as possible.

After that I added a basic enemy who wanders randomly so that I would have something to test combat with. Then I started implementing combat, which hasn't got very far yet.

![Current Gameplay]({{site.url}}/assets/sl_gameplay_1.gif)

## Time Spent

Spent 4 hours today + some bits over the last week or so since the last one of these.

## Next Steps

Implement basic combat and start working on more mechanics and UI for them.

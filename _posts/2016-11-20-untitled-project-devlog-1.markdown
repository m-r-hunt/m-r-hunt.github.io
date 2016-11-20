---
layout: post
title:  "Untitled Project Devlog #1"
date:   2016-11-20 16:40:00 +0000
categories: gamedev
---

As I mentioned at the end of my last post I have started a new project. I've now been working on it for most of a week. Here's a screenshot:

![Screenshot]({{site.url}}/assets/medamon-1.png)

The progress rate has been mixed this week, so it's not too advanced yet. You can walk around the map, with a decent walk cycle (mostly imitating the GBA Pokemon games - but not directly ripped). You always stay aligned with the tile grid but animate smoothly between positions. The camera scrolls following the player, which is trivially implemented using Gamemaker's "views". Finally you can interact with the sign which plays a line of dialog and fires a script. The art needs a lot of work, the tiles so far are recognisable (hopefully) but not very nice looking. There's a surprising amount of subtlety to making good pixel art tiles and mine need a lot of work.

The dialog-and-script interaction of the sign probably took the most work. I was original going to hardcode the dialog inside arrays inside the game. However I discovered that Gamemaker arrays are crap in various ways and don't have any kind of literal syntax. So instead I came up with a tiny dialog file format and wrote some code to interpret them. It can only do 2 things: play lines of dialog in sequence, and fire off Gamemaker scripts (by name). The ability to fire off scripts should allow enough extensibility to do anything I might need, but most interactions in the game will just be playing dialog lines. For example, most NPCs that are not part of the main story and signs around the world will just need to play dialog.

Drawing and creating art is hard and I need a lot more practice at it. I've been studying and trying to imitate the Pokemon art style _without_ actually copying it identically. This helps a lot as I've already learned a lot about walk cycles I didn't know before.

Priorities coming up:

* Build a little town with buildings and interiors (and add code to transition areas).
* Start work on the battle system.
* Create more tiles and improve the look of McMainCharacter.

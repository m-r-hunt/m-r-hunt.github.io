---
layout: post
title:  "Zeldish Postmortem"
date:   2019-11-30 12:00:00 +0000
categories: gamedev
---

Zeldish is the second game I made using the [Godot game engine](https://godotengine.org/). It's a dungeon crawling action game in the style of the 2D Zelda games (mainly the Gameboy Color entries).

![Zeldish Screenshot]({{site.url}}/assets/zeldish.png)

Not a whole ton to say about this one as it was a little bit of a filler project that I worked on before starting my next project, Overgrown, which I knew I was going to switch over to. Still, it was good to get some more experience with Godot. The main interesting things I did with Zeldish were learning about addons, and getting into some detail with scenes and loading in Godot.

Firstly, scenes. I structured the dungeon in Zeldish as individual room scenes. This allowed me to quickly make and test each room individually. Then in order to join them up into a larger dungeon, I had a script that handled loading the next room when the player walked into a doorway. It also animated the screen transition so the new room moved into view. This looked fairly nice I think. I ran into some issues as I was moving the room into the viewport rather than moving a camera around. This caused some issues with the physics engine. I'm getting better with dealing with the physics engine and getting it to do what I want over time. The main difficulty with this approach was making sure the doorways lined up between different rooms, as they were in different scenes. I'm still not sure what the best solution to that is. Putting all the rooms in one scene has its own problems (making sure that offscreen rooms are not doing anything, potential loading/memory problems if the world ever got really big). Maybe a plugin that checked and flagged up problems? Speaking of which:

I wrote a simple addon that allowed me to click a button in a menu in the editor and test a room in the dungeon. I did this because in order for the room transitions to work some extra state needs to be set up outside of just playing a single room scene. This worked pretty nicely. I also added a map generator, which walked through the room scenes and drew a map of the dungeon. This worked quite nicely too. The ease of extending the editor is a really nice feature of Godot. There's a little busy work in creating the addon structure but once you've done that it's quick and easy to add features to an addon. Also, you can always use "tool" scripts to extend the editor (in a more limited way) without writing a full addon, which also works nicely.

![Zeldish Screenshot]({{site.url}}/assets/zeldish_map.png)

Zeldish never got totally finished (even just the first test dungeon). But it was a good learning experience, and it fed nicely into my next project, Overgrown, which had a hard deadline so I had to get started on it. More on that next time.

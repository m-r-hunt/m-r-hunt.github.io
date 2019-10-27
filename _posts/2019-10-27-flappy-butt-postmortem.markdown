---
layout: post
title:  "Flappy Butt Postmortem"
date:   2019-10-27 15:00:00 +0000
categories: gamedev
---

As I alluded to in my [last postmortem](https://mechtoast.com/blog/gamedev/minigolf-adventure-postmortem/), I've stopped trying to write my games from a low level up, and instead moved over to using the [Godot game engine](https://godotengine.org/). [Flappy Butt](https://github.com/m-r-hunt/flappy_butt) is the first thing I made with Godot in order to try out and start becoming familiar with the engine. It's a Flappy Bird clone featuring a flying butt.

![Flappy Butt Screenshot]({{site.url}}/assets/flappy_butt.png)

Technical Stuff
---

I really like Godot, as an engine. 

In the past I've used Gamemaker Studio and Unity, and neither has ever really clicked with me. GMS feels restrictive  to me as a programmer. The GML scripting language is horrible for someone used to "real" programming languages. By contrast Unity is fine, but it's often slow and crashy, and just doesn't seem fun to use. It's also focused on 3D with the 2D features being an afterthought bolted onto the existing 3D engine which is never fun.

Godot does better on all these points. I've just been using the built in scripting language, GDScript, but it feels like a nicely designed modern language (with a distinct Python influence, which I'm quite happy about). You can also use C#, which is not for me in side projects, and write extensions in C++ ("GDNative") if you need to really speed something up. It has a real 2D mode in addition to 3D which is nice to use. And it just seems well designed and easy to use. The everything-is-a-node architecture (as opposed to something like Unity's game objects and components) and scene instancing is very nice, signals and yielding in scripts is really nice for scripting behaviour that takes place across multiple frames without resorting to state machines all the time. It's just really nice.

The main bump in the road of developing Flappy Butt was physics. It took me a while to understand how the physics engine and nodes work, and how to effectively use them. Use of a RigidBody2D as the player made writing some aspects of the physics easy, but I lost some control over the feel as a result.

Broadly speaking, moving from rolling my own engines in a low level language like Rust, even using "game libraries" like SDL or Quicksilver/GGEZ, to using a proper engine has been an absolutely massive boost to my productivity. Although I've still tinkered a bit with some of that stuff in the mean time, focusing on *actually making games and getting them out the door* is much easier using Godot. Maybe someday when my general game making skills have improved and if I hit some limits with big engines, I'll come back to making my own, but for now I'm just going to stick with Godot.

Project Management
---

I originally planned for this to be a short project I could execute quickly and complete to start learning Godot, thinking it might take a week or so. I ended up smashing that assumption and getting into a working state in a day. I could certainly have spent more time polishing, but I thought it was better to move on to a bigger project.

Art
---

(Hopefully) my pixel art continues to improve. I was fairly happy with the butt and the foreground pipes. I had a crack at a parallax scrolling background, which didn't turn out super nice looking but did function. Had some technical problems with the background not always rendering nicely which hopefully I've fixed for future games.

Sound
---

I made some quick bespoke sound effects using a [simple tool](http://www.drpetter.se/project_sfxr.html). This worked well enough given the quick nature of this project.

Next Project
---

After this I started working on a Zelda style dungeon crawling ARPG. (I waited a long time before writing this up so I've actually moved on from that too. More soon!)

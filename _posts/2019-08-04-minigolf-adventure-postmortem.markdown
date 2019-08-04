---
layout: post
title:  "Minigolf Adventure Postmortem"
date:   2019-08-04 20:00:00 +0000
categories: gamedev
---

I recently finished my most recent game project "Minigolf Adventure", and I thought I'd write up a few thoughts about what worked and what didn't.

![Minigolf Adventure Title Screen]({{site.url}}/assets/ma-title.png)

Summary
---

Minigolf Adventure is a minigolf game. Originally I had visions of some metroidvania and adventurey elements, but it ended up growing into a pretty straight minigolf game, albeit with the ability to wander freely around the courses. There are two 18 hole minigolf courses, one with a general grasslands theme and one with a beach theme.

Technical Stuff
---

MA is written entirely in Rust. I really like Rust and have started to use it as my go to language for side project stuff. However, while the core language is good, it's still a little lacking for gamedev. The main library I used, "ggez", got the job done but had some rough edges. This is the second game library I've tried out seriously and neither one has really hit the spot.

One thing that worked really nicely for me is Specs. Specs is a data-oriented Entity-Component-System library for Rust. I've gotten quite into data-oriented design, and Specs works really well for me. I built out some systems around Specs using code generation and macros, and ended up with quite a smooth development experience.

Ultimately though, I've come to the conclusion that I need to start using a real engine because I want to make games, rather than make my own game engine. For this reason, I'm moving to Godot (more on why Godot in another post possibly).

Project Management
---

My project management for this was mixed. On the one hand, I stuck with a project for a significant length of time and saw it through to a conclusion, which is almost unheard of for me outside of gamejams. On the other hand I lost focus in the second half of the project and ended up cutting a lot of work to get to a finished state so I could move on.

My main approach was to set myself one month deadlines, with a reasonably well defined objective at the end, always including "have a fully playable demo" as part of the objective. I did 3 of these month sprints with a month "lost" in the middle where I lost focus on the project. I think one month may be too long as at the beginning of the month the goal seems too far away and that was when I lost focus. I may also need to build in "holidays" between work periods to avoid getting burnt out. More experimentation is needed.

![Minigolf Adventure grassland scene]({{site.url}}/assets/ma-game1.png)

Game Design
---

I think I need to go into projects with a better idea of the design and scope before starting them. I had lofty goals for this game but ended up building something different than what I set out to. That said, I still think I ended up with a decent game.

My (underdeveloped) level design skills really got a work out on this one. 18 holes is a lot. It's possible I needed more mechanics to mix things up.

Art
---

I'm somewhat happy with how the art in this came out. I think I'm improving in this area. It had very bad "placeholderish" art for a long time, but near the end I really spruced things up. I've been more consistently practicing my pixel art skills and I think this is starting to make a difference (but I've got a long way to go).

![Minigolf Adventure beach scene]({{site.url}}/assets/ma-game2.png)

Sound
---

At one point I did make a start on some sound (I have some ambient wave sound effects sitting around), but ultimately I ended up cutting out all sound stuff in favour of getting the project finished. I think I need to put in some placeholder sounds a lot earlier in the project, so it's harder to forget until the end.

Conclusion
---

Minigolf Adventure has been one of my more succesful and well developed game projects, but I have a lot to improve in all areas. I'm looking forward to having a crack at my next project.

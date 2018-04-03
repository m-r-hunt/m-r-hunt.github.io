---
layout: post
title:  "Thoughts on Mega Man Boss Design"
date:   2018-04-03 19:49:00 +0000
categories: gamedev
---

I've been playing some Mega Man for [this challenge I came up with](player255.blogspot.co.uk), and I've got some thoughts on the boss design I've seen.

![Mega Man Zero 4 Title Screen]({{site.url}}/assets/mmz4-title.png)

Disclaimer: I'm not a Mega Man expert, I've only finished Mega Man Zero 4 and Mega Man & Bass. Some of this may not apply to other games in the series, I don't know.

Observation #1
---

Mega Man bosses don't telegraph their attacks well. Often not at all, they simply fire the attack and if you're in the wrong place (directly in front of them)you get hit. I think this is because they are built around ranged combat. If you're standing far away then the time it takes a projectile to threaten you is the trlegraphing for the attack. I've noticed this is most often true for movement where the boss will start moving with no windup. Often they will jump, requiring you to move to avoid a hit, as touching bosses in mega man is a hit.

Observation #2
---

Mega Man bosses often move around and stop in unpredictable positions. This can mean they stop and perform attacks that end up being impossible to effectively dodge, as you don't have enough space to maneuver. Instead you needed to avoid them stopping in that position, or proactively jump over them to effectively flip the positioning.

Observation #3
---

Boss arenas are usually very small. Typical is to just fit on a single screen. I find it's usually impossible to achieve a safe distance and observe the boss, as there simply is nowhere to go. A typical projectile, like Mega Man's arm cannon, takes somewhere around 0.75 seconds to cross the arena, so you're never going to have a long time to react to anything.

Observation #4
---

Attacks are often difficult to avoid *even if you know they are coming*. 

The quintessential example of this for me is an attack I've seen in a few forms: what I like to call the timing laser. In this attack the boss charges up a giant laser beam for several seconds, and then fires it. The beam instantly crosses the width of the screen and persists for a significant time, like a second. To avoid this you must either jump or slide a split second before the laser actually fires, so you remain over/under the beam for its entire duration.

![Me failing to jump over the beam]({{site.url}}/assets/mmab-timinglaser.gif)

 This is hard to execute even if you know whats happening, as you must precisely time your move or you'll end up moving either to early or to late and taking a hit. Although you can see the beam charging there's no indication of when the attack will actually fire, you have to know the duration from the start of the charging animation to the firing exactly. 

Conclusion
---

Mega man bosses are almost impossible to beat on the first try, or generally in a small number of tries. Instead they require a lengthy process of grinding. In this process you must first learn and understand all the bosses attacks and how to counter them, as well as when to attack and how. Then you have to practice executing this until you are be able to fight and react almost perfectly. In the initial robot master stages you can get away with less-than-perfect play, but in the final boss rush stage you'll probably need to be able to get through them almost unscathed, or bring a lot of lives.

That on its own, I wouldn't mind. What makes me frustrated, and makes me start to dislike the games, is this combined with the (anachronistic) lives system. If you lose all your lives, it's game over, which means that rather than respawning at the checkpoint before the boss you're forced to restart the stage. You only spawn with 2 lives, and they are not easy to come by. This leads to enforced grinding of the preceeding stages, which cranks up the frustration (at least for me). I got around this using save states. Whether this is "not playing the game as the developers intended", or "fixing a broken design", is a matter of opinion.

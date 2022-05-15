---
layout: post
title:  "Shattered Lands - Dev Diary #4"
date:   2022-05-15 12:00:00 +0000
categories: gamedev
---

It's been a long time since I did one of these because I've been busy. Keeping up work on the project though.

## Behaviour Trees

Most of what I've done since last time has been work on the AI Behaviour Tree implementation. I've got it into a good place now and I should be able to make good use of it going forward.

I've got a good implementation of Behaviour Trees which tick and produce actions for the entity they are controlling. I've got the code set up so that adding simple nodes, like conditions and actions, is just a single function to implement the behaviour with minimal boilerplate. More complex nodes that actually control flow in a non-trivial way are more involved to write, but these should be rare since I've got the basic primitives (sequence and selection) which cover most cases.

I've got a simple lisp-like S-expression format for actually writing/structuring the trees themselves, which is fairly nice to work with. Here's an example, where ~ is a sequence and ? is a selection:

```
(? (~ HealthLow 
      HasHealingItem 
      (Try TargetAdjacent EvadeTarget) 
      UseHealingItem)
   (~ TargetAdjacent MeleeAttack)
   (~ TargetNearby PathToTarget)
   Wander)
```

This script was fairly easy to get going, but already encodes some non-trivial behaviour (healing when low on health, moving towards and attacking a target). The pieces are modular and it's easy to imagine builing different behaviours from these and a few more building blocks.

Here's a quick demo of the enemy using this behaviour:

![Current Gameplay]({{site.url}}/assets/sl_gameplay_3.gif)

## Time Spent

Spent 5 hours today (+ other odd bits). Have been busy with other life stuff the past month or so, getting back into it now.

## Next Steps

Similar to last time, implement bonfires/checkpoints and add better UI (particularly for items/flasks).

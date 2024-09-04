---
layout: post
title:  "Elementio Dev Diary 1"
date:   2024-09-04 12:00:00 +0000
categories: gamedev
---

I've been working on a project recently and I have too many thoughts about it in my brain, so I need to exorcise them by writing them down into a blog post.
It's a bit of a poorly edited stream of consiousness, but I need to get it out and maybe it will be of some interest.

# Project Overview/Intro

I've been writing a factory builder game (i.e. a bit like Factorio) which is loosely based on real particle physics and chemistry, with a side-on perspective where gravity exists.
My current working title is Elementio.
Alex if you're reading this start working on a good name for it.

![Demo Video]({{site.url}}/assets/elementio_demo1.gif)

I've been using a really cool engine/framework called [Dragonruby GTK](https://dragonruby.org/toolkit/game).
As the name suggests it uses Ruby, which is a language I haven't really touched before but have learned for the purpose.
Both seem really cool.

My project is coming along ok.
I have a target set of features for a "1.0" demo version, which I'm getting fairly close to.
Only suffering a little bit from an ever-moving goalpost (and wasting time writing blog posts when I could be working on it...)

# Dragonruby Good

I won't bury the lede: so far my experiences with Dragonruby have been very good.
It seems like a well designed engine with a pragmatic focus on _actually making and shipping games_.
That might seem like an obvious thing for a game engine but from my experience you'd be surprised how many game things you can find online have very shiny demos but fall down when it comes to publishing a game for other people to play.

I would also like to shout out that the [Discord community](https://dragonruby.org/toolkit/game/chat) around the engine is very active and helpful.
Thanks to everyone who's answered my dumb questions in the chat and given me encouragement on progress updates.
On top of that, one of the Dragonruby developers, Amir Rajan, is very active on the Discord and has given me a lot of help personally, and in general seems very open to community feedback.
This is great, keep doing what you're doing guys.

One thing that's stood out to me about Dragonruby is how the API is designed in a way that seems unusual at first but is actually very powerful.
Dragonruby's API is kind of "functional" in the sense that each frame you get your inputs from the engine, and you have to produce outputs as data about what to render for that frame.
This stands in contrast to other frameworks where you would use a function call to draw, for example `love.graphics.draw(sprite, x, y)` in Love2D (which comes to mind as a somewhat-comparable framework to Dragonruby for me).

At first I thought this was kind splitting hairs, and there wasn't much different between calling a function and sticking some data in an array to be rendered later.
However, the more I've used it, the more it's become apparent that this data-driven "functional" style opens up a lot of very powerful idioms.
Because you still have that data about what to render, you can reinterpret it and use or transform it in different ways.

This also means that Dragonruby code becomes testable by default in way that conventional game code often isn't.
Game code that operates on a big ball of mutable state and directly calls API functions is a pain to test - you either have to carefully architect things contrary to the "normal" way of doing things, or put a lot of effort into techniques like mocking/stubs/etc.
By contrast, if you write Dragonruby code that takes some arguments and returns some data, it ends up trivial to test without undue effort.

The way my initial effort at code has ended up reminds me a lot of Clojure.
I like Clojure and it has definitely influenced my programming, but I've never really used in a big way because of the complex (and annoying) tooling and the fact it isn't well positioned for game programming which is most of what I do.
(Someone could write Dragonruby-for-clojure but they haven't).

The key idea from Clojure that I'm thinking of is that of using plain-old-data built out of arrays and maps, and writing simple functions that transform those data structures, rather than using classes or equivalent "heavier" constructs.
This is a lot of what I've ended up doing so far, and Ruby's language features and Hash/Array classes support it quite well.
This then plays into the Dragonruby API which accepts data in simple array or hash form to render, meaning you can plug bits together and transform data without getting bogged down in Object Oriented Design land (i.e. hell).

I think I want to lean into this more.
I've been thinking about whether it would be good idea to write a library in the style of `clojure.spec` for data validation to support this.
I envisage this as mainly a testing and debugging tool, and I think in Ruby it could work very nicely.

# No Refactoring

Thus far I've intentionally done almost no refactoing in the code.
I've split out functions to remove explicit duplication but have otherwise let it grow organically.
I've ended up with almost all the game logic in one big `tick` function, around 400 lines at time of writing.

Before any trained programmers in the audience throw up in disgust, you show know that this isn't as bad as it sounds.
The code isn't spaghetti, it's just unfactored.
In my opinion, it's quite easy to follow everything the game is doing by reading through the big tick function.

It does need to be refactored into something sensible, but refactoring before now would have been premature.
I've been learing Ruby-the-language, Dragonruby-the-engine, and the problem domain of my specific game style all at once.
Now that I've got a better handle on all of those, it's time to start cutting.

What I've learned so far has been a lot of what I mentioned above about the Clojure-style arrays-and-maps data being effective in Dragonruby.
I am going to split up the code into well seperated functions, but not make the data into classes and mix code and data in that way.
I will make some use of classes, but mainly as namespaces to organise related code and take advantage of the typing saved by `attr_gtk`, not to do "proper" Object Oriented stuff.
I'm also thinking about replacing Dragonruby's entity system with my own homegrown one, since it seems to be a less favoured part of the engine that I'm not really taking advantage of anyway.

# Hot Reloading Good

One of Dragonruby's big features is its hot reloading system.
Whenever you save Ruby code in your project, it's immediately reloaded in the running game during development.

![Hot Reloading Demo]({{site.url}}/assets/elementio_hot_reload_demo.gif)

In the past I've had pretty mixed experiences with hot reloading systems like this.
It is nice when it works, but I've always run into issues using it for real work.
You can end up building up state in a running instance that is not reflected in the saved code, meaning when you fully reload the system it no longer works.
Or alternatively you can have issues with stale references or state in running game meaning it doesn't work on a hot reload, forcing a full reload.

However, I haven't had these issues really at all using Dragonruby, the hot reloading has worked great and provided a very nice development experience.
I think this is in part due to the plain-old-data arrays-and-maps style I've been banging on about in this post, which reduces the chance of those kinds of state issues when reloading.
You could theoretically end up with state that goes away on a hard reload but it doesn't seem to have been a problem for me.
The idiom of using `state.whatever ||= initial value` is a surprisingly powerful idiom for initializing data in the hot reloading environment without adding a lot of overhead to the code.

My one caveat/gripe is that sprites aren't reloaded automatically.
That's easily fixed with an IMGUI window exposing the `GTK.reset_sprites` function on a button though.
Speaking of IMGUI...

# IMGUI

I love IMGUI APIs for debug user interface during development.
Generally not for user-facing "real" UI, but for quickly throwing together visualisation and controls for bits of the game while working on stuff.
`dear-imgui` is the classic in the field for C++ game development, but that's not in Dragonruby (or easy to integrate).
And as far as I can tell there's nothing like it in the Dragonruby ecosystem.

That lead me inevitably to starting to write my own IMGUI library for Dragonruby, tentatively titled Dr. Imgui/drimgui.
It's only in a larval stage, needs a lot of work to feel complete, but I've already got a fair bit of use out of it for developing my game.
For example, building a window that exposes Dragonruby's built in debug features in a convenient way.

![IMGUI Demo]({{site.url}}/assets/elementio_imgui_demo.gif)

I would like to get this polished up and released as open source, although it will be a while (gotta finish 1.0...)

# Issues with Dragonruby

Sadly it's not all roses, although there isn't much in this section.
Honestly mainly teething problems for me rather than big problems with the engine.

The docs are a bit lacking in parts.
Everything is in samples but often missing from the main API docs, and discoverability suffers as a result.
It took me a while to catch on to the fact that there was a lot missing from the API docs and the samples were the place to go to find it.
The samples often unclear on nuance that would (ideally) be in the docs.
But that said, having usage examples is extremely helpful for getting stuff done.

I had some issues with Cruby vs Mruby.
For the unfamiliar, Cruby refers to the mainstream implementation of Ruby.
Dragonruby doesn't use that, it uses Mruby, an eMbeddable implementation of Ruby.
This has some consequences for the end user.
There are differences between Cruby and Mruby, and most learning material for Ruby is targetting Cruby.
You also don't have access to the mainstream Ruby package ecosystem (gems), including even stuff that is considered the "Ruby Standard Library".
Alternatives are available, there is an Mruby package ecosystem, but it's all a bit of a trip hazard for a beginner.
Fine once you've got your head around it really.

Also, relevant to me and my interests, to get good pixel scaling you need a "pro" license for the "HD" features.
I want this and I'll have to pay a monthly fee for it, since I'm currently on the one-off purchase basic license.
Honestly it's hard to complain when I got that license for Dragonruby from one of those giant Itch.io bundles around the Pandemic and thus have been getting value for almost nothing...

# Path Ahead

- Finish v1.0! Gotta do it, get it out there.
- Do a decent job of refactoring as outlined above
- Upgrade to Pro for pixel goodness

That's it, bye.

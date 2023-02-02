---
layout: post
title:  "Figuring Out Web Tooling"
date:   2023-02-02 12:00:00 +0000
categories: programming
---

Last time, I got [Rust code compiling to Web Assembly and running in a browser with minimal tools.]({{site_url}}/blog/programming/rust-in-browser-without-bullshit/)

Recently I've been learning some more traditional web development techniques. Namely CSS and React via [some excellent courses](https://www.joshwcomeau.com/courses/) (with Javascript picked up along the way since I've had some exposure to it).

This means I needed to figure out the tools that I previously railed against in my last article. Some of my prejudices were correct I think. There's a culture in web programming of chucking in complex tools and dependencies which isn't overly helpful. Part of the problem is the more general problem in programming of documentation accidentally being unhelpful when you're completely new to the subject, don't know the buzzwords, etc.

However, the core pillars of the web ecosystem are actually well engineered and documented, and serve a useful purpose when doing Javascript development. Shocking I know. I eventually managed to clear the barrier for entry, but I thought it would be helpful (possibly to me in the future) to record the journey.

# Picking Major Dependencies

Before starting I needed to decide what the major dependencies I wanted to use were.

* What language? Plain Javascript or something that compiles down to it?
* What framework? As the joke goes, new one every week.
* Any other bits I know I'll need?

For language, I landed on [Typescript](https://www.typescriptlang.org). I prefer static types over dynamic, and Typescript seems to be well supported and popular. It's a thin layer over plain JS which I think is a good approach. Seems like everyone has Typescript bindings available for their libraries so it's easy to use with basically anything.

For framework, as I mentioned above I was learning [React](https://reactjs.org/) from a course I found online, and I'm not at the stage of wanting to try anything else. However, somewhat on a whim, I decided to try out [Preact](https://preactjs.com). This is a React alternative, that claims to be smaller and faster with the same feature set. It has a compatibility layer for full React compatibility so you can use the whole existing ecosystem (theoretically, haven't pushed it too hard yet).

Quick digression: do we even need a big framework? Can't we just use [Vanilla.js](http://vanilla-js.com/)? Obviously you can just write everything the old fashioned way. But frameworks like React simplify developing complex UI by automating the "mucking about with DOM elements" bit of web programming and just let you focus on writing the interesting behaviour of your app. I've been convinced that they are broadly speaking A Good Idea if your goal is to write complex interactive web pages. You sacrifice some performance over theoretical hyperoptimized pure JS, but if I wanted incredible bare metal performance I'd go away and write desktop apps in C.

Finally, I decided to use [HTM](https://github.com/developit/htm) over JSX. For anyone unfamiliar, the TLDR is that these are ways to write HTML-like syntax directly in Javascript which is a nice convenience. HTM is a newer and less common option but it has the advantage that it's implement as a plain JS library and doesn't require an extra build step, which is why I chose to use it.

# False Start with Preact CLI

My first approach was, as recommended by the [Preact docs](https://preactjs.com/guide/v10/getting-started/), to use the Preact CLI tool.

It started off smoothly enough, I installed and ran the tool to generate a typescript project from their template.

However, I quickly ran into a brick wall when trying to understand how everything worked. The Preact CLI bundles a bunch of tools and has a bunch of features that are obvious if you're an experienced JS dev, but very unobvious and not well documented for a noob like me.

The straw that broke the camel's back for me was that the template code was importing CSS from Javascipt like this

```
import style from './style.css';
```

and I couldn't find a straight answer as to how this worked or what tool was doing it. (Spoilers: this is a feature of Javascript bundlers [like Webpack](https://webpack.js.org/guides/asset-management/#loading-css), one of which is being used by the Preact CLI.)

# Starting from Scratch

So having become frustrated with the Preact CLI, I chucked it in the bin and went tried to start from nothing and build up to a full tooling setup.

## Typescript

First I got Typescript working. The Typescript compiler has [decent docs](https://www.typescriptlang.org/docs/handbook/intro.html) and it's trivial to use it as a standalone tool. Just install via NPM and run `tsc index.ts` and it will happily spit out `index.js` (or compiler errors if you have mistakes). You need to add more options or create a `tsconfig.json` file to make it really useful, but that wasn't too hard.

I wrote a quick `index.html` to include my `index.js` script and ran a python script I had handy to serve the current directory over HTTP.

Success! A basic webpage running some Typescript code.

## Dependencies

Well we need to be able to import some dependencies. Modules and imports is an area of Javascript that changed somewhat recently, where "somewhat recently" is 2015.

The traditional approach here is to use NPM to install dependencies and have some kind of build step that packages all the JS used in your website into one blob that gets sent to the browser (more on this later).

I still hadn't figured out how all that worked so I did the simplest thing I could think of. I grabbed a "compiled" copy of the libraries I wanted and vendored them manually into my project. I also had to grab the Typescript `.d.ts` definition files. After that though I was able to import like so

```
import { render } from "./vendor/preact/preact.js";
```

and have everything work. I wrote some test code and served it up with my python script and everything worked.

## Webpack

At this point my project was coming along but there were some major things that were not up to proper standards:

* Javascript being sent to the browser as a bunch of little files, bad performance
* Shonky vendored dependencies, painful to add more or update

Somewhere I picked up the information that a bundler would fix this issues and that [Webpack](https://webpack.js.org/) was the major player in the space, so I decided to try it out.

Honestly getting started with Webpack turned out to be easier than I expected. They have [good docs](https://webpack.js.org/guides/getting-started/) and I pretty much followed their guide through "Getting Started", "Asset Management", "Output Management" and "Development". I installed the Typescript plugin early in the process which took care of compiling the code easily enough.

Switch dependencies over to use NPM was very easy after I started using Webpack, just install dependencies the normal way and import them with the "normal" absolute path

```
import { render } from "preact";
```

Getting Webpack setup also allowed me to use their development server which automatically watches for changes, rebuilds everything, and refreshes the page in the browser if you have it open allowing for a very nice workflow. By default it tries to do hot module reloading which didn't seem to work properly for me but just having the page refresh is already a great workflow.

Another benefit of Webpack is making source maps work (it's possible they would have worked with the plain `tsc` build but I didn't know how). So now when I look at my page in the browser debugger it displays the actual Typescript source instead of the compiled Javascript. Also very nice.

After this step, my project pretty much resembles a "real" Javascript project. I'm only targetting client-side stuff for now so I don't need to figure out how to do server-side stuff in Node.

## TODO: Babel

The elephant in the room that I haven't touched is [Babel](https://babeljs.io/). This is a tool which "transpiles" Javascript, that is, converts Javascript into more Javascript with certain changes applied. Common uses are compiling fancy new features into backwards compatible forms or "pre-calculating" certain constructs so they don't need to be done at runtime.

Babel seems to be very commonly used and I've seen a bunch of references to Babel plugins to do various things.

I could use it to optimize my code, for example by pre-compiling HTM expressions into the corresponding object representation ahead of time. I haven't felt a need to set this up yet though.

# Conclusion

Working through each piece of the puzzle has been a huge help. A lot of tutorials want to chuck you in at the deep end to give you a working super-shiny app without bothering to explain how everything works. Now I have some confidence I understand all the bits that go into a proper Javascript app and what to google/what docs to read when something breaks.

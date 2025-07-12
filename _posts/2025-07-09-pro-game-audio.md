---
layout: post
title:  "Putting Professional Audio in a Toy Game Project"
date:   2025-07-12 12:00:00 +0000
categories: gamedev
---

I've wanted to learn more about sound design and audio programming in games for a while, but I haven't found a good way to jump in.
I came up with an idea to put one of the big professional audio systems into a toy game project and see if I could get it working.
Just really get in and sledgehammer that nut.

If you want to skip to the "finished product" you can see a video [here](https://youtu.be/Wt_Id0E54Rk).

There are basically 2 big options for game audio: FMOD and Audiokinetic WWise.
If you're a gamer, you've probably seen one of those two logos popup at the start of most big games you've played.

![FMOD and WWise Logos]({{site.url}}/assets/ps_logos.png)

I didn't really have a reason to prefer one over the other.
Both have fairly convincing lists of games they've been used in:

* [WWise](https://www.audiokinetic.com/en/wwise/powered-by-wwise)
* [FMOD](https://www.fmod.com/games)

In the end I landed on FMOD because it has a very clear free license for hobbyist/noncommercial/education usage, and just lets you download it (after creating an account which was trivial).
WWise on the other hand seemed to have a more involved process to register a non-commercial project which put me off.

My goal is to get 3 kinds of sound into the game:
* sound effects, preferably with fancy stuff like variable filters and randomised pitches etc
* background music
* speech/VO which will be me announcing over the game

## Step 0 - Make a game

I needed something to put audio into, so I whipped up a small pong project.
Nothing too exciting there, written in C++ with SDL3 to handle all the platformy stuff (with the obvious exception of audio).

I used CMake to build, although at this stage the build is completely trivial. We'll have to do some proper build systeming later though. Here's a GIF of it running at the first, simplest stage (although I did elaborate on it later).

![Basic Gameplay]({{site.url}}/assets/pis.gif)

## Step 1 - Adding FMOD and playing a sound

So first I downloaded FMOD from the website. This came in the form of an installer which "installed" the library files, headers, etc. into my program files directory.
I swiftly made a copy in my project, vendoring the dependency, as is common practice in games.
This makes the whole process of building easier.

Whenever you add a C/C++ library to a project you're flipping a coin as to whether it's going to be an easy 5 minute process or like 3 hours of decoding cryptic build system errors.
Integrating FMOD turned out to be straightforward, just a folder with some header files to add to the include path, a library to link against and a corresponding DLL to copy to the output.

Then to actually use it in code, we need to create and initialise a system. As a general note, for code examples I'm omiting error handling to keep them concise, but basically everything in FMOD returns an FMOD_RESULT which you have to check for error handling purposes. When I made mistakes (which I definitely did during development), the error code was a useful clue to what I did wrong.

```C++
FMOD::System *system = nullptr;
FMOD::System_Create(&system);
system->init(512, FMOD_INIT_NORMAL, 0);
```

FMOD supplies both a C and a C++ interface in seperate headers, throughout this I'm using the C++ interface which was convenient given I was already working in C++. The existence of a C interface means you could plug this in to pretty much any environment that supports FFI to C.

I also switched on the debug logging, sending it to a file as the easiest option. This also needs you to link against the logging version of the supplied FMOD libraries, which I did.

```C++
FMOD::Debug_Initialize(FMOD_DEBUG_LEVEL_ERROR, FMOD_DEBUG_MODE_FILE, 0, "fmod_log.txt");
```

We also need to make sure to give FMOD a chance to update itself in our game loop by calling `system->update()` once per frame.

Now we're ready to load up a sound file

```C++
FMOD::Sound* sound;
system->createSound("../blip.wav", 0, 0, &sound); // Hardcoded path for easy POC, real game would use its asset loading system
```

Then at the appropriate point in code we can play our sound. In this case I used this "blip" sound as the sound when the ball bounces off the paddle, so I stuck this code in where I detect a collision between the ball and the paddle.

```C++
FMOD::Channel* channel = nullptr;
system->playSound(sound, 0, false, &channel);
```

All things considered, pretty straightforward and I didn't have any issues getting this working.

## Step 2 - Using FMOD Studio

What we got set up with in the previous section was the FMOD Core API. This is still very useful and powerful, but it is relatively low level as you have to load up and play sound files yourself. If you want effects you have to construct them in code and fiddle with the audio routing manually, and so on.

What I really want to be using is the FMOD Studio GUI program where I can do all this sound designy stuff with a nice interface, and just be able to tell FMOD "trigger this sound event" from code. To do that, we need to switch over to the FMOD *Studio* API, as well as set up an FMOD project to design the audio.

After installing it, I started a new project in FMOD Studio. I added a single "bank" to hold my audio events. Banks are used to organise your audio events/assets and in theory you can have multiple that you use to manage loading different bits of audio at different times in the game. For my purposes I stuck to just creating one "General" bank.

Then I created an Event and stuck it in the bank. An FMOD Studio Event is the unit of audio that you trigger from the game. It can be as simple as a single sound effect or a whole timeline of multiple clips, with control flow, effects, parameterisation, mixing, etc. For this initial blip I just stuck to a single sound clip, but I added a random pitch shift in order to prove I'm getting something from using the Studio that I couldn't trivially do myself from code.

![Initial Event]({{site.url}}/assets/ps_initial_event.png)

The final step on the studio side is to build the project, which produces compiled bank files to load from the game. This is just a button press in a menu, and for my bare minimum project takes just a few seconds. I'm sure that a bigger project would have a proportionally longer build time though.

Hoping back over to the code, we need to pull in another library/DLL for the Studio API. Then we switch our initialisation to create a Studio System instead of just a System. The Studio System uses a normal FMOD System internally, and you can even retrieve it if you want to.

```C++
FMOD::Studio::System *system = nullptr;
FMOD::Studio::System::create(&system);
system->initialize(512, FMOD_STUDIO_INIT_NORMAL, FMOD_INIT_NORMAL, 0);
```

Then we need to load the bank files produced by our built project. We need to load up the "Master" bank, which contains global stuff for the project, the "strings" bank which has the data to map string event paths onto the GUIDs used internally by FMOD, and the actual bank file for the "General" bank I created in the project. We also tell the general bank to actually load the audio sample data so it's ready to play.

```C++
FMOD::Studio::Bank* master_bank;
system->loadBankFile("../../fspro/Build/Desktop/Master.bank", FMOD_STUDIO_LOAD_BANK_NORMAL, &master_bank); // More hardcoded paths, again a real game would load this as an asset

FMOD::Studio::Bank* strings_bank;
system->loadBankFile("../../fspro/Build/Desktop/Master.strings.bank", FMOD_STUDIO_LOAD_BANK_NORMAL, &strings_bank);

FMOD::Studio::Bank* bank;
system->loadBankFile("../../fspro/Build/Desktop/General.bank", FMOD_STUDIO_LOAD_BANK_NORMAL, &bank);
bank->loadSampleData();
```

Now we're ready to play the sound event we set up in the project. To do this we need to first lookup its "Event Description", then use that to create an "Event Instance", and then play that instance.

```C++
// Initialisation
FMOD::Studio::EventDescription* sound_event;
system->getEvent("event:/PaddleBounce", &sound_event);

// When we want to actually play the event
FMOD::Studio::EventInstance* instance;
sound_event->createInstance(&instance);
instance->start();
instance->release(); // This tells FMOD we aren't going to use this instance handle again so it will be cleaned up automatically when it finishes playing
```

With that, everything is working. We're playing the event as defined in FMOD studio, including the pitch shifting effect applied to the event. We can mess with the event in Studio and rebuild it to see the sound updated in game with no code changes.

## Digression - Parameters and Live Editing

A couple of nice features I was messing with at this point.
Parameters let you send data to a Studio Event and within the event the parameters can drive mostly anything: selecting different sounds to play, changing effect values, etc.
They are quite simple to use on the code side, just a single line to update a parameter on an instance. Here I set a parameter on my "PaddleBounce" event to tell it which player has been hit, and then play the bounce sound at a different pitch to create a pleasing "ping-pong" sound as the ball bounces back and forth.

```C++
instance->setParameterByName("Player", player_hit ? 0 : 1);
```

![Event parameter setup]({{site.url}}/assets/ps_parameters.png)

Parameters can also be set globally to control things like mixer channel volumes.

You can also use Studio to live edit the sound in the running game with almost no setup. Just pass the `FMOD_STUDIO_INIT_LIVEUPDATE` flag when initialising and then you can press the button in studio to connect. Then any updates you make will be immediately reflected in the running game. Very useful for setting mix volumes.

![Live Updating]({{site.url}}/assets/ps_live_update.png)

## Step 3 - Playing Music

On the face of it, playing background music is no different than playing any other sound: create an event in the Studio and play it when the game starts, job done. There are a few small wrinkles to think about.

First of all, for music tracks you usually don't want to load the whole track upfront, but instead stream it in as it plays. If I was doing everything myself I would probably want to use compression for background music, rather than an uncompressed format like .wav for sound effects. Luckily FMOD basically handles this all for you, including automatically setting the music tracks to stream rather than load up front. You can go in and manually set this on each sound file as well as set the compression format, but it seems to do something sensible by default.

![Music file settings]({{site.url}}/assets/ps_music.png)

The other thing is handling the music playlist. This can actually be done very easily within FMOD - you can use a "Multi Instrument" and drop all the music tracks inside it, then set the instrument to play them shuffled and loop itself. That will create an endlessly playing background music playlist with no work on the code side apart from starting it at the right time.

![Multi-instrument setup]({{site.url}}/assets/ps_multi.png)


I had a little side track at this point when I noticed that FMOD was always shuffling the playlist the same way. In the end I figured out that, unsurprisingly, it has a random seed that it uses for its randomisation and by default this is always set the same. By setting it differently each run I achieve actual random music. In order to do this we have to dig the "Core System" out from inside the "Studio System" which is a little clunky, and then I set the random seed to the current time in traditional fashion.

```C++
FMOD_ADVANCEDSETTINGS settings{ 0 }; // All settings default to 0 so we can ignore other fields
settings.cbSize = sizeof(FMOD_ADVANCEDSETTINGS); // Required or FMOD will reject the settings as invalid

SDL_Time currentTime;
SDL_GetCurrentTime(&currentTime);
settings.randomSeed = currentTime;

FMOD::System* core = nullptr;
system->getCoreSystem(&core);
core->setAdvancedSettings(&settings);
```

At this point I also set up the mixer in FMOD to seperate the music and sound effects into different channels which can have their volume set individually. This is pretty much the most basic setup possible but FMOD supports any arbitrary configuration of channels and subchannels and channels inside events etc. To do volume control settings you can use a global parameter to drive the mixer levels.

![Multi-instrument setup]({{site.url}}/assets/ps_mixer.png)

## Step 4 - Voice Over

Now if you read the FMOD manual, it has a section where it tells you the recommended way to handle speech and VO. Because most games have a ton of dialogue lines that also need to be localised, you don't want to set them all up as individual events. Instead you set up just one event with all the sound design stuff configured for the speech, with a "programmer instrument" inside it. This "programmer instrument" is a thing that doesn't actually play a sound itself, but instead sends a callback to your code so that you can pick what to play. Then in this callback you look up the actual dialogue line that's supposed to play and give it back to FMOD to play for the event.

To handle managing the sound files for all the voice lines, FMOD has a feature called "audio tables" where you can include sound files in the FMOD project without actually having them attached to events, but still get them built into the sound banks. Then your programmer instrument can look them up to be played rather than having to manage VO sound files seperately.

So having learned about the "right way" to do this, I then proceeded to ignore it. My toy game is only going to have about 3 voice lines and is not going to be localised, so just setting them up as bespoke events is going to be quicker and easier. Using that method, playing VO is just like playing any other sound. I set up another mixer channel for VO and slapped together some events.

I wanted to implement a countdown synced up with the VO counting down so I had to figure out how to get callbacks from an event as it plays. There are several callbacks you can opt into when an Event is playing but none of them seem to be explicitly for the purpose of "trigger something in code at a specific time". So the (possibly hacky) solution I ended up with was setting "destination markers" (which are supposed to be for control flow in an event) and enabling callbacks whenever a marker is passed. Doing this I was able to mark the points where the countdown should change in the event and get callbacks in code at the right time.

![Markers]({{site.url}}/assets/ps_markers.png)

On the code side we just create a callback function and subscribe to the callbacks when creating the event instance.

```C++
FMOD_RESULT readyCallback(FMOD_STUDIO_EVENT_CALLBACK_TYPE type, FMOD_STUDIO_EVENTINSTANCE *event, void* parameters)
{
    if (type == FMOD_STUDIO_EVENT_CALLBACK_TIMELINE_MARKER)
    {
        FMOD_STUDIO_TIMELINE_MARKER_PROPERTIES* props = static_cast<FMOD_STUDIO_TIMELINE_MARKER_PROPERTIES*>(parameters);
        // Do stuff with marker parameters here
    }
    return FMOD_OK;
}


//...

FMOD::Studio::EventDescription* ready_event = nullptr;
system->getEvent("event:/GetReady", &ready_event);
FMOD::Studio::EventInstance* ready_instance = nullptr;
ready_event->createInstance(&ready_instance);
ready_instance->setCallback(&readyCallback, FMOD_STUDIO_EVENT_CALLBACK_TIMELINE_MARKER);
ready_instance->start();
ready_instance->release();
```

By default the FMOD documentation says it might call these callbacks from audio threads, and thus you would have to worry about threading issues and locking any data used from them. This does not spark joy. However you can set the `FMOD_STUDIO_INIT_DEFERRED_CALLBACKS` flag and then it will only call your callbacks on the same thread when you call `system->update()` in the game loop. This ducks any possible threading issues.

## Conclusion

Well, we did it. FMOD-powered Pong. Once again, you can see a video of the final result [here](https://youtu.be/Wt_Id0E54Rk). Don't worry, I'm under no illusions that this sounds amazingly professional, but I was able to achieve somethings quite easily that would be challenging to do with only a lower level audio API. The main thing holding back the sound quality is the quality of the source assets - sound effects I generated quickly with [sfxr](https://sfxr.me/), royalty free background music from the internet, my own crappy voice acting...

I thought about trying to build a playable web version and uploading it. FMOD does support an Emscripten build so it should be possible. However I was having issues with getting SDL_ttf (used for the tiny amount of text rendering in the game) to build even on Windows so the prospect of making it all work on Emscripten felt like too much for this blog post. Remember when I said...

> Whenever you add a C/C++ library to a project you're flipping a coin as to whether it's going to be an easy 5 minute process or like 3 hours of decoding cryptic build system errors.

... for SDL_ttf I flipped a tails apparently. I ended up just dropping in prebuilt binaries for Windows but that won't help me with the Emscripten build.

I'm barely scratching the surface of what you can do with FMOD obviously. There are a ton of features I haven't looked at, and I've only used the ones I did touch in some of the most basic ways possible. I also didn't really do much actual "audio programming" here, leaving all that up to FMOD and just integrating it. But I do feel that I've dipped a toe into the world of sound design and had a learning experience in integrating game middleware/tooling.

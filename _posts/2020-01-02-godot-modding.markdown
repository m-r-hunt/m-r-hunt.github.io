---
layout: post
title:  "Making Godot Games Modable"
date:   2020-01-02 22:00:00 +0000
categories: gamedev
---

This article is about how to use Godot in the upcoming 8 Bits to Infinity "Shape Jam" in January 2020. More info is available on the [jam pages](https://itch.io/jam/shape-jam-ii-part-1) but here's a short summary: In part 1, develop a game with simple shapes for graphics but make it easily modable so someone else can replace the graphics and add sound. For part 2, take someone else's part 1 game and create the graphics and sound.

The simplest approach to using Godot for this jam is to just provide the Godot project source. Since it's a free and open source engine, anyone can download the engine and modify the game. This is a perfectly valid approach which doesn't really need more elaboration. There are some drawbacks to this approach. It requires a modder to download and use the engine (at least at a basic level), including rexporting the project after they are done making modifications.

Another way is to dynamically load assets at runtime from outside the Godot project and use them in the game. This is not totally straightforward and obvious to do in Godot, as it's outside the normal Godot asset pipeline. Here's some explanation on how to go about it. I'm using GDScript for my examples, but it should translate simply into other Godot scripting languages like C#.

I've got a test project demonstrating these techniques [on Github.](https://github.com/m-r-hunt/godot_modding_test_project)

When working normally in Godot, you place assets into the Godot project folder. Then, if they are in a format the engine understand, it will automatically import them and they will be available under the `res://` paths. In order to load assets outside of this process, we need to manually open and read files and then convert them into the proper Godot resource format using the functionality available in the engine.

## Reading bytes out of a file

This code snippet lets you open and read a file from the filesystem, creating a [`PoolByteArray`](https://docs.godotengine.org/en/3.1/classes/class_poolbytearray.html) of the file content.

```gdscript
    var file = File.new()
    file.open(filepath, File.READ)
    var bytes = file.get_buffer(file.get_len()) # bytes is a PoolByteArray
```

A note on the filepath: we don't want to use Godot's res:// or user:// style filepaths as these will not read out of the compiled project directory. Instead, using a simple relative path seems to do the trick. For example, using `test_image.png` as the filepath would read that image out of the same directory as the project executable.

It's probably possible for someone to break this by running the exe with a different working directory, but that's really their problem.

Finally, this will probably only work on desktop platforms (Mac/Win/Linux) and not on Web. Not sure about Mobile but that's unlikely to be relevant for a Game Jam.

## Loading Images

First we need to create an `Image` resource from our file bytes. Conveniently, the [`Image` class](https://docs.godotengine.org/en/3.1/classes/class_image.html) has a couple of methods to do the trick: `load_jpeg/png/webp_from_buffer()`.

Once we've got an Image, we can create an [ImageTexture](https://docs.godotengine.org/en/3.1/classes/class_imagetexture.html) from it using `ImageTexture.create_from_image().`

Example:

```gdscript
    var img = Image.new()
    img.load_png_from_buffer(bytes)
    var tx = ImageTexture.new()
    tx.create_from_image(img)
```

Then you can use this texture anywhere you need to as with any other texture, for example assigning it to a [`Sprite`](https://docs.godotengine.org/en/3.1/classes/class_sprite.html) to be drawn on screen:

```gdscript
    $Sprite.texture = tx
```

## Loading Sounds

Sounds are slightly trickier as there isn't a nice function to load the bytes into a sound. Instead we need to assign it to the data of the correct kind of [AudioStream](https://docs.godotengine.org/en/3.1/classes/class_audiostream.html) and make sure the settings are correct for the sound to play correctly.

[`AudioStreamSample`](https://docs.godotengine.org/en/3.1/classes/class_audiostreamsample.html) is used for WAV sounds and [`AudioStreamOGGVorbis`](https://docs.godotengine.org/en/3.1/classes/class_audiostreamoggvorbis.html) is used for OGGVorbis sound/music files. In both cases we assign the `PoolByteArray` to the `data` member of the stream. For an `AudioStreamSample`/WAV file the `format`, `stereo` mode and `mix_rate` must be set correctly for the WAV file in order for the sound to play correctly. Modders producing sounds will need to export their sounds with the correct settings.

WAV Example:

```gdscript
    var stream = AudioStreamSample.new()
    stream.data = bytes
    # Note these settings will need to be correct otherwise you'll get garbage sound.
    # Will need to be documented for modders.
    stream.format = AudioStreamSample.FORMAT_16_BITS
    stream.stereo = true
    # stream.mix_rate defaults to 44100 which seems to be standard
```

OGGVorbis Example:

```gdscript
    var stream = AudioStreamOGGVorbis.new()
    stream.data = bytes
    # OGGVorbis doesn't have any additional settings so this should hopefully just work
```

Then once you have an `AudioStream` you can assign it to the stream member of an [`AudioStreamPlayer2D`](https://docs.godotengine.org/en/3.1/classes/class_audiostreamplayer2d.html)/[`3D`](https://docs.godotengine.org/en/3.1/classes/class_audiostreamplayer3d.html) as usual for playback.

```gdscript
    $AudioStreamPlayer2D.stream = stream
```

## Conclusion

Using these techniques, you can make a Godot game modable without needing to provide the source, or require modders to run the full Godot editor. This should make for some fun Shape Jam projects. Enjoy!

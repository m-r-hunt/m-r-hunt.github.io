---
layout: post
title:  "Write your own Godot unit testing framework"
date:   2019-10-23 10:00:00 +0000
categories: gamedev
---

Godot gives you all the tools to write your own unit testing framework. You could use [one that someone's already written](https://github.com/bitwes/Gut), but it would take almost as long to install and learn someone else's framework as it would to roll your own, and your framework will be perfectly suited to your needs. Plus it's fun.

Here's how you do it:

Pick a place for your tests to live in your project. I have a `res://scripts` folder where all my scripts live, so `res://scripts/tests` is an obvious choice.

Create a new test runner script. This script should 

* [Iterate through the files in your test directory](https://godotengine.org/qa/5175/how-to-get-all-the-files-inside-a-folder)
* load() and instantiate any scripts it finds (load(filename).new())
* iterate through the methods on the resulting objects (using object.get_method_list())
* Call any methods which start with the string "test_" (using object.call())

Then create a scene with a single node, that has your test runner script attached. That's it, test framework complete. Run the scene to run the tests. You can write test scripts, put them in your tests directory, and any "test_foo" methods will get called. You can print out results, or assert to check things. [Here's my implementation of this minimal framework.](https://github.com/m-r-hunt/simple_godot_unit_testing)

Technical note: we use a scene to run tests to act as a sandbox for the test scripts. We *could* run them from a tool mode script in the editor, (or a plugin), but a misbehaving script could mess with the editor and cause detrimental effects.

Of course this simple framework has its problems. Any asserting test will crash the whole test run, for example. But now you get to decide how to proceed. Look at existing unit test frameworks from other languages for inspiration. Some ideas:

* pass a "testing" object into the test methods, with methods like testing.expect_equal(a, b). The testing object can compile a report based on what it sees and nicely display errors.
* add setup and teardown hooks that tests can implement if they need them (either using duck typing as above or by adding a test base class)
* add a command line test interface (https://docs.godotengine.org/en/3.1/getting_started/editor/command_line_tutorial.html#running-a-script)

One of my favourite things about Godot is that it's pretty easy to extend the engine and editor to do whatever you want, and running tests is just one example of that. Have fun testing your game!

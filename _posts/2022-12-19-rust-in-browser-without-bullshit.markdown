---
layout: post
title:  "Running Rust in the Browser with No Bullshit"
date:   2022-12-19 12:00:00 +0000
categories: programming
---

Web Assembly (WASM): it's the hot new(ish) way to compile and run native code in the browser. Rust has been improving WASM support for a while now, and I was working on a project which gave me cause to jump in and try and figure it out.

One thing I should note is that I strongly dislike the Javascript ecosystem and the overcomplicated tools, frameworks, and other stuff surrounding it. I don't understand half of the web stuff, I don't want to have to learn it, and I don't want to have to use a million dependencies and tools to run some Rust code in the browser. A big goal going into this was to navigate the world of Rust WASM without having to use Node.js, NPM, or anything like a Javascript bundler. Aiming for an absolute minimum of new tools and dependencies.

I wrote this up so I have a reference of what I did, and it could conceivably be useful for someone else. This will probably all be out of date in 6 months because it deals with web stuff.

## Getting Started

To start with, I mostly followed the guide in the [Rust Webassembly Book](https://rustwasm.github.io/docs/book/) which is linked to from the Rust homepage. I went off script from there to avoid having to pull in NPM and all that stuff.

### Rust Code

To start with, we can write some pretty normal Rust code that will ultimately get called from Javascript, and can call out to Javascript. We get out first dependency, [`wasm-bindgen`](https://rustwasm.github.io/docs/wasm-bindgen/) which handles generating bindings between Javascript and Rust. This really is pulling its weight, as intefacing Javascript and any Web Assembly stuff requires a bunch of grovelling around with memory buffers and function names. Basic code looks like this (lifted from the book):

```
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
extern {
    fn alert(s: &str);
}

#[wasm_bindgen]
pub fn greet() {
    alert("Hello, wasm-game-of-life!");
}
```

Note the `wasm-bindgen` annotations on extern functions to call out to JS, and on `pub` functions to get exported to JS.

Cargo.toml looks like this:

```
[package]
name = "wasm_example"
version = "0.1.0"
edition = "2021"

[lib]
crate-type = ["cdylib", "rlib"]

[dependencies]
wasm-bindgen = "0.2.83"
```

### Building for WASM

To build we need to:

* Build the crate for the `wasm32-unknown-unknown` target (with the toolchain potentially installed via `rustup`).
* Use `wasm-bindgen` to generate the JS glue code

I think we could theoretically use a script to do this manually, but there's a fairly convenient tool called [`wasm-pack`](https://rustwasm.github.io/wasm-pack/) which does this, as suggested by the book. Specifically to build we use this command:

```
wasm-pack build --target=web
```

The `--target=web` flag is important as it gets `wasm-pack` to build its output to be run directly in the browser. Apparently WASM loading isn't standardised and this tells `wasm-pack` to do the right thing for our use case.

This dumps some output in the `pkg` directory in the crate:

```
.gitignore
package.json
wasm_example.d.ts
wasm_example.js
wasm_example_bg.js
wasm_example_bg.wasm
wasm_example_bg.wasm.d.ts
```

Note: I didn't add that `.gitignore`, it's written out by `wasm-pack`. I guess it's trying to be helpful because `pkg` is not a normal Rust build directory and you might not already have it ignored? But it's a weird choice.

Anyway, we only need the .js files and the .wasm file. The other stuff is extraneous info for other tooling we're not using.

### Writing a webpage to consume our code

Next we need to write an actual webpage which uses our code. After a bit of trial and error, I came up with this html:

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Hello wasm-pack!</title>
  </head>
  <body>
      <script type="module">
          import init, { greet } from "./wasm_example.js";
          await init();
          greet();
      </script>
  </body>
</html>
```

This is fairly straightforward, it has an embedded script which loads up our module, initialises it, and calls our Rust function. If you need more info about Javascript modules and async/await, [MDN exists](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules).

### Serving it all up

In order for our webpage to actually function in the browser, we need to serve it via HTTP. Putting the html file above in the same directory as the files needed from the WASM build and serving it via any competent web server should get there.

Annoyingly the python builtin web server `python -m http.server` didn't work for local testing as it doesn't set the Content-Type header correctly on .js files. I wrote a little Python script based on it that does work though:

```
from http.server import *
import sys

SimpleHTTPRequestHandler.extensions_map[".js"] = "text/javascript"

class CustomServer(ThreadingHTTPServer):
    def finish_request(self, request, client_address):
        self.RequestHandlerClass(request, client_address, self,
                                 directory="www")

with CustomServer(('', 8000), SimpleHTTPRequestHandler) as httpd:
    host, port = httpd.socket.getsockname()[:2]
    url_host = f'[{host}]' if ':' in host else host
    print(
        f"Serving HTTP on {host} port {port} "
        f"(http://{url_host}:{port}/) ..."
    )
    try:
        httpd.serve_forever()
    except KeyboardInterrupt:
        print("\nKeyboard interrupt received, exiting.")
        sys.exit(0)
```

Note you'll need to set the directory appropriately if you haven't got all the files under `www`.

I also tested hosting the files on Itch.io where it worked without an issue. The html must be named `index.html`, and zipped up with the other files. You can then upload the zip file and tick "This file will be played in the browser" and it all works. This suggests it should work on most web hosting things, or if you're hosting it yourself you'll just have to make sure your webserver does The Right Thing and sets the Content-Type headers appropriately.

## Other Issues

The Rust and WASM and Rust-WASM ecosystems are all evolving fast, so I expect some of the information here will go out-of-date sooner or later. Going further than the basic example above, I've hit a few more issues when getting things working.

### Rust Dependencies

Any Rust dependencies you use will need to work on WASM. If they are self-contained "pure code" dependencies, they should just work. However anything that talks to the outside world will need to support WASM and specifically the `wasm-pack`/`wasm-bindgen` flavour of WASM as opposed to WASI or stdweb or emscripten or any other one(?). This can crop up in slightly surprising places, and transitive dependencies can cause problems too.

For example, I am using the Rust scripting language [Rhai](https://rhai.rs/) which explicitly states it supports WASM. To get it to work, I had to follow its documentation and enable the `wasm-bindgen` feature flag and the `js` feature on the `getrandom` crate, which is a transitive dependency. Building without the right flags threw up some cryptic errors which were a bit annoying to track down.

### Javascript Modules

This is a little note about JS modules. We had to use a JS module in our html above because the `wasm-bindgen` output is in modules, and only modules can import modules. If we need to use and non-module JS in our HTML, we'll have to figure out how to bridge the gap. The best I've found so far is to set fields on the `window` object inside the module, which become global variables in non-module JS. For example, this html can call the `greet` function when a button is clicked:

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello wasm-pack!</title>
</head>
<body>
<script type="module">
    import init, { greet } from "./wasm_example.js";
    await init();
    window.greet = function() {
        greet()
    }
</script>
<script></script>
<button onclick="greet()">Greet Rust</button>
</body>
</html>
```

The `onclick` handler is normal non-module JS but we've bridged the gap by exposing our WASM function on the `window` object.

## Conclusion

We got there. Rust running in the browser with only a small number of tools and dependencies, and a general feeling that I understand what's going on. We do have to write non-trivial HTML and Javascript as the "front end" to use our Rust code "back end", but that's pretty much inevitable with the current state of things. The only alternative is to have some prewritten equivalent like you get with emscripten, but that tends to bring its own headaches in my experience.

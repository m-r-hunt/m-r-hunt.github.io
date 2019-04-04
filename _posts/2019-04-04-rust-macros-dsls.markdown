---
layout: post
title:  "Rust Macros for DSLs, or, Embedding Lisp inside Rust Source"
date:   2019-04-04 20:00:00 +0000
categories: programming
---

Recently I've been experimenting with Rust macros, and I've come to the conclusion that they are quite powerful. More powerful than I had previously realised.

Some readers familiar with Rust may think this article is going to be about the relatively recent addition of procedural macros, but actually I've done everything using the old `macro_rules!` style macros.

As an experiment, let's try embedding Lisp inside Rust source. So, we'll write Lisp code inside an `rlisp!{}` macro invocation, and the macros will compile it to ordinary Rust. It's not "real" Lisp, just an alternate syntax for Rust, but this shows that macros are powerful enough to write DSLs with dramatically different syntax.

For the first step, let's compile a list style function call:

{% highlight rust %}
macro_rules! rlisp {
    ( ( $fn:tt $($args:tt)* ) ) => {
        $fn($($args),*)
    };
}

fn multiply(x: i32, y: i32) -> i32 { x * y }

fn main() {
    println!("{}", rlisp!{(multiply 2 10)});
}
{% endhighlight %}

This works, but the obvious problem is that you cannot nest calls. We can fix that easily with a bit of recursion:

{% highlight rust %}
macro_rules! rlisp {
    ( ( $fn:tt $($args:tt)* ) ) => {
        rlisp!($fn)($(rlisp!($args)),*)
    };
    ( $atom:tt ) => { $atom };
}

fn multiply(x: i32, y: i32) -> i32 { x * y }

fn main() {
    println!("{}", rlisp!{(multiply 2 (multiply 10 4))});
}
{% endhighlight %}

We can ditch the multiply `fn` by adding explicit operator support to the macro:

{% highlight rust %}
    ( ( * $arg1:tt $($args:tt)* ) ) => {
        $arg1 $(* $args)*
    };
{% endhighlight %}

This would have to be done by hand for all the other operators too.

What about defining functions? Well, then we have to be a bit careful because of the distinction between items and expressions in Rust. We can't define a global function in the middle of a print statement. My approach is to have a separate `rlisp_expr!` for the subset of rlisp that works in expression context, and make `rlisp!` expand to items. That allows us to both define named functions and have anonymous ones in expressions.

{% highlight rust %}
macro_rules! rlisp_expr {
    ( ( fn [$($args:ident : $types:tt)*] $ret:tt $body:tt) ) => {
        {
            |$($args: $types),*| -> $ret {  rlisp_expr!($body) }
        }
    };
    ( ( + $arg1:tt $($args:tt)* ) ) => {
        $arg1 $(+ $args)*
    };
    ( ( * $arg1:tt $($args:tt)* ) ) => {
        $arg1 $(* $args)*
    };
    ( ( $fn:tt $($args:tt)* ) ) => {
        rlisp_expr!($fn) ($(rlisp_expr!($args)),*)
    };
    ($atom:tt) => {$atom};
    () => {};
}

macro_rules! rlisp {
    ( ( defn $fn:ident [$($args:ident : $types:tt)*] $ret:tt $body:tt) $($rest:tt)*) => {
            fn $fn($($args: $types),*) -> $ret {  rlisp_expr!($body) }
            rlisp!{$($rest)*}
    };
    ( ( print $arg:tt ) $($rest:tt)*) => {
        println!("{}", rlisp_expr!($arg));
        rlisp!{$($rest)*}
    };
    ( ( $fn:tt $($args:tt)* )  $($rest:tt)*) => {
        rlisp_expr!($fn) ($(rlisp_expr!($args)),*);
        rlisp!{$($rest)*}
    };
    () => {};
}

fn main() {
    rlisp!{
        (defn square [x: i32] i32 (* x x))
        (print (square 10))
        (print ((fn [x: i32] i32 (+ x 23)) 2))    
    }
}
{% endhighlight %}

Note the use of the `$rest` to allow mutliple statements in a single invocation of rlisp. This is a technique called [incremental tt munching](https://danielkeep.github.io/tlborm/book/pat-incremental-tt-munchers.html) which enables a lot of complex macro code.

Now we're really flying, how about `let` statements? Again with two forms, one in expression context to create a local binding and one as an item to create a name usable outside of rlisp:

{% highlight rust %}
macro_rules! rlisp_expr {
    ( ( fn [$($args:ident : $types:tt)*] $ret:tt $body:tt) ) => {
        {
            |$($args: $types),*| -> $ret {  rlisp_expr!($body) }
        }
    };
    ( ( let [$($var:ident $value:tt)*]  $($rest:tt)*)) => {
        {
            $(let $var = rlisp_expr!($value));* ;
            rlisp_expr!{$($rest)*}
        }
    };
    ( ( + $arg1:tt $($args:tt)* ) ) => {
        $arg1 $(+ $args)*
    };
    ( ( * $arg1:tt $($args:tt)* ) ) => {
        $arg1 $(* $args)*
    };
    ( ( $fn:tt $($args:tt)* ) ) => {
        rlisp_expr!($fn) ($(rlisp_expr!($args)),*)
    };
    ($atom:tt) => {$atom};
    () => {};
}

macro_rules! rlisp {
    ( ( defn $fn:ident [$($args:ident : $types:tt)*] $ret:tt $body:tt) $($rest:tt)*) => {
            fn $fn($($args: $types),*) -> $ret {  rlisp_expr!($body) }
            rlisp!{$($rest)*}
    };
    ( ( let [$($var:ident $value:tt)*] ) $($rest:tt)*) => {
        $(let $var = rlisp_expr!($value));* ;
        rlisp!{$($rest)*}
    };
    ( ( print $arg:tt ) $($rest:tt)*) => {
        println!("{}", rlisp_expr!($arg));
        rlisp!{$($rest)*}
    };
    ( ( $fn:tt $($args:tt)* )  $($rest:tt)*) => {
        rlisp_expr!($fn) ($(rlisp_expr!($args)),*);
        rlisp!{$($rest)*}
    };
    () => {};
}

fn main() {
    rlisp!{
        (let [z (* 4 11)])
        (defn square [x: i32] i32 (* x x))
        (defn foo [x:i32] i32 (let [y (square 27)] (+ y x)))
        (print (square z))
        (print ((fn [x: i32] i32 (+ x 1)) 12))
        (print (foo 12))
    }
}
{% endhighlight %}

Hopefully my point has been made now, implementing other special forms like flow control is left as an exercise for the reader.

I'm not suggesting that actually using rlisp as I've defined it here is a good idea. Rather, I just wanted to show what you can do to define DSLs even with regular `macro_rules!` macros. `macro_rules!` does have its limits, but procedural macros add a whole new level of flexibility. I think you can really create any DSL you want, with the only restriction being that the input code must meet the loose requirements of Rust macro input (mainly needing balanced delimiters) and the output must be valid Rust code.

Of course, with great power comes greatly unreadable code. I find that reading macro source is hard on the eyes. Overuse of these techniques can easily create a maintenance headache. The Rust compiler does a heroic effort in trying to report macro problems as useful error messages, but often it has to give up and just tell you that something went wrong in a macro.

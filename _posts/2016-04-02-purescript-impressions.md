---
layout:     post
title:      PureScript Impressions
date:       2016-04-02 09:32:11
summary:    Trying to put some sense in JavaScript
categories: purescript
---

A couple of days ago I was checking [Elm](http://elm-lang.org). It's a very interesting project and I'm curious to see where it's headed to. While looking for resources about it, I stumbled on PureScript.

While Elm has a syntax that is inspired by Haskell, PureScript's one is almost identical, to the point that Haskell's didactic resources can be used to some extent.
Since I always wanted to learn Haskell, I think PureScript might be a better match for me than Elm.

###What's PureScript?

The official website keeps it very succint, by writing:

>PureScript is a small strongly typed programming language that compiles to JavaScript.

Not much! They probably assume that everyone checking the website is coming from a Haskell background. They also forgot to write in there that it's a purely functional programming language. I think it's pretty important.

The language is still very young, so it's still hard to find examples and learning material. Things are also changing quickly, but I think this is both a problem and something exciting!

###So, what does it look like?

Having no experience in Haskell whatsoever, there are things that look weird even in the most simple examples.

This code is taken from the tutorial (try it!):

```purescript
module Euler1 where

import Prelude

import Data.List (range, filter)  
import Data.Foldable (sum)

ns = range 0 999

multiples = filter (\n -> mod n 3 == 0 || mod n 5 == 0) ns

answer = sum multiples
```

The language is VERY modular. To the point that without that import Prelude line, you wouldn't even be able to sum numbers. Crazy! I wonder what's the choice behind this. I have a hard time imagining a piece of software that won't use any of [these](https://pursuit.purescript.org/packages/purescript-prelude/0.1.4/docs/Prelude) functions.

There's also a relative lack of parentheses. Functions arguments are listed after the function name, separated only by a space. Parentheses are mainly used for grouping when combining multiple functions.

From what I've seen, functions can be used both in prefix and infix position. For example, map is usually used like this:

```purescript
map (\n -> n + 1) [1, 2, 3, 4]  
```

But it can also be used as an infix operator by surrounding it with backticks:

```purescript
(+) :: forall a. (Semiring a) => a -> a -> a
```

The Semiring part is a class constraint and I'm still not entirely sure of how they work, but the part after the double arrow shows that (+) is a curried function, and that the result is of the same type of the arguments. After applying the two arguments, we don't have a function anymore but, for example, a Number.

In fact, all functions in PureScript take one argument only.

###The Ecosystem

The language is young (there's a bug in version 0.8.3 that make builds fail!), and so are the tools and libraries. I haven't spent a lot of time searching, but it seems that PureScript already has its fair share of React frameworks (and some alternatives too).

###The End

If I havent' scared or bored you to death, the next step after the tutorial is to follow [PureScript by Example](https://leanpub.com/purescript/read). Good luck!

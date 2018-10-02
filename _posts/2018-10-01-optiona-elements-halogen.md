---
layout:     post
title:      Optional elements and properties in Halogen
date:       2018-10-01 12:14:00
categories: purescript
---

Since Halogen's HTML, properties and classes are all arrays, dealing with
optional elments might not be immediatly obvious to the less experienced
Purescript coder (like me!).

For example, in most templating languages it's possibile to write something like
this (here I'm using handlebars):

```handlebars
{{#if user_name}}
Hello #{user_name}!
{{/if}}
```

Obviously we want to avoid something hideous like:

```haskell
HH.div_
  case maybeUsername of
    Nothing -> []
    Just n -> [ HH.text $ "Hello " <> n ]
```

This example might look ok here, but it quickly leads to unreadable code in more
real life scenarios.

## Classes and properties

### Monads to the rescue!

To optionally render classes and properties, we can use these operators:

```haskell
join :: forall a m. Bind m => m (m a) -> m a
guard :: forall m. MonadZero m => Boolean -> m Unit
($>) :: forall f a b. Functor f => f a -> b -> f b
```

In our case, `m` is actually `Array`. So we can rewrite them as:
```haskell
join :: forall a. Array (Array a) -> Array a
guard :: Boolean -> Array Unit
($>) :: forall a b. Array a -> b -> Array b
```

We are going to use `guard` and `($>)` to return either an empty array in case
the guarding fails, or an array containing just the single element when the
guard succeeds.

This is because `guard` returns `empty` when the condition fails, and `m Unit`
when it succeds. In our example, this is actually `[]` or `[unit]`.

```haskell
([] :: Array Unit) $> "hello" == ([] :: Array String)
[unit] $> "hello" == ["hello"]
```

From the example we can see that the penguin `($>)` replaces the element in the
array if present.

The last step is `join` which takes an `Array (Array a)` and returns `Array a`.

We can put all this together like this:

```haskell
HH.div
[ HP.classes $ join [ pure $ ClassName "hello"
                    , guard true $> ClassName "world"
                    , guard false $> ClassName "nope"
                    ]
]
```

Our div will have "hello" and "world" as classes. `pure` in our example is `[]`,
so it's:
```haskell
join [ [ClassName "hello"], [ClassName "world"], []] 
-- which becomes
[ ClassName "hello", ClassName "world" ]
```

### A better solution

Ok, it's not better, but I like it more.

The problem with the previous approach is that when an optional elment has to be
added, one needs to wrap all the other elements in `pure` and add a `join`.

We can abuse html to come up with a simpler solution:

```haskell
dummyAttr :: forall r i. HP.IProp r i
dummyAttr = HP.attr (HH.AttrName "data-dummy") ""

whenP :: forall r i. Boolean -> HP.IProp r i -> HP.IProp r i
whenP b p = if b then p else dummyAttr

maybeP :: forall a r i. Maybe a -> (a -> HP.IProp r i) -> HP.IProp r i
maybeP m p = maybe dummyAttr p m
  
whenC :: Boolean -> ClassName -> ClassName
whenC b c = if b then c else ClassName ""
```

We add either a fake attribute or an empty class when our conditions are not
met.

For example:
```haskell
HH.div
  [ HP.classes [ ClassName "hello"
               , whenC true $ ClassName "world"
               , whenC false $ ClassName "nope"
               ]
  , maybeP mTitle $ \t -> HP.title t             
  ]
```

## HTML elements

A good solution most of the time is just to extract the optional HTML to a
separate function:

```haskell
render state =
  HH.div_
    [ HH.text "Hello"
    , adminSection state.isAdmin
    , HH.text "World"]
    
adminSection false = HH.text ""
adminSection true = HH.div ...
```

We can write a couple of helper methods like the one we had for classes and
properties:

```haskell
whenH :: forall p i. Boolean -> (Unit -> HH.HTML p i) -> HH.HTML p i
whenH b k = if b then k unit else HH.text ""

maybeH :: forall a p i. Maybe a -> (a -> HH.HTML p i) -> HH.HTML p i
maybeH m k = maybe (HH.text "") k m
```

For example:
```haskell
render state =
  HH.div_
    [ HH.h1_ [ HH.text "Big title" ]
    , whenH state.isAdmin $ \_ -> HH.h2_ [ HH.text "Hello admin!" ]
    , maybeH state.todo $ \t -> HH.p [ HH.text $ "Remember: " <> todo ]
    ]
```

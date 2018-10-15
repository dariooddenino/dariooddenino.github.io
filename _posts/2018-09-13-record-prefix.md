---
layout:     post
title:      Automatically adding (or removing) a prefix to a record labels in Purescript
date:       2018-09-13 12:14:00
categories: purescript
---

Thanks to the awesome [heterogeneous](https://github.com/natefaubion/purescript-heterogeneous) 
library's dark magic powers, it's now very easy to do a lot of operations on non heterogeneous collections.

It's common in Haskell to have prefixed record fields labels, to avoid name clashing. For example:

```haskell
data User = User { userName :: Text, userPassword :: Password }
```

In Purescript this is not necessary, and I was starting to be annoyed by the fact that I had to use
unnecessary prefixes in the data structures I was using to send data to the backend.

To be honest, I have no idea yet if heterogeneous is necessary for this, but it helped me get to a solution
pretty easily.

Using `FoldingWithIndex`, I wrote a function that goes through a record's fields and builds 
a new record prefixing a specified string to all the labels.

I'll try to explain what's happening, even though some things are still not 100% clear to me.

The first step is to create a data structure. Heterogeneous needs this to dispatch constraints since
the record's fields are not all of the same type.

`sym` is the prefix we're going to add to the labels, and `SProxy` is a data type used to pass symbol
information around.

```purescript
data PrefixProps sym = PrefixProps (SProxy sym)
```

Then we write an instance for `FoldingWithIndex`. The actual function is pretty simple: it folds through the 
record's fields and uses `Record.builder` to create a new record using the prefixed labels.
The real magic is happening in the signature:

- `IsSymbol newsym` means that `newsym` is a symbol
- `Append presym sym newsym` means that our new symbol is the result of prepending the prefix to the old symbol
- `Row.Lacks newsym rb` means that the new prefixed symbol is not already present as a record label
- `Row.Cons newsym a rb rc` means that `rc` is the result of adding a field with label `newsym` and value `a` to `rb`

Then there's the class `FoldingWithIndex` representing a fold, with the arguments being our data type, the current symbol,
the accumulator Builder, the current value and the result Builder.

```purescript
instance prefixProps ::
  ( IsSymbol newsym
  , Append presym sym newsym
  , Row.Lacks newsym rb
  , Row.Cons newsym a rb rc
  ) =>
  FoldingWithIndex
    (PrefixProps presym)
    (SProxy sym)
    (Builder { | ra } { | rb })
    a
    (Builder { | ra } { | rc })
  where
  foldingWithIndex (PrefixProps _) _ rin a =
(_ >>> Builder.insert (SProxy :: SProxy newsym) a) rin
```

The last thing is the actual function that we are going to use. It calls `hfoldlWithIndex` using the class instance
we just wrote, and then uses `Record.builder` to create the actual record.

```purescript
add ::
  forall rin rout pre.
  HFoldlWithIndex (PrefixProps pre) (Builder {} {}) { | rin } (Builder {} { | rout }) =>
  SProxy pre ->
  { | rin } ->
  { | rout }
add pre = flip Builder.build {} <<< hfoldlWithIndex (PrefixProps pre) identity
```

Done! `remove` is basically the same, and here's how they can be used:

```purescript
foo :: { bar :: Int, baz :: Boolean }
foo = { bar: 1, baz: true }


bar :: { prebar :: Int, prebaz :: Boolean }
bar = add (SProxy :: SProxy "pre") foo

baz :: { bar :: Int, baz :: Boolean }
baz = remove (SProxy :: SProxy "pre") bar
```

You can find the code [here](https://github.com/dariooddenino/purescript-record-prefix).

If you want to learn more about heterogenous, watch [this video](https://www.youtube.com/watch?v=oNbkpZZAhgk)

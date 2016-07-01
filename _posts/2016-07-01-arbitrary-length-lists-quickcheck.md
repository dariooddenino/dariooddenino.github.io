---
layout:     post
title:      Arbitrary length lists with QuickCheck
date:       2016-07-01 16:32:11
summary:    Using **sized** to build arbitrary length lists for QuickCheck
categories: haskell
---

Following the amazing [Haskell Programming](http://haskellbook.com/) book, I was stuck 
for a while on the Monad chapter.
 
An exercise asks to write a `List` data type, implement its `Functor`, `Applicative` 
and `Monad` instances and then test them with QuickCheck and Checkers.

Having this data type:

```haskell
data List a =
      Nil
    | Cons a (List a)
    deriving (Eq, Show)
```

I had write its `Arbitrary` instance to generate values for the tests.

There's a useful function in QuickCheck called [sized](http://hackage.haskell.org/package/QuickCheck-2.8.2/docs/Test-QuickCheck-Gen.html#v:sized) 
that can be used for this.

The type signature is:

```haskell
sized :: (Int -> Gen a) -> Gen a
```

What's needed to use it is a function that takes a `Int` and returns a `Gen a`.

This is the function I ended up with:

```haskell
arbitraryList :: Arbitrary a => Int -> Gen (List a)
arbitraryList m
    | m == 0 = return Nil
    | m > 6 = arbitraryList 6
    | otherwise = Cons <$> arbitrary <*> (arbitraryList (m-1))
```
   
I capped the length at 6 to avoid super long lists.    
 

---
title: Working with newtypes
---

Using newtype deriving and [purescript-newtype](https://pursuit.purescript.org/packages/purescript-newtype/2.0.0) it's possible to work with newtypes without a lot of the boilerplate.

## Newtype deriving

```haskell
newtype EmailAddress = EmailAddress String

instance eqEmailAddress :: Eq EmailAddress where
  eq (EmailAddress s1) (EmailAddress s2) = eq s1 s2

-- Thanks to newtype deriving, we can replace it with:

derive newtype instance eqEmailAddress :: Eq EmailAddress

```
Any instance can be derived for any newtype, as long as there is an instance for the same class for the underlying type.

## Data.Newtype

Inside of `Data.Newtype` there are a lot of helper functions that make working with newtypes much easier.

Fist we have to derive a Newtype instance. We only have to provide the first type argument, representing the newtype itself.

```haskell
derive instance newtypeEmailAddress :: Newtype EmailAddress _
```

Now we have access to:

- `wrap` and `unwrap`:

```haskell
unwrap (EmailAddress "foo") -- "foo"
wrap "foo" :: EmailAddress -- EmailAddress "foo"
```

- `over`: lifts a function to operate over a newtype, similary to how `map` works with `Functor`.

```haskell
upperEmailAddress :: EmailAddress -> EmailAddress
upperEmailAddress = over EmailAddress toUpper
```

- `over2`: the same, but for binary operations.

```haskell
concatEmails :: EmailAddress -> EmailAddress -> EmailAddress
concatEmails = over2 EmailAddress (<>)
```

- `overF` and `overF2`: they are similar to `over` and `over2`, but the lifted function operates on values in a `Functor`.

```haskell
byDomain :: String -> Array EmailAddress -> Maybe EmailAddress
byDomain domain = overF EmailAddress (find (contains (wrap domain)))
```

- `under`, `underF`, `under2` and `underF2`: they are the opposite of the `over` functions. They lower a function that operates on a newtype to work on the wrapped value instead.

It's important to note that in all these function (`under` and `over` variants) the `Newtype` is polymorphic, so the return type can be a different newtype (but with the same underlying type).

```haskell
newtype Degrees = Degrees Number
derive instance newtypeDegrees :: Newtype Degrees _

newtype NormalDegrees = NormalDegrees Number
derive instance newtypeNormalDegrees :: Newtype NormalDegrees _

normaliseDegrees :: Degrees -> NormalDegrees
normaliseDegrees (Degrees deg) = NormalDegrees (deg % 360.0)

asNormalDegrees :: Number -> Number
asNormalDegrees = under Degrees normaliseDegrees -- this works because the `Newtype` is polymorphic
```

- `ala`: it's used when you have a higher order function that you want to use in the context of some newtype.

```haskell
ala additive foldMap [1, 2, 3, 4] -- 10
```

- `alaF`: useful for cases where you want to use an additional projection with the higher order function.

```haskell
alaF Additive foldMap length ["hello", "world"] -- 10
```

- `traverse`: traverses over a newtype.

```haskell
traverse Degrees (\x -> Just (x * 2.0)) (Degrees 1.0) -- Just (Degrees 2.0)
```

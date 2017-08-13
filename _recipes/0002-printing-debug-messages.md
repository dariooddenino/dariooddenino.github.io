---
title: Printing debug messages
---

Using [purescript-debug](https://pursuit.purescript.org/packages/purescript-debug/3.0.0/docs/Debug.Trace):

```haskell
import Debug.Trace (trace, traceShow, traceAny, spy, traceA, traceShowA, traceAnyA, traceShowM, traceAnyM)
```

First we have the "plain" trace functions: `trace`, `traceShow` and `traceAny`.

Each of them logs a message to the console and then returns a value. The return value is thunked so it is not evaluated until after the message has been printed, to preserve a predictable console output.

The difference is that `trace` takes a `String`, `traceShow` a `Show`able value, and `traceAny` takes any value, logging its underlying representation.

```haskell
trace :: forall a. String -> (Unit -> a) -> a
traceShow :: forall a b. Show a => a -> (Unit -> b) -> b
traceAny :: forall a b. a -> (Unit -> b) -> b
```

Some examples:
```haskell
foo :: Int -> Int
foo x = do
    trace ("Called foo with " <> show x) \_ ->
    x + 2

bar :: Int -> Int
bar x = do
    traceShow x \_ ->
    x * 2

newtype Baz = Baz Int

baz :: Baz -> String
baz x = do
    traceAny x \_ ->
    42
```

---

If you're in an `Applicative` context, you can use the `traceA` variants that return the `unit` value of the Applicative `a`:

```haskell
traceA :: forall a. Applicative a => String -> a Unit
traceShowA :: forall a b. Show b => Applicative a => b -> a Unit
traceAnyA :: forall a b. Applicative a => b -> a Unit
```

A simple example:

```haskell
main :: Eff () Unit
main = do
    -- some computations
    traceAnyA "Test"
    -- other computations
```

---

Then we have `traceAnyM` and `traceShowM` which are used inside of monadic chains:

```haskell
traceShowM :: forall m a. Show a => Monad m => a -> m a
traceAnyM :: forall m a. Show a => Monad m => a -> m a
```

A simple example:

```haskell
foo :: Maybe (Array Int) -> Int
foo mbArray = fromMaybe zero
    $ mbArray
    >>= traceAnyM
    >>= head
    >>= traceAnyM

-- Under each invocation I'm writing the console output when called from psci:

foo $ Just [1, 2]
-- [1, 2] (first traceAnyM)
-- 1 (second traceAnyM)
-- 1 (return value)

foo $ Just []
-- [] (first traceAnyM)
-- 0 (return value from fromMaybe)

foo Nothing
-- 0 (return value, there's no logging)
```

---

Finally there's `spy`, which logs a value and return it:

```haskell
spy :: forall a. a -> a
```

This example will log 3 and then assign it to `foo`:

```haskell
main = do
    --- stuff
    let foo = spy 3
    -- other stuff
```

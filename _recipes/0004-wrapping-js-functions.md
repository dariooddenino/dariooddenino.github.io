---
title: Wrapping JavaScript functions
---

We'll se how to wrap three types of functions:

- pure functions
- effectful sync functions
- async functions

## Pure functions

JavaScript functions are usually uncurried, i.e.:
```javascript
function foo (a, b, c) {
  return a + b + c;
}
```

Since in PureScript all functions are curried, we can save us a lot of boilerplate by using `Data.Function.Uncurried` from [purescript-functions](https://pursuit.purescript.org/packages/purescript-functions).

Continuing from the example above, let's say we have this in our `Main.js`:

```javascript
exports.fooImpl = function (a, b, c) {
  return a + b + c;
}
```

Then in our `Main.purs` we can write:

```haskell
import Data.Function.Uncurried (Fn3, runFn3)

foreign import fooImpl :: Fn3 Int Int Int Int

foo :: Int -> Int -> Int -> Int
foo = runFn3 fooImpl
```

They go up to 10 arguments and it's very unlikely that more would be needed!

## Effectful sync functions

There's a similar set of functions to work with effectful functions, and they're are under [Control.Monad.Eff.Uncurried](https://pursuit.purescript.org/packages/purescript-eff/3.1.0/docs/Control.Monad.Eff.Uncurried).

Let's say we have:

```javascript
exports.effectfulFooImpl = function (target, amount) {
  launchMissiles(target, amount);
}
```

Then we can wrap it like this:

```haskell
foreign import effectfulFooImpl :: forall eff. EffFn2 (missiles :: MISSILES | eff) String Int Unit

effectfulFoo :: forall eff. String -> Int -> Eff (missiles :: MISSILES | eff) Unit
effectfulFoo t a = runEffFn2 effectfulFooImpl t a
```

## Async functions

To work with async functions, I've found that the easiest way is to use `Control.Monad.Aff.Compat` from [purescript-aff](https://pursuit.purescript.org/packages/purescript-aff).

```javascript
// let's suppose get makes an ajax request.
exports.ajaxGetImpl = function (request) {
  return function (onError, onSuccess) {
    get(request, function (err, response) {
      if (err) {
        onError(err);
      } else {
        onSuccess(response);
      }
    });
  }
}
```

And then wrap it like this:

```haskell
foreign import ajaxGetImpl :: forall eff. Request -> EffFnAff (ajax :: AJAX | eff) Response

ajaxGet :: forall eff. Request -> Aff (ajax :: AJAX | eff) Response
ajaxGet = fromEffFnAff <<< ajaxGetImpl
```

It would be better to return a canceler, but you can find an example on `purescript-aff`'s documentation page.

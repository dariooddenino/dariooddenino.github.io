# Durations and Intervals operations

## Converting Durations

Since all the module types implement the `Duration` type class, we can use 
[convertDuration](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time.Duration#v:convertDuration)
to convert from a duration type to another.

```haskell
convertDuration :: forall a b. Duration a => Duration b => a -> b
```

The only thing we need to do is pass it one of the duration types 
([Days](/ditypes#days), 
[Hours](/ditypes#hours), 
[Minutes](/ditypes#minutes), 
[Seconds](/ditypes#seconds), 
[Milliseconds](/ditypes#milliseconds)
), and then specify an output duration type
(or use the result in a context where the compiler can infer the type).

```haskell
myMinutesDuration :: Minutes
myMinutesDuration = Minutes 60.0

myHoursDuration :: Hours
myHoursDuration = convertDuration myMinutesDuration -- Hours 1.0

-- And so on for the rest...
```

## Negating Durations

It's possible to negate a duration by using [negateDuration](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time.Duration#v:negateDuration):

```haskell
negateDuration :: forall a. Duration a => a -> a
```

For example:

```haskell
myDuration :: Days
myDuration = Days 10.0

myDurationNeg :: Days
myDurationNeg = negateDuration myDuration -- ^ -10 days
```

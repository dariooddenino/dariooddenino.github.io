# Intervals

This seems to be used mainly with formatters. Come back later after some tests.

## Interval

## RecurringInterval

## Duration

[Data.Interval.Duration](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Interval.Duration#t:Duration)
shouldn't be confused with the [Data.Time.Duration](/ditypes#duration-type-class) type class.

This `Duration` is a map from [DurationComponent](#durationcomponent) to the respective amount.
```haskell
newtype Duration = Duration (Map DurationComponent Number)
```

The module exposes a series of helper functions to build a `Duration` easily:
```haskell
year :: Number -> Duration
month :: Number -> Duration
week :: Number -> Duration
day :: Number -> Duration
hour :: Number -> Duration
minute :: Number -> Duration
second :: Number -> Duration
millisecond :: Number -> Duration
```

## DurationComponent

[Data.Interval.DurationComponent](https://github.com/purescript/purescript-datetime/blob/v4.1.1/src/Data/Interval/Duration.purs#L34-L34)
is a sum type whose constructors represent a type of duration.
Not to be confused with the newtypes in [Durations](#ditypes).

```haskell
data DurationComponent = Second | Minute | Hour | Day | Week | Month | Year
```


## IsoDuration

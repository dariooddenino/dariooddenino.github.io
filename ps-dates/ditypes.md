# Durations

## Duration type class

[Data.Time.Duration](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time.Duration#t:Duration) 
exports many duration data types, and they all implement the [Duration](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time.Duration#t:Duration) type class.

This allows us to easily [convert between duration types](/diops#manipulating-durations) and [calculate differences between dates](/dateops#difference-between-two-dates).

```haskell
class Duration a where
  fromDuration :: a -> Milliseconds
  toDuration :: Milliseconds -> a
```

## Days

`Days` is exported from [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time.Duration#t:Days), 
and represents a duration measured in days, where a day is assumed to be exactly 24 hours.

```haskell
newtype Days = Days Number
```

## Hours

`Hours` is exported from [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time.Duration#t:Hours), 
and represents a duration measured in hours.

```haskell
newtype Hours = Hours Number
```

## Minutes

`Minutes` is exported from [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time.Duration#t:Minutes), 
and represents a duration measured in minutes.

```haskell
newtype Minutes = Minutes Number
```

## Seconds

`Seconds` is exported from [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time.Duration#t:Seconds), 
and represents a duration measured in seconds.

```haskell
newtype Seconds = Seconds Number
```

## Milliseconds

`Milliseconds` is exported from [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time.Duration#t:Milliseconds), 
and represents a duration measured in milliseconds.

```haskell
newtype Milliseconds = Milliseconds Number
```

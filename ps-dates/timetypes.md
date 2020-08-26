# Time types and how to construct them

## Time

`Time` is exported from [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time#t:Time)

```haskell
data Time = Hour Minute Second Millisecond
```

We can use the `Time` constructor, but there's no way to directy build a value for [Hour](#hour), [Minute](#minute), [Second](#second) and [Millisecond](#millisecond).

What we can do is use [(<$>)](https://pursuit.purescript.org/packages/purescript-prelude/4.1.1/docs/Data.Functor#v:(%3C$%3E)) and [(<*>)](https://pursuit.purescript.org/packages/purescript-prelude/4.1.1/docs/Control.Apply#v:(%3C*%3E)) to build a [Maybe](https://pursuit.purescript.org/packages/purescript-maybe/4.0.1/docs/Data.Maybe#t:Maybe) value (or [lift4](https://pursuit.purescript.org/packages/purescript-prelude/4.1.1/docs/Control.Apply#v:lift4) the constructor):

```haskell
myTime :: Maybe Time
myTime = Time <$> toEnum 20 <*> toEnum 30 <*> toEnum 0 <*> toEnum 0 -- ^ 20:30:00

-- Can also be written as:
myTime :: Maybe Time
myTime = lift4 Time (toEnum 20) (toEnum 30) (toEnum 0) (toEnum 0)

-- Or by using Maybe's monad instance:
myTime :: Maybe Time
myTime = do
  h <- toEnum 20
  m <- toEnum 30
  s <- toEnum 0
  ms <- toEnum 0
  pure $ Time h m s ms
```


## Hour

`Hour` is exported from [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time.Component#t:Hour) and is in the 24-hours format.

`newtype Hour = Hour Int`

The `Hour` constructor is not exported, so we have to use [toEnum](https://pursuit.purescript.org/packages/purescript-enums/4.0.1/docs/Data.Enum#v:toEnum) to build it.

```haskell
myHour :: Maybe Hour
myHour = toEnum 22 -- ^ Values go from 0 to 23
```

## Minute

`Minute` is exported from [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time.Component#t:Minute).

`newtype Minute = Minute Int`

The `Minute` constructor is not exported, so we have to use [toEnum](https://pursuit.purescript.org/packages/purescript-enums/4.0.1/docs/Data.Enum#v:toEnum) to build it.

```haskell
myMinutes :: Maybe Hour
myMinutes = toEnum 22 -- ^ Values go from 0 to 59
```

## Second

`Second` is exported from [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time.Component#t:Second).

The `Second` constructor is not exported, so we have to use [toEnum](https://pursuit.purescript.org/packages/purescript-enums/4.0.1/docs/Data.Enum#v:toEnum) to build it.

```haskell
mySeconds :: Maybe Hour
mySeconds = toEnum 22 -- ^ Values go from 0 to 59
```


## Millisecond

`Millisecond` is exported from [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time.Component#t:Millisecond).

The `Millisecond` constructor is not exported, so we have to use [toEnum](https://pursuit.purescript.org/packages/purescript-enums/4.0.1/docs/Data.Enum#v:toEnum) to build it.

```haskell
myMilliseconds :: Maybe Hour
myMilliseconds = toEnum 300 -- ^ Values go from 0 to 999
```

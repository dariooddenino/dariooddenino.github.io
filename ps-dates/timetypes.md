# Time types and how to construct them

## Time

`Time` is exported from [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time#t:Time)

```haskell
data Time = Hour Minute Second Millisecond
```

We can use the `Time` constructor, but there's no way to directy build a value for [Hour](#hour), [Minute](#minute), [Second](#second) and [Millisecond](#millisecond).

What we can do is use [(<$>)](https://pursuit.purescript.org/packages/purescript-prelude/4.1.1/docs/Data.Functor#v:(%3C$%3E)) and [(<*>)](https://pursuit.purescript.org/packages/purescript-prelude/4.1.1/docs/Control.Apply#v:(%3C*%3E)) to build a [Maybe](https://pursuit.purescript.org/packages/purescript-maybe/4.0.1/docs/Data.Maybe#t:Maybe) value:

```haskell
myTime :: Maybe Time
myTime = Time <$> toEnum 20 <*> toEnum 30 <*> toEnum 0 <*> toEnum 0 -- ^ 20:30:00

-- Can also be written as:
myTime2 :: Maybe Time
myTime2 = lift4 Time (toEnum 20) (toEnum 30) (toEnum 0) (toEnum 0)
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

## Second

## Millisecond

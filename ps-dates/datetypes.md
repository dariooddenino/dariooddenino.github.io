# Date types and how to construct them

## Date

`Date` is exported by [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date#t:Date) and represents a date 
in the [Gregorian calendar](https://en.wikipedia.org/wiki/Gregorian_calendar).

```haskell
data Date = Date Year Month Day
```

If we already have the [Year](#year), [Month](#month) and [Day](#day) values, we can construct a `Date` in two ways (since the constructor is not exported):

- [canonicalDate](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date#v:canonicalDate) constructs a valid date
from the given values. For example *2016-02-31* will be corrected to *2016-03-02*.

```haskell
myDate :: Date
myDate = canonicalDate myYear myMonth myDay
```

- [exactDate](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date#v:exactDate) 
returns [Nothing](https://pursuit.purescript.org/packages/purescript-maybe/4.0.1/docs/Data.Maybe#t:Maybe) in case of an invalid date.

```haskell
myDate :: Maybe Date
myDate = exactDate myYear myMonth myDay
```

In case we need to build our values from scratch we have to deal with [Maybe](https://pursuit.purescript.org/packages/purescript-maybe/4.0.1/docs/Data.Maybe#t:Maybe) and we have a few ways to do so,
by using [(<$>)](https://pursuit.purescript.org/packages/purescript-prelude/4.1.1/docs/Data.Functor#v:(%3C$%3E)),
[(<*>)](https://pursuit.purescript.org/packages/purescript-prelude/4.1.1/docs/Control.Apply#v:(%3C*%3E)) and 
[lift3](https://pursuit.purescript.org/packages/purescript-prelude/4.1.1/docs/Control.Apply#v:lift3):

```haskell
myDate :: Maybe Date
myDate = lift3 canonicalDate (toEnum 1920) (toEnum 3) (toEnum 10)

-- or:
myDate :: Maybe Date
myDate = canonicalDate <$> toEnum 1920 <*> toEnum 3 <*> toEnum 10

-- or we can use Maybe's monad instance:
myDate :: Maybe Date
myDate = do
  y <- toEnum 1920
  m <- toEnum 10
  d <- toEnum 3
  pure $ canonicalDate y m d
  
-- The same can be done for exactDate, but we need to handle an "extra" Maybe:
myDate :: Maybe Date
myDate = join $ lift3 exactDate (toEnum 1920) (toEnum 3) (toEnum 10)

-- or:
myDate :: Maybe Date
myDate = join $ exactDate <$> toEnum 1920 <*> toEnum 3 <*> toEnum 10

-- Much better:
myDate :: Maybe Date
myDate = do
  y <- toEnum 1920
  m <- toEnum 10
  d <- toEnum 3
  exactDate y m d
```


## Year

`Year` is exported by [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date.Component#t:Year)

`newtype Year = Year Int`

The `Year` constructor is not exported, so the only way we have to build one is to use [toEnum](https://pursuit.purescript.org/packages/purescript-enums/4.0.1/docs/Data.Enum#v:toEnum)

```haskell
myYear :: Maybe Year
myYear = toEnum 1920
```

## Month

`Month` is exported by [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date.Component#t:Month)

```haskell
data Month
  = January
  | February
  | March
  | April
  | May
  | June
  | July
  | August
  | September
  | October
  | November
  | December
```

We can construct `Month`s directly, or by using [toEnum](https://pursuit.purescript.org/packages/purescript-enums/4.0.1/docs/Data.Enum#v:toEnum)

```haskell
myMonth :: Month
myMonth = January

myMaybeMonth :: Maybe Month
myMaybeMonth = toEnum 1 -- ^ 1 is January
```


## Day

`Day` is exported by [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date.Component#t:Day) and represents the day of the month (1..31).

`newtype Day = Day Int`

Since the `Day` constructor is not exported, we can use [toEnum](https://pursuit.purescript.org/packages/purescript-enums/4.0.1/docs/Data.Enum#v:toEnum)

```haskell
myDay :: Maybe Day
myDay = toEnum 23
```

## Weekday

`Weekday` is exported by [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date.Component#t:Day) and represents a day of the week in the [Gregorian calendar](https://en.wikipedia.org/wiki/Gregorian_calendar).

```haskell
data Weekday  
  = Monday
  | Tuesday
  | Wednesday
  | Thursday
  | Friday
  | Saturday
  | Sunday
```

We can construct `Weekday`s directly, or by using [toEnum](https://pursuit.purescript.org/packages/purescript-enums/4.0.1/docs/Data.Enum#v:toEnum)

```haskell
myWeekday :: Weekday
myWeekday = Wednesday

myMaybeWeekday :: Maybe Weekday
myMaybeWeekday = toEnum 3 -- ^ 1 is Monday
```

# Date types and how to construct them

## Year

`Year` is exported from [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date.Component#t:Year)

`newtype Year = Year Int`

The `Year` constructor is not exported, so the only way we have to build one is to use [toEnum](https://pursuit.purescript.org/packages/purescript-enums/4.0.1/docs/Data.Enum#v:toEnum)

```haskell
myYear :: Maybe Year
myYear = toEnum 1920
```

## Month

`Month` is exported from [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date.Component#t:Month)

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

`Day` is exported from [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date.Component#t:Day) and represents the day of the month (1..31).

`newtype Day = Day Int`

Since the `Day` constructor is not exported, we can use [toEnum](https://pursuit.purescript.org/packages/purescript-enums/4.0.1/docs/Data.Enum#v:toEnum)

```haskell
myDay :: Maybe Day
myDay = toEnum 23
```

## Weekday

`Weekday` is exported from [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date.Component#t:Day) and represents a day of the week in the [Gregorian calendar](https://en.wikipedia.org/wiki/Gregorian_calendar).

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

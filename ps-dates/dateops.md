# Date operations

## Extracting elements out of a Date

Extracting a [Year](/datetypes#year), [Month](/datetypes#month) or a [Day](#datetypes.md/day) out
of a [Date](/datetypes.md#date) is simple.

We can deconstruct via pattern matching:

```haskell
let (Date myYear myMonth myDay) = myDate
```

Or we can use the [year](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date#v:year),
[month](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date#v:month), 
[day](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date#v:day) 
and [weekday](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date#v:weekday) functions:

```haskell
let myYear = year myDate
    myMonth = month myMonth
    myDay = day myMonth
    myWeekday = weekday myMonth
```


## Adjusting a Date

[adjust](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date#v:adjust) adjusts a [Date](/datetypes#date) using a
[Duration](/ditypes#duration-type-class) in [Days](/ditypes#days). 

```haskell
adjust :: Days -> Date -> Maybe Date
```

The result is a [Maybe](https://pursuit.purescript.org/packages/purescript-maybe/4.0.1/docs/Data.Maybe#t:Maybe)
because the number of days must already be an integer and fall within the
valid range of values for the Int type, and the resulting date must be in the
valid dates range.

When converting durations (using [convertDuration](/diops#converting-durations)) to `Days` there's no guarantee that the end result will be an integer.
A possible solution could be using
[floor](https://pursuit.purescript.org/packages/purescript-math/2.1.1/docs/Math#v:floor):

```haskell
  -- let's say we have a duration in minutes.
  let (Days ds) = convertDuration minsDuration 
      newDate = adjust (Days $ floor ds) oldDate
```


## Difference between two Dates

To calculate the difference between two [Date](/datetypes#date)s we can use
[diff](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date#v:diff).

```haskell
diff :: forall d. Duration d => Date -> Date -> d
```

The result of `diff` is one of the types ([Days](/ditypes#days),
[Hours](/ditypes#hours), [Minute](/ditypes#minutes),
[Seconds](/ditypes#seconds), [Milliseconds](/ditypes#milliseconds)) which implements the
[Duration](/ditypes#duration-type-class) type class. We need to help the compiler
by specifying the type when it's not clear from the context.

```haskell

daysDiff :: Days
daysDiff = diff date1 date2

hoursDiff :: Hours
hoursDiff = diff date1 date2

```

## Finding the last Day of a Month

Given a [Year](/datetypes#year) and a [Month](/datetypes#month) we can get
the last [Day](/datetypes#day) of the `Month` by using
[lastDayOfMonth](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date#v:lastDayOfMonth).

```haskell
lastDayOfMonth :: Year -> Month -> Day
```

## Checking wheter a Year is a leap year

[isLeapYear](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Date#v:isLeapYear)
checks whether a [Year](/datetypes#year) is a leap year according to the proleptic
Gregorian calendar.

```haskell
isLeapYear :: Year -> Boolean
```

## Additional operations

- Finding first/last weekday of a month
- Finding last weekday before date
- Finding first weekday after date

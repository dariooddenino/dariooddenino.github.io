# Time operations

## Extracting elements out of Time

Extracint a [Hour](#timetypes/hour), [Minute](#timetypes/minute), 
[Second](#timetypes/second) and [Millisecond](#timetypes/millisecond) is simple.

We can deconstruct via pattern matching:

```haskell
let (Time myHour myMinute mySecond myMillisecond) = myTime
```

Or we can use the 
[hour](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time#v:hour),
[minute](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time#v:minute),
[second](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time#v:second) and 
[millisecond](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time#v:millisecond) functions:

```haskell
let myHour = hour myTime
    myMinute = minute myTime
    mySecond = second myTime
    myMillisecond = millisecond myTime
```

## Adjusting a Time value

It's possible to alter a `Time` value directly by using 
[setHour](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time#v:setHour), 
[setMinute](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time#v:setMinute), 
[setSecond](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time#v:setSecond) and 
[setMillisecond](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time#v:setMillisecond) 
to change one of its components.

```haskell
setHour :: Hour -> Time -> Time
setMinute :: Minute -> Time -> Time
setSecond :: Second -> Time -> Time
setMillisecond :: Millisecond -> Time -> Time
```

It's also possible to use 
[adjust](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time#v:adjust).

```haskell
adjust :: forall d. Duration d => d -> Time -> Tuple Days Time
```

This function adjusts a time value with a [Duration](/ditypes#duration-type-class) offset. The result includes 
a remainder value of the whole number of days involved in 
the adjustment, for example, if a time of 23:00:00:00 has a 
duration of +2 hours added to it, the result will be 1 day, 
and 01:00:00:00. 
Correspondingly, if the dureation is negative, a negative 
number of days may also be returned as the remainder.

## Difference between two Time values

To calculate the difference between two [Time](/timetypes#time)s we can use 
[diff](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.Time#v:diff).

```haskell
diff :: forall d. Duration d => Time -> Time -> d
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

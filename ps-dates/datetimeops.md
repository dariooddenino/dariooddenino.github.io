# DateTime operations

## Extracting a Date and a Time out of a DateTime

A [Date](/datetypes#date) 
and a [Time](/timetypes#time) 
can be extracted out of a [DateTime](/datetimetypes/#datetime) 
by deconstructing via pattern matching:

```haskell
let (DateTime myDate myTime) = myDateTime
```

We can also use [date](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.DateTime#v:date) and [time](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.DateTime#v:time):

```haskell
date :: DateTime -> Date
time :: DateTime -> Time
```

## Adjusting a DateTime

It's possible to alter a [DateTime](/datetimetypes#datetime) 
directly by using 
[modifyDate](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.DateTime#v:modifyDate), 
[modifyTime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.DateTime#v:modifyTime),
[modifyDateF](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.DateTime#v:modifyDateF) and 
[modifyTimeF](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.DateTime#v:modifyTimeF):

```haskell
modifyDate :: (Date -> Date) -> DateTime -> DateTime
modifyTime :: (Time -> Time) -> DateTime -> DateTime
modifyDateF :: forall f. Functor f => (Date -> f Date) -> DateTime -> f DateTime
modifyTimeF :: forall f. Functor f => (Time -> f Time) -> DateTime -> f DateTime
```

All these functions take as the first argument a function that modifies the specific `DateTime` component, a 
`DateTime` and return the new value.
The latter two are convenience functions which let us work with 
modifying functions that produce a [Functor](https://pursuit.purescript.org/packages/purescript-prelude/4.1.0/docs/Data.Functor#t:Functor).
This is very useful, for example, in conjunction with the [adjust](/dateops#adjusting-a-date) function which produces a 
[Maybe](https://pursuit.purescript.org/packages/purescript-maybe/4.0.1/docs/Data.Maybe#t:Maybe) 
(which is a `Functor`!).

For example:

```haskell
addThreeDays :: Date -> Maybe Date
addThreeDays = adjust (Days 3.0)

oldDateTime :: DateTime

newDateTime :: Maybe DateTime
newDateTime = modifyDateF addThreeDays oldDateTime

```

It's also possible to use [adjust](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.DateTime#v:adjust) directly:

```haskell
adjust :: forall d. Duration d => d -> DateTime -> Maybe DateTime
```

This function adjusts a `DateTime` value with a [Duration](/ditypes#duration-type-class) offset. 
[Nothing]() is returned if the resulting date would be outside 
the range of valid dates.

## Difference between two DateTime values

To calculate the difference between two 
[DateTime](/datetimetypes#datetime)s we can use 
[diff](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.DateTime#v:diff):

```haskell
diff :: forall d. Duration d => DateTime -> DateTime -> d
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

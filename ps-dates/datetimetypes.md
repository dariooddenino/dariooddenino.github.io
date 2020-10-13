# DateTime and how to construct it

## DateTime

`DateTime` is exported by [purescript-datetime](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.DateTime#t:DateTime) 
and represents a date/time value in the [Gregorian calendar](https://en.wikipedia.org/wiki/Gregorian_calendar)/[UTC time zone](https://en.wikipedia.org/wiki/Coordinated_Universal_Time).

```haskell
data DateTime = DateTime Date Time
```

We can construct a `DateTime` value by simply passing a [Date](/datetypes.md#date) and a [Time](/timetypes.md#time)
to it.

```haskell
myDateTime :: DateTime Date Time
myDateTime = DateTime myDate myTime
```

## Instant

An [Instant](https://pursuit.purescript.org/packages/purescript-datetime/4.1.1/docs/Data.DateTime.Instant#t:Instant) 
is a duration in milliseconds relative to the Unix epoch (1970-01-01 00:00:00 UTC).

The constructor is private, so to build one we can use:

```haskell
instant :: Milliseconds -> Maybe Instant

-- e.g.
myInstant = instant $ Milliseconds 1000.0
-- > Just (Instant (Milliseconds 1000.0))
```

This function takes a [Milliseconds](/timetypes#millisecond) value and tries to build an `Instant`.

The minimum acceptable value equates to the [bottom](https://pursuit.purescript.org/packages/purescript-prelude/4.1.1/docs/Data.Bounded#v:bottom) 
`DateTime` (if we pass negative values to go before the year 1970) and the maximum acceptable value equates to the [top](https://pursuit.purescript.org/packages/purescript-prelude/4.1.1/docs/Data.Bounded#v:top) 
`DateTime`.

If we already have a [Date](/datetypes#date) 
or a [DateTime](#datetime) we can use:

```haskell
fromDateTime :: DateTime -> Instant

fromDate :: Date -> Instant
```

`fromDate` uses the assumed time 00:00:00.

It's also possible to convert back by using:

```haskell
unInstant :: Instant -> Milliseconds

toDateTime :: Instant -> DateTime

-- e.g.
myDateTime = toDateTime <$> instant (Milliseconds 1000.0)
-- > Just (DateTime (Date (Year 1980) January (Day 1)) (Time (Hour 0)) (Minute 0) (Second 1) (Millisecond 0))
```

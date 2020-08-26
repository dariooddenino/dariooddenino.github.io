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


# Date operations

## Extracting elements out of a Date

Extract a [Year](#datetypes.md/year), [Month](#datetypes.md/month) or a [Day](#datetypes.md/day) out
of a [Date](#datetypes.md/date) is simple.

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


## Manipulating a Date

## Difference between two Dates

---
title: Basic handling of JSONs
---

When working with JSONs, we can use [purescript-simple-json](https://pursuit.purescript.org/packages/purescript-simple-json/0.10.0/docs/Simple.JSON) if we don't need to do complex handling.

The code is heavily inspired by the library's test code and by [this video](https://www.youtube.com/watch?v=zngNCvmIA1s)

We'll se how to read/write:
- simple objects with basic types
- newtype fields
- nested objects
- `null` and `undefined`

We'll use these helper types and functions:

```haskell
type E a = Either MultipleErrors a

handleJSON :: forall a. ReadForeign a => String -> E a
handleJSON = runExcept <<< readJSON


testFunc 
  :: forall a eff
   . ReadForeign a 
  => WriteForeign a 
  => Proxy a 
  -> String 
  -> Effect  Unit
testFunc _ s = case (handleJSON s) of
  Left err -> logShow err
  Right (r :: a) -> log $ writeJSON r
```

`readJSON` and `writeJSON` are provided by `purescript-simple-json`, and are the only functions really we need to work with JSONs (`runExcept` from `purescript-transformers` is useful too!).
The rest of the code is just to keep it DRY.

## Simple objects with basic types

In this example, we'll be working with this record:
```haskell
type SimpleRecord = { a :: Int, b :: String, c :: Boolean, d :: Array String }
```

Let's try it out with some test values. We'll be using our `testFunc` which basically does a roundtrip:
```haskell
simpleRecordSuccess = "{ \"a\": 2, \"b\": \"foo\", \"c\": true, \"d\": [ \"foo\", \"bar\" ]}"
simpleRecordSuccessToo = 
  "{ \"a\": 2, \"b\": \"foo\", \"c\": true, \"d\": [ \"foo\", \"bar\" ], \"e\": 2.0 }"
simpleRecordFail = "{ \"a\": 2 }"

testSimpleRecord =
  traverse_ (testFunc (Proxy :: Proxy SimpleRecord))
    [ simpleRecordSuccess -- it works!
    , simpleRecordSuccessToo -- it works and it "discards" the `field`.
    , simpleRecordFail -- we get an error complaining that `b` is `Undefined` instead of `String`
    ]
```

## Newtype fields

What if we need to work with a record like this?
```haskell
newtype Foo = Foo String
type NTRecord = { a :: Foo }
```

In this case there's a little more boilerplate involved, but it's just as easy:
```haskell
derive newtype instance rfFoo :: ReadForeign Foo
derive newtype instance wfFoo :: WriteForeign Foo

testNt =
  traverse_ (testFunc (Proxy :: Proxy NTRecord))
    [ "{\"a\": \"banana\"}" -- ok!
    , "{\"b\", \"banana\"}" -- `ErrorAtProperty`: "a" is "Undefined" instead of "String" 
    , "{\"a\": 2}"] -- same as above, but with "Number"
```

## Nested objects

What if we want to read a sub object of our JSON?

```haskell
nestedTest' = do
  let nested = _.a <$> wrappedRec
  case (runExcept nested) of
    Left err -> logShow err
    Right r -> log $ writeJSON r
  where wrappedRec :: F {a :: SimpleRecord}
        wrappedRec = readJSON $ "{ \"a\":" <> simpleRecordSuccess <> "}"
```

## null and undefined

The first option is to use `Maybe`:

```haskell
type MaybeRecord = { a :: Maybe String }

nullMaybe = "{ \"a\": null }"
undefinedMaybe = "{}"
definedMaybe = "{ \"a\": \"foo\" }"

maybeTest = traverse_ (testFunc (Proxy :: Proxy MaybeRecord))
  [ nullMaybe -- {"a": null}
  , undefinedMaybe -- {"a": null} WATCH OUT!
  , definedMaybe ] -- {"a": "foo"}
```

As you can see we need to be careful, as `undefined`s are written as `null`s too, so you could lose something in the translation.

If we use `NullOrUndefined` instead of `Maybe` we get the opposite behaviour (`null`s are writtend as `undefined`s).

```haskell
type NullRecord = { a :: NullOrUndefined String }

nullTest = traverse_ (testFunc (Proxy :: Proxy NullRecord))
  [ nullMaybe -- {}
  , undefinedMaybe -- {}
  ]
```

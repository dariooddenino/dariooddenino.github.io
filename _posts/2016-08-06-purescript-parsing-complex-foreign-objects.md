---
layout:     post
title:      Parsing complex foreign objects in PureScript
date:       2016-08-06 09:32:11
summary:    A way to deal with big objects and FFI.
categories: purescript
---

While trying to write a PureScript wrapper for the [aws-sdk](https://www.npmjs.com/package/aws-sdk) package, and in specific for the S3 interface, I was in need to parse the information coming back from the library calls.

For example, a call to `listBuckets` returns this kind of object:

```javascript
{ Buckets: [{ Name: "String", CreationDate: "Date object" }],
  Owner: { DisplayName: "String", ID: "String" }}
```

I was interested in the buckets array only, so at first I tried finding a solution using [purescript-foreign](https://github.com/purescript/purescript-foreign).

I modeled my bucket data type, together with its `IsForeign` instance:

```haskell
newtype Bucket = Bucket
    { name :: String
    , creationDate :: Either String DateTime
    }
    
instance bucketIsForeign :: IsForeign Bucket where
    read value = do
        name <- readProp "Name" value
        creationDate <- readProp "CreationDate" value
        pure $ Bucket { name, readDate creationDate } 
        
-- readDate is a function I write to concert js Date objects to ps DateTime objects.
```

I was then stuck when I tried to use my new instance to parse the bigger response object. The compiler didn't like the fact that I wasn't parsing the whole object. At first my solution was to write a data type representing the whole response, together with its `IsForeign` instance.

Thanks to this I was able to get my buckets array, but there was a lot of boilerplate involved, and I was scared that this approach would become unmanageable with more complex response objects.

I'm not sure if it's possible to find a better solution using just `purescript-foreign`, but I found a nice one enough using [purescript-foreign-lens](https://github.com/purescript-contrib/purescript-foreign-lens).

This is the example found in the library repo:

```haskell
doc :: Foreign
doc = toForeign { paras: [ { word: "Hello" }, { word: "World" } ] }

-- | This `FoldP` extracts all words appearing in a structure like the one above.
words :: forall r. Monoid r => FoldP r Foreign String
words = prop "paras"
    <<< array
    <<< traversed
    <<< prop "word"
    <<< string

main :: forall e. Eff (console :: CONSOLE | e) Unit
main = traverse_ log (doc ^.. words)
```

It parses the object, resulting in a list of strings. It was almost what I needed, I just had to find a way to replace that last `string` with a function that parsed buckets.

`string` is defined as:

```haskell
string :: forall r. Monoid r => FoldP r Foreign String
string = to readString <<< traversed
```

`readString` is a function from `purescript-foreign` that reads foreign strings. I already had my `bucketIsForeign` instance defined, so I crossed my fingers and wrote:

```haskell
bucket :: forall r. Monoid r => FoldP r Foreign Bucket
bucket = to read <<< traversed

buckets :: forall r. Monoid r => FoldP r Foreign Bucket
buckets = prop "Buckets"
      <<< array
      <<< traversed
      <<< bucket
```

And then, luckily, I was able to get a `List Bucket` by using `response ^.. buckets`.

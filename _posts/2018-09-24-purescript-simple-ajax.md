---
layout:     post
title:      Simple AJAX in Purescript
date:       2018-09-24 12:14:00
categories: purescript
---

I've recently published a small library that summarizes how I'm using affjax and simple-json in my project.

It's called [purescript-simple-ajax](https://pursuit.purescript.org/packages/purescript-simple-ajax), and its aim
is to provide beginners with a solution that works in most base cases when working with AJAX and JSON.

## Basic usage

The library assumes that the user is sending and receiving JSON. 
Thanks to the amazing [simple-json](https://pursuit.purescript.org/packages/purescript-simple-json/4.2.0)
library, decoding and encoding JSON takes zero effort in a lot of cases.

The basic methods that simple-ajax exposes are:

```haskell
get :: forall b. ReadForeign b => URL -> Aff (Either AjaxError b)
delete :: forall b. ReadForeign b => URL -> Aff (Either AjaxError b) 
delete_ :: URL -> Aff (Either AjaxError Unit)
post :: forall a b. WriteForeign a => ReadForeign b => URL -> Maybe a -> Aff (Either AjaxError b)
post_ :: forall a. WriteForeign a => URL -> Maybe a -> Aff (Either HTTPError Unit)
```

`put`, `put_`, `patch` and `patch_` have the same signature as `post` and `post_`.

Luckily, most basic types have an instance of `ReadForeign` and `WriteForeign`.

For example here encoding and decoding is done automatically, without needing to
write any additional line of code:

```haskell
type Foo = { foo :: Int, bar :: Maybe Boolean }

type Baz = { quuz :: Foo }

main = do
  res <- launchAff_ $ post { foo: 2, bar: Just true }
  case res of
    Left e -> -- we will deal with errors later on.
    Right v -> logShow v.quuz -- v is parsed as a Baz in this case.
```

## Modifying the request

Along the basic methods listed above, there are also versions which accept a
custom request object.

The methods are `getR`, `deleteR`, `deleteR_`, `postR`, `postR_`, `putR`,
`putR_`, `patchR`, `patchR_`.

The methods accept a subset of 
```haskell
type SimpleRequest = { headers         :: Array RequestHeader
                     , username        :: Maybe String
                     , password        :: Maybe String
                     , withCredentials :: Boolean
                     , retryPolicy     :: Maybe RetryPolicy
                     }
```

What this means is that if we need to pass the whole record, but just the field
we need to change from the basic request. E.g.

```haskell
getR { withCredentials: true } "http://www.foo.bar"
```

## Errors

The different types of error (`Error`, `ForeignError` and `ResponseFormatError`)
are put together in a `Variant`, and there are two type alias that can be used:

- `HTTPError` containing the HTTP errors
- `AjaxError` which extends `HTTPError` with JSON parsing errors

Using `Variant` tools we have a lot of power on how to deal with these errors.

- We can ignore the underlying type, and just handle the generic error case:

```haskell
case res of
  Left _ -> log "ERROR!"
```

- We can handle a default case and do something specific for the errors we are
interested in:

```haskell
case res of
    Left e -> 
      let mess = default "Generic error"
                 # on _notFound const "Not found"
                 $ e
      log mess
```

- Or we can be sure that all errors case are handled in a specific way:

```haskell
case res of
    Left e ->
      let mess = onMatch
        { badRequest: \s -> "Bad request: "  <> s
        , unAuthorized: \_ -> "Unauthorized"
        , forbidden: \_ -> "Nope"
        , notFound: \_ -> "Hello"
        , methodNotAllowed: \_ -> "!"
        , formatError: \err -> "Error: " <> renderForeignError err
        , serverError: \err -> "Server error: " <> err
        , parseError: \ers -> intercalate ", " $ map renderForeignError ers
        } e
      log mess
```

The possible errors are listed in the types `BasicError` and `ParseError`, along
with the eventual payload they carry.

## Retries

To enable retries, we can just pass a request with the `retryPolicy` field. E.g.

```haskell
getR { retryPolicy: defaultRetryPolicy } "http://foo.bar"
```

## Custom instances

If the compiler complains that our data types don't implement `ReadForeign` or
`WriteForeign`, we have some options:

- if we implemented those data types, we can just write the instances for them.
- if we are using data types from a library, we can either wrap them in a
  `Newtype`, or write two customs functions `Foo -> Foreign` and `Foreign ->
  Either MultipleErrors Foo`.
  I prefer the second approach, as generally I only need to read/write once, and
  I don't want to have to deal with wrapping and unwrapping a newtype all the time.
  
You can find more about this reading `simple-json` documentation.
   

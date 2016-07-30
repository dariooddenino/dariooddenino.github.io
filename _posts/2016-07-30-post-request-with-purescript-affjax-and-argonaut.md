---
layout:     post
title:      Post requests with PureScript Affjax and Argonaut
date:       2016-07-30 09:32:11
summary:    Simple ajax calls to an API example
categories: purescript
---

In this post I will explain how make a simple ajax call in PureScript, using [Affjax](https://github.com/slamdata/purescript-affjax) and [Argonaut](https://github.com/purescript-contrib/purescript-argonaut).

For this example I'm going to use [http://jsonplaceholder.typicode.com/](http://jsonplaceholder.typicode.com/) as my API endpoints. We will make a `POST` call to the `/posts` endpoint to create a new post.

A post has these fields: *title*, *body*, *userId* and *id*. To create a new one, we'll have to send an object containing the first three fields to the appropriate endpoint.

We can model our data type:

```haskell
data Post = Post { id :: Maybe Int
                 , title :: String
                 , body :: String
                 , userId :: Int
                 }
                 
post1 = Post { id: Nothing, title: "title", body: "body", userId: 20 }
```

We have our data, now we only need to find out how to send it to our endpoint! Affjax documentation tells us that:

> When sending data in a request the Requestable class enables automatic conversion into a format that is acceptable for an XHR request. Correspondingly there is a Respondable class for reading data that comes back from the server.

We need to implement a `Respondable` and a `Requestable` instance for our data type. The documentation doesn't tell us much more, and the type signatures are a little cryptic for a beginner.

Luckily, the instances are already defined for `Json`, so we can look at Argonaut for rescue!

We will need to implement `decodeJson` and `encodeJson` for our data type.

```haskell
instance decodeJsonPost :: DecodeJson Post where
  decodeJson json = do
    obj <- decodeJson json
    id <- obj .? "id"
    title <- obj .? "title"
    body <- obj .? "body"
    userId <- obj .? "userId"
    pure $ Post { id, title, body, userId }

instance encodeJsonPost :: EncodeJson Post where
  encodeJson (Post post)
    = "id" := post.id
    ~> "title" := post.title
    ~> "body" := post.body
    ~> "userId" := post.userId
    ~> jsonEmptyObject
```

Now we're ready to make our ajax call:

```haskell
main :: forall e. Eff (err :: EXCEPTION, ajax :: AJAX, console :: CONSOLE | e) Unit
main = void $ launchAff $ do
  res <- post "http://jsonplaceholder.typicode.com/posts" (encodeJson post1)
  liftEff $ log $ "response: " <> res.response
```

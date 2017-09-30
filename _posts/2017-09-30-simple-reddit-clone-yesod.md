---
layout:     post
title:      Planning a simple Reddit clone with Yesod
date:       2017-09-30 14:14:00
categories: hasell
---

# The plan

The plan is to build a very simplified Reddit clone using Yesod. It will be my first real project using it (and Haskell too, actually), and I hope I will learn a lot from the process.

The users will be able to post URLs. Other users will be able to give a vote and add comments (no threads for now).

There will also be a simple profile page.

The end goal is to integrate some halogen widgets for advanced front-end functionalities.

# Customizing the login page

My first goal is to find how to customize the login page. I want to prevent logged users from accessing the page and to change the allowed auth methods.

The second part is very easy: it's enoguth to change the `authPlugins` entry in te `YesodAuth App` instance.

## Preventing logged users from accessing the login page

The first part, luckily, is written as an example in Yesod's source.

```haskell
instance YesodAuth App where
  loginHandler = do
    ma <- lift maybeAuthId
    when (isJust ma) $
      lift $ redirect HomeR
    defaultLoginHandler
```

As in the code above, it's enough to implement the `loginHandler` method in the `YesodAuth` instance of the app (it's defined in `src/Foundation.hs`).

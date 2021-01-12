---
layout:     post
title:      Adding live css and js reload to Yesod
date:       2021-01-12 13:14:00
categories: haskell
---

By default Yesod does live reloading when editing `.hs` files or
Shakespeare template files. When working directly with js or css files this doesn't happen
and we're forced to manually reload the browser at every change.

This is a simple setup to improve this behaviour.

## Browser-sync

The first step is installing [browser-sync](https://browsersync.io/):

```
npm i -g browser-sync
```

Then we can run a server instance with:

```
browser-sync start --no-open --files="static" --proxy="localhost:3000" --port=4000
```

I'm making browser-sync watch the `static` folder, because that's
where all my built js and css files end up in.
You might need to change the ports according to your Yesod settings and whether
there's already some other process using port 4000 (in that case browser-sync tries
with 4001).

Start your server as usual (i.e. `stack exec -- yesod devel`) and then you can
visit `localhost:4000`, edit some css and see the browser refresh automatically :)

## Enabling CORS

Sadly, that's not enough to make everything work. If you try to trigger any AJAX
request, you will see that they probably don't work because of [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).

We can fix this by adding [wai-cors](https://hackage.haskell.org/package/wai-cors) to 
our project's `pacakge.yaml` dependencies.

Then go to `src/Application.hs`, we'll need a few simple edits there:

1) Add the import on top
```haskell
import Network.Wai.Middleware.Cors
```

2) Create a CORS middleware
```haskell
corsWare :: Middleware
corsWare = let
    appCorsResourcePolicy = 
      simpleCorsResourcePolicy { corsMethods = ["OPTIONS", "GET", "POST", "PUT", "DELETE"]
                               , corsOrigins = Just (["http://localhost:3000", "http://localhost:4000"], True)
                               , corsRequestHeaders = ["Authorization", "Content-Type", "x-requested-with", "x-xsrf-token"]
                               }
    in cors (const $ Just appCorsResourcePolicy)
```

3) Add the middleware to `getApplicationDev`, for example:
```haskell
getApplicationDev :: IO (Settings, Application)
getApplicationDev = do
    settings <- getAppSettings
    foundation <- makeFoundation settings
    startDequeue foundation
    startJobSchedule foundation
    wsettings <- getDevSettings $ warpSettings foundation
    app <- makeApplication foundation
    return (wsettings, corsWare app)
```

AJAX request should start working again.
Please be careful with CORS, as it could lead to serious security issues if
enabled on a live server.

## Reloading everything

My current setup includes building some js and purescript code in a subfolder 
and using compass to build sass files.
I've created this simple script to manage all build processes from a single 
terminal:

```bash
#!/bin/bash

trap killgroup SIGINT

killgroup(){
  echo killing...
  kill 0
}

browser-sync start --no-open --files="static" --proxy="localhost:3000" --port=4000 &
compass watch &
cd purescript; npm run watch &
wait
```

This allows me to close all three processes with a single `CTRL-C` and avoid 
having multiple terminals open.


I hope this was useful enough, see you next time!

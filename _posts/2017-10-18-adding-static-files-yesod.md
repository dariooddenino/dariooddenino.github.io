---
layout:     post
title:      Adding static files to Yesod
date:       2017-10-18 14:14:00
categories: haskell
---

In a Yesod app the files in the static folder can be accessed as a variable composed by the folder structure and the name of the file.
For example, a file in `static/css/main.css` will be `css_main_css`.

If the static folder is modified, the `StaticFiles.hs` file needs to be recompiled for Yesod to catch up with the changes.

Another solution is to add `-fforce-recomp` to the `ghc-options` in the `package.yaml` file:

```yaml
library:
  source-dirs: src
  when:
  - condition: (flag(dev)) || (flag(library-only))
    then:
      ghc-options:
      - -Wall
      - -fwarn-tabs
      - -O0
      - -fforce-recomp
      cpp-options: -DDEVELOPMENT
    else:
      ghc-options:
      - -Wall
      - -fwarn-tabs
      - -O2
```

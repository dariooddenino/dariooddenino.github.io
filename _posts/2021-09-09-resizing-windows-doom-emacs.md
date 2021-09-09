---
layout:     post
title:      Resizing and moving windows in Doom Emacs
date:       2021-09-09 10:14:00
categories: emacs
---

The keybindings that I use the most when working with windows in Doom Emacs are:

- `SPC w v` and `SPC w s` to split a window vertically and horizontally
- `SPC w w` to move to the next window
- `SPC w h/j/k/l` to move to a window in a specific direction
- `SPC w H/J/K/L` to move a window in a specific direction
- `SPC w a` to delete the other windows
- `SPC w q` to close the current window

Resizing the windows is not as smooth. My usual workflow consisted in just closing windows and/or using `SPC w =` to rebalance the space between the open ones.
This solution was suboptimal, and after months of dissatisfaction I decided to check the other available commands and stop being so lazy.

The first solution I found was using `SPC w |` and `SPC w _` to set a window's width and height respectively. On their own, these commands are not very useful as they arbitrarily expand the current window, but vim powers come to the rescue!
We can just do something like `80 SPC w |` to resize the current window to 80 columns width.

We can still improve the workflow a little more. Sometimes guessing the correct size is not easy, and resizing windows with something like `SPC w >` or `SPC w +` is annoying because the whole key sequence has to be repeated for each step.

Luckily we can use Doom's [hydra](https://github.com/hlissner/doom-emacs/tree/develop/modules/ui/hydra) module. 
We need to add it to `init.el` :ui section

```lisp
:ui
...
hydra
...
```

Then define this function (for example in `config.el`):

```lisp
(defhydra doom-window-resize-hydra (:hint nil)
  "
             _k_ increase height
_h_ decrease width    _l_ increase width
             _j_ decrease height
"
  ("h" evil-window-decrease-width)
  ("j" evil-window-increase-height)
  ("k" evil-window-decrease-height)
  ("l" evil-window-increase-width)

  ("q" nil))
```

The last step is binding this function to a key sequence:

```lisp
(map!
    (:prefix "w"
      :desc "Hydra resize" :n "SPC" #'doom-window-resize-hydra/body))
```

Now we can just press `SPC w SPC` and then hold one of `h`, `j`, `k` or `l` to resize the current window.



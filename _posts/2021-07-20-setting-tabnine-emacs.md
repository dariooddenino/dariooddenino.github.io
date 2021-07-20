---
layout:     post
title:      Setting up TabNine on Doom Emacs
date:       2021-07-20 10:14:00
categories: emacs, tabnine
---

I was curious to try [TabNine](https://www.tabnine.com/), so I purchased a pro subscription to test it.

The results were not very satisfying at first, but the problem was that my Emacs
configuration was not correct. TabNine suggestions were buried among company's 
default ones, making it faster for me to just type what I wanted instead of scrolling
through the suggestions.

After a bit of trials I finally found the correct way to configure it on Doom Emacs.

Add this line to `packages.el` to install company-tabnine:

```lisp
(package! company-tabnine :recipe (:host github :repo "/TommyX12/company-tabnine"))
```

Then add to `config.el`:

```lisp
(after! company
  (setq +lsp-company-backends '(company-tabnine :separate company-capf company-yasnippet))
  (setq company-show-numbers t)
  (setq company-idle-delay 0)
)
```

The `:separate` key is what makes company show Tabnine suggestions before the standard ones.

After this, run the usual `bin/doom upgrade` and, once Emacs has started, run `company-tabnine-install-binary`.

Done! After this remember to configure TabNine and enable semantic completion for the languages you use. 

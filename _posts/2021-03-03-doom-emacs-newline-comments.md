---
layout:     post
title:      Adding a newline after a comment block in Doom Emacs
date:       2021-03-03 11:14:00
categories: emacs
---

Adding a newline at the end of a comment block is something that always
bothered me in Doom Emacs.

The default behaviour when pressing `o` is to expand the comment section
to the new line, but sometimes that's not what I want.

The solution was to edit my `config.el` to add this:

```lisp
(defun append-line-comment-block ()
  "Appends a new line after a comment block without expanding it.
Calls `evil-append-line` and `+default/newline` in sequence."
  (interactive)
  (call-interactively 'evil-append-line)
  (call-interactively '+default/newline)
  )

(map!
  (:prefix "g"
    :desc "New line after comment block" :n "o" #'append-line-comment-block
   ))
```

Now I have two options.

Using `o` I can expand the comment block with a new line:

```javascript
/**
 *
 */|  <~ pressing `o` with cursor here
 
 |
 V

/**
 *
 *
 */| <~ gives me a new line inside of the block
```

Using `g o` I can add an empty line below the comment block:

```javascript
/**
 *
 */| <~ pressing `g o` with the cursor here
 
 |
 V
/**
 *
 */
| <~ gives me a new empty line below the block
```

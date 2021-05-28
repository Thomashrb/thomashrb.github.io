---
layout: post
title:  Emacs major modes
category: Emacs
---

I have used emacs as my main editor for around six years now.
I keep my own emacs.d and regularly add, remove and configure
packages. These packages consist of major and minor modes to
extend emacs. I have however yet to write my own mode in emacs.
From before I know that major mode allows re-binding keys, altering how text is shown
and I would like learn more about how it works so I have
spent the day writing games to explore major modes.

I started by looking at games other people have [written](https://www.emacswiki.org/emacs/CategoryGames)
and decided that a game that looked simple(st) to re-write was
tictactoe.

I compiled a small [repository](https://github.com/Thomashrb/emacs-games) of games from todays experimentation
but to summarize the biggest things I learned was


1. elisp does not support multidimensional arrays
2. `(interactive)` allows a function to be called with `M-x`
3. defining a major mode is easy and you can even extend an existing major mode
   (in this case special-mode is extended to get some default values and behavior) like so:
```elisp
(define-derived-mode tictactoe-mode special-mode "tic-tac-toe"
  (define-key tictactoe-mode-map (kbd "SPC") '<function you want to call with spacebar>))
```
4. syntax highlighting is also straight forward to achieve by overloading
   `font-lock-defaults` and can be done like so:
```elisp
;; this will highlight WINNER and hit as types while _ is highligted as constants and lables
;; using the highlighting specified by your emacs theme
(setq font-lock-defaults
    '((("_" . font-lock-constant-face)
       ("WINNER\\|hit" . font-lock-type-face))))
```

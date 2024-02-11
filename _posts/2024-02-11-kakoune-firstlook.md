---
layout: post
title: Kakoune a fresh take on editors
category: editors
---

#### Modal editors are fun

Editing and exploring text in a modal editor is fun.
In editors like [Kakoune](https://kakoune.org/) and (Helix)[https://helix-editor.com/] it is even more fun.

Modal editing is flipped on its head;
where you in vim you state what you want to do/execute first and then how much of it/where,
in kak/hx you first state how much/where and then what you want to do.

In practice this means that you first select an area or text and then you state what you want to do with.
Ex deleting two words forward:

- in vim: `d2w`
- in kak: `2wd`
- in hx: `v2wd`

This concept also extends to things like search and replace where you would use macros etc
because kak and hx are both built around using multiple cursors. Multiple cursors besides
giving clear visual feedback on what you are about to do is also a really fun way to edit
text.

#### Why am I drawn to Kakoune specifically

It is not because Kakoune is written in c++ (helix is written in rust btw) but because it tries to stick to the unix philosopy of
only doing one thing and doing it well. This means that instead of including stuff like file explorers, lsp, treesitter, vcs integrations, tabs, windows,
or even more esoteric stuff like sorting text (which vim has included), or if you want to do a search and replace in a big file where multiple cursors in unwieldy
kakoune makes it easy to integrate with these things and even uncludes utilities to pipe text out to command line ulitities.
For windowing and tabs Kakoune either opens up new windows on the os or even better uses tmux for window handling using its client/server architecture.
Why implement and commit to maintining something that already exists and probably does a better job?
Using the example of sorting text which in vim is build in; you can select the lines of text you want to sort `pipe sort` (pipe is per default hotkeyed as `|` even)
Kakounes lack of file explorer has lead me to discovering the [broot](https://github.com/Canop/broot) which I didnt know I was missing before I found it,
and is easy to integrate into my kakoune work flow.

So far I have found that using `pipe` and even `append-output` which runs a shell command and appends its output into the buffer allows
me to work the same within my editor as I do outside of it affording me less time to think about where I am and how to do it and more
about what I am doing. I know this is also possible in vim with ! % and the angled brackets its just more streamlined in Kakoune
where it is "the" way of doing things.

Oh and clippy is back!

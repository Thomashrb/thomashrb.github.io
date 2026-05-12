---
layout: post
title: Creating custom syntax highlighting for kakoune
category: editors
---

#### The missing syntax highlighter

I recently got the urge to try my hand at coding c# in my favourite modal editor [Kakoune](https://kakoune.org/).
However to my suprise I found that csharp was not one of the syntax highlighters that came with kakoune.

#### 10 minutes later

Initially when I [confirmed that this was the case](https://github.com/mawww/kakoune/issues/4354) I thought it would be a slog to add syntax,
but following the suggestion in this ticket and just adapting the java syntax it turns out I could have syntax highlighting working
very quickly.

![]({{site.baseurl}}/assets/img/csharp_kak_highlight.jpg)

I created a repo at https://github.com/Thomashrb/csharp.kak to keep track of it.

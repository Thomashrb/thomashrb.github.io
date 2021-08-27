---
layout: post
title:  Exploring frida
category: Reverse
---

[Frida](https://frida.re/docs/home/) is a reverse engineering tool
that allows for dynamic (no need to restart) code injection
into native apps which in turn can enable both exploration of
obfuscated code as well as changing the logic of running programs.

I have previously attended a [presentation](https://www.youtube.com/watch?v=S8a8ym-FGnA) from the author of
Frida where he introduced and demoed the tool. I am however as a general
beginner in this space. So today I tried to re-create
and 'follow a long' this presentation in an attempt
to gain a more practical understanding of how to
use Frida.

### Overview

![]({{site.baseurl}}/assets/img/frida/overview.png)

The overview (taken from the presentation above) of how Frida works
is as follows:

Given us (the Debugger) and the program/process (Debugee)

1) Memory is allocated in the Debugee process. This memory contains some code.
2) A thread is created inside the Debugee process that can execute the code injected in step 1.
3) Once the code is executed (in step 2) a library (frida-agent.so) is loaded.
   This library sets up a communication channel accessible from outside the process. (to us, the Debugger)
4) The communication channel can then be used to inject code into the process (in this case JavaScript)


### How do we go about loading a script into a program

I have gone ahead and created (copied from the presentation :))
a program that runs forever counting from 1 -> infinity
this program is called "number_is" and looks like this:

``` c
#include <stdio.h>
#include <unistd.h>

static void
f (int i)
{
  printf("Number is %d\n", i);
}

int
main ()
{
  int i = 1;

  // print where in memory the function is
  printf("f is at %p\n", f);

  while(1)
    {
      f(i++);
      sleep(1);
    }

  return 0;
}
```


In order to inject our script `explore.js` into the running process of
this program we can use the following snippet that
uses the python bindings of frida.

``` python
import frida
import codecs
import sys

#1 attach to process
session = frida.attach("number_is")
#2 load script that we want to inject
with codecs.open("explore.js", "r", "utf-8") as f:
    source = f.read()
#3 load script over opened communication channel into process
script = session.create_script(source)
#4 run (now loaded) script
script.load()
```

There also exists a repl for frida however so the above can also be accomplished
without having to write the python boilerplate yourself:

``` bash
$ frida number_is -l explore.js
```


### What do we inject

The script we are injecting into the process may be (and is in our case) a JS-script.
It can for instance look like this:


``` javascript
// attach to the function
Interceptor.attach(ptr('<find function pointer to put here>'), {
    // before the function is called
    onEnter: function(args) {
        // print argument
        console.log('f() n=' + args[0].toInt32());
    }
});
```

### Summary

I have now re-created a working frida demo and my frida feet are wet.
(unfortunately sharing the output of the above is hard to do in a blog
friendly way but please try it for yourself if you are interested)

I still have much to learn on this subject.
The next time I re-visit this subject I will attempt to create something from scratch
using frida.

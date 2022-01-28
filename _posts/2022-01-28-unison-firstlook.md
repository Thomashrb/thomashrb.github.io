---
layout: post
title:  A first look at unison language
category:  unison
---

# Its not a philishave

![]({{site.baseurl}}/assets/img/unisonlogo.png)

_Its unison![unison](https://www.unisonweb.org) is a (as of writing this) alpha release language_
_I am by no means an expert in unison so the following is my summary after a deep dive in the [documentation](https://www.unisonweb.org/docs)_

The lowdown of unison is that it is a functional language that applies the concept of [content addressed storage](https://en.wikipedia.org/wiki/Content-addressable_storage) to the how
code and the `codebase` is managed. Unlike other languages where the codebase is a set of files in unison the `codebase` is a database of content addressable definitions.

At the heart of how the `codebase` works are hashed functions. Unison stores and manages these hashes as well as the ast that makes up the program.
One can think of a unison program as a graph where every node is a definition that can refer to other definitions. For the programmer to be able
to work on the `codebase` unison can render these ASTs to and from text files.


### Hashed functions

In order to enable content addressing unison will hash all your functions.
However it does so by unifying the functions so aliases does not effect the hash.

Lets attempt to psudo-hash the following function as unison would do it:

```unison
xum : [Nat] -> Nat
sum xs = foldLeft (+) 0 xs
```

The first thing unison does is to "normalize" the function.

| sum | xs    | foldLeft   | (+)       | 0 | xs    |
|-----|-------|------------|-----------|---|-------|
| _   | $arg1 | #abcde1234 | (##Nat.+) | 0 | $arg1 |

- sum      - is the function name but we do not care about that because the aim is to create a function name (hash)
- xs       - is exchanged for a indexed argument name
- foldLeft - is exchanged for its hash (here represented with just the start of the hash)
- 0        - constants are stored as constants
- (+)      - this is a built in function so is a special case of resolving to a namespaced function name

Now the "normalized" representation of the function can be hashed.



### Some `codebase` selling points

#### Refactoring aliases does not effect the `codebase`


Because of the way the functions are hashed unison does not care what the programmers name functions
or even if they name them the same thing. Our function above:

```unison
sum xs = foldLeft (+) 0 xs
```

would have the name hash as this function:

```unison
tally nums = foldLeft (+) 0 nums
```

#### No conflicting dependencies

Imagine that we in a non-unison language have libraries `b` `c` and `d` like so:

```
    A
   / \
  b   c
   \ /
    d
```

Our program is `A` and it depends on `b` and `c`. They in turn depend on `d`.
This is fine as long as `b` and `c` uses the same version of `d` or even a compatible one.
However if that ceases to be the case we can no longer build our program with upgraded dependencies.

This is not a problem in unison. In unison the `d` would evaluate to a hash and `c` or `d` can either depend on
the same `d` or a different one (between the two).


#### No builds

Code that exists in the `codebase` is already ready to run. The instant you pull a library codebase
its functions can be executed. This in turn enables self deploying code also which is another (large) concept in unison
that is unfortunately as of writing this just a theory and a promise.


#### Pluggable syntax

While it does not exist currently the `codebase` can enable pluggable syntax.
As stated initially there exists tooling in unison to translate its ASTs and function hashes
to human readable text files and back again. Adding a new syntax is simply having a different translation to and from.

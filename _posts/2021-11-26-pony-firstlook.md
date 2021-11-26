---
layout: post
title:  First peek at pony language
category:  pony
---

Today I set out to explore ponylang.

As someone who enjoys writing both elixir, rust and scala 
ponylang seems to combine all of them into something else.
I am especially interested in the ponylangs take on 
safe sharing trough integrated referential capabilities.


### What is pony language

The sales pitch from ponylang.io reads

> Pony is an open-source, object-oriented, actor-model, capabilities-secure, high-performance programming language.

Pony promises to be a statically typed, compiled language, on a lightweight runtime with an integrated actor model.
Unlike other actor-model-integrated languages like erlang/elixir pony does not use a virtual machine.
Pony also attempts to iterate on safe variable sharing from what I am previously used to (immutability)
to using referential capabilities (refcaps) thus limiting the overhead.


### A first look at referential capabilities

Refcaps is at the core of pony. It is a way of describing read and write permissions given a context
trough the type system. To make sense of it I found that [this talk](https://www.youtube.com/watch?v=u1JfYa413fY) really helped.

It explains that data races can be prevented by implementing two `laws`:

1. read law: If anyone is reading noone else can write
2. write law: If anyone is reading noone else can read or write

And that in order to fufull these two laws we can think up four `solutions`:

1. only allow access from one thread at a time -- this can be implemented with a mutex
2. allow either many readers or one writer -- this can be impemented with a semaphore
3. eliminate writers -- all state is immutable and aliases never change
4. restrict the power that a alias can have so that depending on the alias the holder knows what is and 
   is not allowed with that alias. this is refcaps and pony solution to the datarace problem.


#### The sharable refcaps

The three refcaps are allowed to share aliases between actors in pony are `val` `iso` and `tag`.

- `val`|value means that the data is immutable for this alias (3. eliminating all writers)
- `iso`|isolated means that there only exists one alias and the holder is free to read and write to is (1. allow access from one thread)
- `tag`|share the identity of something (cannot read or write)

There exists 3 more refcaps but for getting started with ponylang my experience is that understanding
the sharable refcaps will take you far.

## Initial takeaway

- Knowing all refcaps in ponylang requires learning 6! types and is an initial hurdle to get over
- Refcaps seem to solve safely solve data access in a more efficient way then just making aliases read only
- Refcaps in pony gives the programmer more ability to reason about objects when reading code

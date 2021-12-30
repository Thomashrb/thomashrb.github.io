---
layout: post
title:  A second look at pony language
category:  pony
---

I last wrote about my initial look at pony. I have since played a bit more with the language
and compiled a list of interesting quirks I learned about pony.


#### Division by 0

Pony wont crash if you devide by 0.
Division by 0 is 0 :)


```pony
fun a(i: U64): U64 => 10 / i
env.out.print(a(0).string())                  //  0

```

But only if the zero is not known when compiling the function

```pony
fun b(): U64 => 10 / 0                        //  main.pony:2:24: constant divide or rem by zero
```

#### Sum types


The sum types of pony are a beauty, and honestly short of Haskell I think the most pleasant to use for me so far in any language :)
They are so powerful they even entierly replace the need for Option/Maybe types.

```pony
fun c(str: String): (U8 | None) =>
    match str
        | "one" => 1
        | "two" => 2
        else None
    end

env.out.print(Abc.c("one").string())        //  1
env.out.print(Abc.c("gazzillion").string()) //  None
```


#### Calling C

The C FFI of pony is fairly straight forward

```pony
// import library
use "lib:computersays"

// declare function from library above
// with the correct types
// pony will trust that you get this
// correct. in this case the function is
// called answer, its return type is USize
// and it takes no arguments
use @answer[USize]()

// now we can call the function
// and print its output
env.out.print((@answer().string())          //  42
```

#### Interface or Traits

Some languages support either traits/nominal subtyping or interfaces/structural subtyping.
Pony supports both.

```pony
trait Named
  fun name(): String => "Bob"

class Bob is Named

env.out.print(Bob.name())                   //  "Bob"
```

Sorry I couldnt think of a better example to illustrate the "structural" part :)
Interfaces also accept the keyword `is` which for interfaces with implemented functions
is actually how you would say that this class implements interface Named without
the "structural subtyping" part :)

```pony
interface Named
  fun name(): String => "Bob"

class Bob is Named

env.out.print(Bob.name())                   //  "Bob"
```

#### Behaviours

Actors can have behaviours `be`. These behaviours are how actors communicate.
There is no "await" function like with BEAM languages so a Main actor and
a Counter actor might communicate like so:

```pony
actor Counter
  var _count: U32

  new create() =>
    _count = 0

  // this is accessable from the outside because it is a `be`
  be increment() =>
    _count = _count + 1

  // a `be` can get an alias to a (in this case an actor with `tag` refcap) passed
  be get(main: Main) =>
    // we call Mains `be display`
    main.display(_count)

actor Main
  let _env: Env

  // Main is the entrypoint of an executable, Env is passed implicitly
  // as new create (the constructor) is called on execute
  new create(env: Env) =>
    _env = env

    var count: U32 = 10
    var counter = Counter

    for i in Range[U32](0, count) do
      counter.increment()
    end

    counter.get(this)

  be display(result: U32) =>
    _env.out.print(result.string())         //  "10"
```

---
layout: post
title:  Using a c shared library from zig
category:  zig
---

Using a c library from zig is quite easy. The zig compiler understands c code so no FFI required.

#### Create a standard zig project

```bash
$ zig init-exe
```

#### Find a library and function(s) you would like to use

```bash
$ ls /lib/
```

As an example I will use `libc` and the simplest function I can think to use is:

```bash
$ nm -C -A /lib/libc.so.6 | grep " printf"
/lib/libc.so.6:0000000000058230 T printf
```

#### Link the library in your build.zig file

```bash
$ git diff build.zig
diff --git a/build.zig b/build.zig
index 0e4f4b7..66343d9 100644
--- a/build.zig
+++ b/build.zig
@@ -12,6 +12,7 @@ pub fn build(b: *std.build.Builder) void {
     const mode = b.standardReleaseOptions();

     const exe = b.addExecutable("c_lib_test", "src/main.zig");
+    exe.linkSystemLibrary("c");
     exe.setTarget(target);
     exe.setBuildMode(mode);
     exe.install();

```


#### Import and call library in your zig code

The import is done with two builtin functions

```zig
const c = @cImport({
    @cInclude("stdio.h");
});
```

Now all functions in stdio resides under the the const `c` and can be called like so:

```zig
c.printf("Hello world of C!");
```


#### Thats it, here is the full program

```zig
const c = @cImport({
    @cInclude("stdio.h");
});

pub fn main() void {
    _ = c.printf("Hello world of C!");
}
```


```bash
$ zig build run
Hello world of C!
```

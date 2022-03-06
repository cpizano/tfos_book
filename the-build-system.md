# The Build System

The build system used in the examples is GN (which stands for Generate-Ninja). It is also the build system used in the Fuchsia master repository and Chromium.&#x20;

More precisely, GN is a meta-build system. It generates the build plan that [ninja](https://ninja-build.org) executes. GN generates the plan via `gn gen <outdir>` then you build via `ninja -C <outdir>`. In a clever move, the GN files themselves are part of the build dependency graph so normally you don't have to invoke  `gn gen` over and over; ninja will do that for you as needed.

If you came here directly from Lab 0, then you are wondering how a typical gn file looks like. Well, here is a simple one:

```c
executable(“calculator”) {
  sources = [
    “main.cc”,
    "ops.cc",
  ]

  cflags = [ “-Wall” ]
  include_dirs = [ “.” ]

  deps = [ “//some_other_gn” ]
}

```

GN Is a fully featured build system. Here are some learning resources:

* [Quick introduction slides](https://docs.google.com/presentation/d/15Zwb53JcncHfEwHpnG\_PoIbbzQ3GQi\_cpujYwbpcbZo/edit#slide=id.g1199fa62d0\_2\_6).
* [Quick start (generic)](https://gn.googlesource.com/gn/+/master/docs/quick\_start.md).
* [Frequently asked questions.](https://gn.googlesource.com/gn/+/master/docs/faq.md)

In the samples repository, GN lives in `buildtools/<host-os>/gn` for example to get information about the `.gn` (aka dotfile) use:

```bash
$ ./buildtools/linux64/gn help dotfile
```


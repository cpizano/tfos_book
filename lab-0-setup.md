# Lab 0: Setup

From here on we will explain most concepts with code. The reader is encouraged to download the source and tools so that you can play with variation and experiment with each concept. This is not required; if you want to just follow along by just reading, feel free to skip this lab. You can always come back and follow the steps below.

## Get the Code

The instructions below should work for Linux and its mostly tested on Debian. Windows is not supported at this time and Mac might actually work but has not been tested or troubleshooted enough. The lab code is [hosted in GitHub](https://github.com/cpizano/TFOS-src) and based on the [samples repo upstream](https://fuchsia.googlesource.com/samples).

The prerequisites:

* Linux host on Intel or AMD pc, preferably Debian
* About 3GB disk free.

First install the prerequisites:

```
sudo apt-get install curl unzip clang python python2
```

Then you can do a shallow clone of the repo:

```bash
$ git clone https://github.com/cpizano/TFOS-src.git --depth 1
```

Then you need to run the combined setup & test script:

```bash
$ cd samples
$ ./scripts/setup-and-test.sh
```

That script can take from 3 minutes to 30 minutes to complete since it is actually downloading and building many things. The output should look like:

```
Rebuilding symlinks in /home/cpu/src/tfos-src/buildtools/linux64 ...complete.
All build tools downloaded and extracted successfully to /home/ss/src/tfos-src/buildtools
Building for Fuchsia on arm64...
Done. Made 779 targets from 188 files in 57ms
ninja: Entering directory `/home/ss/src/tfos-src/out/arm64'
[96/96] STAMP obj/default.stamp
Building for Fuchsia on x64...
Done. Made 779 targets from 188 files in 56ms
ninja: Entering directory `/home/ss/src/tfos-src/out/x64'
[1/1] Regenerating ninja files
[94/94] STAMP obj/default.stamp
Building for linux on x64...
Done. Made 15 targets from 11 files in 6ms
ninja: Entering directory `/home/ss/src/tfos-src/out/linux'
[1/4] SOLINK ./libhello_shared.so

.... many more lines here .....
...
....

Running main() from ../../third_party/googletest/src/googletest/src/gtest_main.cc
[==========] Running 6 tests from 1 test suite.
[----------] Global test environment set-up.
[----------] 6 tests from Rot13Test
[ RUN      ] Rot13Test.TrueTest
[       OK ] Rot13Test.TrueTest (0 ms)
[ RUN      ] Rot13Test.Encrypt_EdgeCases
[       OK ] Rot13Test.Encrypt_EdgeCases (0 ms)
[ RUN      ] Rot13Test.Encrypt_HelloWorld
[       OK ] Rot13Test.Encrypt_HelloWorld (0 ms)
[ RUN      ] Rot13Test.Encrypt_Empty
[       OK ] Rot13Test.Encrypt_Empty (0 ms)
[ RUN      ] Rot13Test.Checksum_Empty
[       OK ] Rot13Test.Checksum_Empty (0 ms)
[ RUN      ] Rot13Test.Checksum_HelloWorld
[       OK ] Rot13Test.Checksum_HelloWorld (0 ms)
[----------] 6 tests from Rot13Test (0 ms total)

[----------] Global test environment tear-down
[==========] 6 tests from 1 test suite ran. (0 ms total)
[  PASSED  ] 6 tests.

Success!

```

{% hint style="warning" %}
The commands above built artifacts for x64 (Intel) and ARM64, but **elsewhere in the book we only use the x64 version**. Testing and experimenting with ARM64 is doable but left as an exercise for the reader.
{% endhint %}

## Building the code

The lab code lives in `src\`along with some examples. The build system is `gn` and `ninja` which you don't need to master right away, but if you are curious you can visit this chapter: [The build system](the-build-system.md).

All the code labs are linked to the root `BUILD.gn` so whenever you make a change to one of the existing files, you can simply build again with:

```
$ ./buildtools/linux64/ninja -C out/x64
```

The ultimate result is a set of .far ([fuchsia archive](glossary.md#far)) files, which live in the out directory, for example`out/x64/lab_001.far` which will run in the [FEMU](glossary.md#femu) virtual machine.

You can think of a .far archive as the transitive set of the program you built plus all its dependencies in a self contained bundle.








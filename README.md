# Welcome

## The Fuchsia Operating System (TFOS)

Welcome to TFOS! or more precisely the TFOS interactive book. This site objective is to teach the key concepts of Fuchsia. The approach of **TFOS** is to explain how Fuchsia works by by comparing and contrasting with existing systems and to do so _**using code**_ whenever practical.

## Fuchsia in a Nutshell

[Fuchsia](https://fuchsia.dev) is a modern, open-source, general purpose [operating system ](https://en.wikipedia.org/wiki/Operating\_system)made by Google and external contributors. Fuchsia is developed in the open and anybody can contribute it. Here are some important links:

* [main git repository](https://fuchsia.googlesource.com/fuchsia/)
* [official documentation](https://fuchsia.dev)
* [getting started](https://fuchsia.dev/fuchsia-src/get-started)

Fuchsia is neither inspired by [UNIX](https://en.wikipedia.org/wiki/Unix) concepts (like Linux or BSD) nor by the Windows or Mac operating systems. As such, it's challenging to explain to developers whose only experience is with those systems.

## Intended Audience

This interactive book is geared towards system-level developers; we only assume basic knowledge of operating systems. Terms such as drivers, file systems, kernel-mode and user-mode, should be familiar to the reader. However, we don't assume the reader is an expert in operating systems. Whenever an advanced concept is used it will be explained beforehand or a link to a relevant source will be given.

## Use of C and C++

We also assume a basic working knowledge of C and C++. In particular C++11 and C11. Whenever an advanced C++ construct is used, it will be explained beforehand.  The main use of C++ is to remove distracting boilerplate code while at the same time not training new developers to forgo type safety, error checking or proper cleanup.

## Editorializing Dangers

As the new operating system on the block, it is very helpful to note how each facility or feature resembles of differs from similar facilities on Mac, Linux or Windows. Often the differences are informed on our experience with how the existing operating systems "got it wrong". However, the intention is not one of bashing existing operating systems; all three mainstream operating systems got an incredible amount of things right and their designers have shown an astounding foresight. That being said, all three where designed 25 years (or more) ago and we, the Fuchsia authors believe that enough things have changed that a fresh take is worthwhile. It's our hope that at the end of TFOS you will agree with us.




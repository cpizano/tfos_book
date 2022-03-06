# Lab 2: Serving Packages

The final step before we can get to learning fuchsia concepts by writing code is to set the package manager, affectionately named `pm` in the repository. The package manager is how we push new code to a fuchsia device and how a fuchsia device discovers what can be run. The procedure might seem complex at first, but using it saves time in the long run.

We'll be using 3 different terminals at the same time to run 3 different pieces of software:

* A Fuchsia instance in FEMU, as before
* An ssh client connected to the FEMU instance
* The package manager

A diagram is helpful to visualize the entire system

(do diagram)


# Lab 3: C friendly but not POSIX

Thanks for slugging out the layers section. That was quite a long read! Now its time to get hands on.

## Vanilla C code Just Works

First off, good news: a lot of what you already know can be used right away in a Fuchsia environment. For example the C code as show below just works:

{% code title="lab_001a.c" %}
```c
#include <stdio.h>

int main() {
    int number;
    printf("enter an integer: ");
    if (scanf("%d", &number) != 1)
        return 1;  
    printf("twice that: %d", number * 2);
    return 0;
}
```
{% endcode %}

In the right environment this program does exactly what you expect.&#x20;

A huge swath of single-threaded, pure C code can be ported as-is. This is how a good chunk of [sbase unix tools](https://fuchsia.googlesource.com/third\_party/sbase/+/master/README) got ported to Fuchsia.

Same can be said for C++, for example:

{% code title="lab_001b.cc" %}
```c
#include <iostream>
#include <fstream>
int main(int argc, char * argv[])
{
    std::fstream myfile("data.txt", std::ios_base::in);

    float a;
    while (myfile >> a)
    {
        std::cout << a << ",";
    }
    return 0;
}
```
{% endcode %}

## Fuchsia's standard library (libc)

Fuchsia's libc implements the C11 standard. In particular this includes the threading-related interfaces such as threads (`thrd_t`) and mutexes (`mtx_t`). A small handful of extensions are also in this portion of the system to bridge the C11 structures.

## POSIX support

Fuchsia has a partial implementation of POSIX.  For example, it has basic support for pthreads. The most significant omissions are:

* No support `fork()` or `exec` because  they are not compatible with the architecture or security model of Fuchsia.  Process launching will be discussed later on.
* Does not support UNIX style signals. There is no `SIGTERM` and libc functions will not `EINTR`, and it is not necessary for Fuchsia-only code to consider that case. However, it is perfectly safe to do so. Code written for both Posix and Fuchsia may still have EINTR-handling loops.

Fuchsia does implement:

* File I/O
* BSD sockets

However these facilities are predicated in that the relevant services are reachable by the program. This topic is complex and will be addressed later.

(do a pthreads example)

## Basic programming model

(talk about threads)

## Much more than meets the eye

As you imagine **it would not be worthwhile to invent a new operating system from scratch whose strength or purpose is running this kind of programs**. Fuchsia's true capabilities are elsewhere. We need to go deeper and study the system mechanisms that underpin this and many more advanced programs that fully take advantage of what fuchsia is capable. Lets start that journey in the next lab.

(explain about processes and threads)

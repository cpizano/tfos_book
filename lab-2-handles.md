# Lab 4: Handles and Objects

The first concept to master is that of the "**handle**".

## Handles are sessions to Kernel Objects

The Zircon programming interface is based on the concept of handles. You can think of a handle as a logical session with a kernel object. A kernel object in this context is similar to a programming language object: the developer should visualize it as separate entity inside kernel that does one particular type of operation, has mutable state and has a definite lifetime.

The handle is not the kernel object, but a way to talk to such object.

(some picture here)

Lets go ahead with our first interesting program. Lets create a kernel object and its handle:

{% code title="lab_002a.c" %}
```cpp
#include <zircon/syscalls.h>
#include <zircon/types.h>

int main() {
    zx_handle_t event_h;
    zx_status_t st = zx_event_create(0u, &event_h);
    if (st != ZX_OK)
        return 1;

    st = zx_handle_close(event_h);
    if (st != ZX_OK)
        return 2;

    // Upon success the program returns 0.
    return 0;
}
```
{% endcode %}

What you see above is the entire program for creating a event kernel object (line #6), connecting it to the `event_h` handle, and then if successful, closing it. The event object was created during `zx_event_create()` and shortly after destroyed during `zx_handle_close()`.

The C types `zx_handle_t` and `zx_status_t` are just 32 bit unsigned integers which are defined in `zircon/types.h`. Therefore, a handle is just a number but is the unique identifier of the connection between a particular kernel object and "this" process. Other processes might also get a handle with the same numeric value but it won't be connected to the same kernel object.

{% hint style="info" %}
Kernel events are used to coordinate two or more threads which might or might not belong to the same process. So far we only have one thread so the program can't do anything useful yet.

You can read more about events in the fuchsia documentation [here](https://fuchsia.dev/fuchsia-src/reference/kernel\_objects/event).
{% endhint %}

A few more things are worth mentioning:&#x20;

* Handle values are always chosen by the kernel.
* A valid handle will never be equal to the constant `ZX_HANDLE_INVALID`.&#x20;
* Handles can have any other numeric value but the least two significant bits are always 1.
* Handle values are always chosen by the kernel
* When a process terminates or is killed kernel automatically closes all outstanding handles.
* No matter how simple your program is, two sequential runs of the program are highly unlikely to receive the same handle values.

The two most interesting lines of the program are the two [syscalls](https://en.wikipedia.org/wiki/System\_call) :&#x20;

```cpp
zx_event_create(0u, &event_h)
zx_handle_close(event_h)
```

Which are defined in `zircon/syscalls.h` and implemented in a virtual dynamic shared library ([vDSO](glossary.md#vdso)) named `libzircon.so`.  These syscalls define the API exposed by the kernel which is effectively the lowest level interface in the system.

Most syscalls return an status code because most syscalls can fail. It is almost always an error to not check for the returned status code. We don't want to train developers to think error checking is optional so the code in this document does error checking but for brevity's sake not always shows the most desirable error handling.

{% hint style="danger" %}
Unlike Linux or Windows, in order to make a system call, the process has to make the call via the system provided vDSO: any other method including `asm SYSENTER` will cause the call to fail and its considered adversarial behavior of the application.
{% endhint %}

## Handle Duplication

Anyhow, we didn't do anything interesting yet, but we are about to remedy that:

{% code title="lab_002b.c" %}
```cpp
#include <zircon/syscalls.h>
#include <zircon/types.h>

int main() {
    zx_handle_t event_o, event_d;
    zx_status_t st = zx_event_create(0u, &event_o);
    if (st != ZX_OK)
        return 1;

    st = zx_handle_duplicate(event_o, ZX_RIGHT_SAME_RIGHTS, &event_d);
    if (st != ZX_OK)
        return 2;

    st = zx_handle_close(event_o);
    if (st != ZX_OK)
        return 3;

    st = zx_object_signal(event_d, 0u, ZX_EVENT_SIGNALED);
    if (st != ZX_OK)
        return 4;

    zx_handle_close(event_d);
    return 0;
}
```
{% endcode %}

Above, we create the event object and then duplicate its handle. When we close the first handle the event object (line #14) does not get destroyed because there is a second, outstanding session/handle represented by `event_d`. Then we issue the first real operation against the event (line #18) which is to signal it.

Note that when duplicating events you can specify what set of "_**rights**_" the new handle will get. A "_right_" in this context defines (and restricts) to the set of valid operations for a given kernel object. The two main operations on an event are waiting on it and signaling it; therefore you can create a handle that only allows waiting and one that only allows signaling both connected to the same event object.

{% hint style="info" %}
The rights associated with handles is one that makes Fuchsia handles similar to Windows handles but different from UNIX file descriptors in which `dup()`'ed file descriptors can only have the same permissions as the source. Although in the code above we simply ask for the same rights as the source handle. More on that later.
{% endhint %}

(explain a bit more about rights, maybe remove signal so the run can fail)


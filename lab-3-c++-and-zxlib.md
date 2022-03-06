# Lab 5: zxlib and Threads

When the last program from [lab 2 ](lab-2-handles.md)exits, the process will get terminated which will implicitly close all outstanding handles. This makes the example technically correct but very brittle against refactoring. The better code (not shown) should explicitly close the **event\_o** before `return 2;`, but as you can see even the incomplete the error handling is starting to dominate the examples. We'll fix that right now using C++ wrappers from the **zx** namespace. Here is the same code:

{% code title="lab_003a.cc" %}
```cpp
#include <zx/event.h>

int main() {
    zx::event event;
    zx::event event_d;
    zx_status_t st = zx::event::create(0u, &event);
    if (st != ZX_OK)
        return 1;

    st = event.duplicate(ZX_RIGHT_SAME_RIGHTS, &event_d);
    if (st != ZX_OK)
        return 2;

    st = event.close();
    if (st != ZX_OK)
        return 3;

    st = event_d.signal(0u, ZX_EVENT_SIGNALED);
    if (st != ZX_OK)
        return 4;

    return 0;
}
```
{% endcode %}

The above version fixes the two outstanding problems and it's shorter. Some people might even argue that is easier to read. What we really gain is automatically calling `zx_handle_close()` at the right time, in the [spirit of RAII](http://en.cppreference.com/w/cpp/language/raii). Special attention has been paid so that the C++ wrappers don't do any extra work under the covers and use the C++ type system mostly to avoid common errors.

(introduce here the zircon thread here? or maybe add a lab before this one in C)



The zxlib models a simple, two-level hierarchy of classes: `zx::event` and `zx::thread` both inherit from `zx::object` which has methods corresponding to syscalls that apply to many kernel objects. Here is the cheat sheet on the most common methods:

* `zx::object::get()`: get the handle value but keep ownership
* `zx::object::release()`: get the handle value and release ownership
* `zx::object::signal()`: equivalent to `zx_object_signal()`
* `zx::object::wait()`: equivalent to `zx_object_wait_one()`


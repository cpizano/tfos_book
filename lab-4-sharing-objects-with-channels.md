# Lab 6: Sharing objects with Channels

Looking back to lab 3, we created and event and then signaled it. What does that accomplish? so far nothing, but that by itself is noteworthy: In Zircon, most kernel objects a process creates **are never implicitly shared with anybody else**. There is no way another process can somehow come up with a name that would allow it to open this event and mutate or inspect it.

{% hint style="info" %}
If you are familiar with Windows, the lab 2 code would look somewhat familiar, but the key detail is that on Windows you can do:

```
// Windows code (won't compile)
    
// first process:
HANDLE ev = CreateEvent(NULL, 0, 0, "my_event_1");
    
// Second process:
HANDLE ev = OpenEvent(EVENT_MODIFY_STATE, true, "my_event_1");
```

Which allows other processes in the system to share the event by name. At first glance this seems like a desirable feature but conjoining access-by-name and access-by-handle creates difficult and subtle problems which can become significant security headaches.

(expand on this)
{% endhint %}

Now, without being able to name kernel objects how do we share them? for that, there is a specialized kernel object, named the "_channel_" whose job is to transport handles between processes. The channel, similar to pipes in Windows or Linux; it has two sides which can be connected to different processes and using each side the processes can send data to the other, but in Fuchsia the channel can send handles as well.

This is illustrated below:

```cpp
#include <zx/event.h>

int main() {
    zx_handle_t ch0 = GetChannelEndpointSomehow();

    zx::event event;
    zx_status_t st = zx::event::create(0u, &event);
    if (st != ZX_OK)
        return 1;

    zx::event event_d;
    st = event.duplicate(ZX_RIGHT_SAME_RIGHTS, &event_d);
    if (st != ZX_OK)
        return 2;

    zx_handle_t to_send = event_d.release();
    st = zx_channel_write(ch0, 0u, nullptr, 0u, &to_send, 1u);
    if (st != ZX_OK) {
        zx_handle_close(to_send);
        return 3;
    }

    st = zx_object_wait_one(
        event.get(), ZX_EVENT_SIGNALED, ZX_TIME_INFINITE, nullptr);
    return (st == ZX_OK) ? 0 : 4;
}
```

What this code is doing is creating an event and a second handle to it, then sending that second handle (via `zx_channel_write()`) to somebody else who is holding the other side of that channel. Then the code waits (forever) for the event to be signaled by whoever happens to receive the handle. If waiting forever is not desirable the third argument of `zx_object_wait_one()` can specify a timeout deadline. More on that later.

By the way, sending the handle means that if the call is successful, the handle is no longer valid in this process. This is an atomic operation; there is no moment in time when the handle is valid for both the sender and the receiver. This is why we call `release()` on the event that we are sending. If we don't, the destructor will call `zx_handle_close()` on an invalid handle which is a bug. More on that later.

We should also mention that channels can also be used to send plain bytes, or both bytes and handles. For completeness this is a snippet of of sending a string and no handles:

```cpp
    char msg[] = "hello Zircon!";
    st = zx_channel_write(ch0, 0u, msg, countof(msg), nullptr, 0u);
```

Now let's go back to look at the event receiving side:

```cpp
int main() {
    zx_handle_t ch1 = GetChannelEndpointSomehow();
    // Acquire the "done" event.
    zx_handle_t event;
    zx_status_t st = zx_channel_read(ch1, 0u, nullptr, &event, 0u, 1u);
    if (st != ZX_OK)
        return 1;
    // Do our lengthy computation.
    DoSomeWork();
    // Tell the other side we are done.
    st = zx_object_signal(event, 0u, ZX_EVENT_SIGNALED);
    if (st != ZX_OK)
        return 2;

    zx_close_handle(event);
    zx_close_handle(ch1);
    return (st != ZX_OK) ? 2 : 0;
}
```

So taken together we have one program that waits until the other one does some work and signals the event. It's worth reiterating that channels _move_ handles but they do so in two stages: first the write moves the handle into the channel and then the read moves the handle from the channel into the process.&#x20;

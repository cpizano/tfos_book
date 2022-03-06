# L0: Zircon

Zircon is the set of open-source components that sit most closely to hardware. It includes the kernel but also includes core services, drivers and boot loaders, all which are necessary for any significant system operation.

The Zircon kernel is a single raw binary: `zircon.bin` that at this time is about 500 to 800 KiB depending on build configuration, processor architecture and if low-level debug commands are enabled. Unlike Windows, Mac or Linux, the core services are user-mode programs and no other code besides `zircon.bin` is run in kernel mode. That fact along with the relatively small kernel public interface is why sometimes Zircon kernel is said to be a [microkernel](https://en.wikipedia.org/wiki/Microkernel) design.

{% hint style="warning" %}
Note: for some people, the microkernel denomination implies a very minimal set of orthogonal system calls, for example the [L4 microkernel](https://en.wikipedia.org/wiki/L4\_microkernel\_family) can have as few as 7 depending how you count. This minimalism is not a design goal for Zircon. Our aim is to create a pragmatic, [message-passing](https://en.wikipedia.org/wiki/Message\_passing) kernel. Any operating system that aims for widespread use needs to balance purity of design versus performance and ergonomics.&#x20;
{% endhint %}

Zircon can be built for ARM64 and for x86-64. There is no support for 32-bit-only systems or systems without virtual memory management hardware. For ARM we support the ARMv8-A model and for x86 we support both Intel and AMD 64 bit variants.

Zircon is designed so that in the supported hardware and emulators it can fully boot and present to the user a basic text terminal connected to a a [DASH-based](https://en.wikipedia.org/wiki/Almquist\_shell) command interpreter. The method varies:

* If the hardware supports a graphics [frame buffer](https://en.wikipedia.org/wiki/Framebuffer). It will present a simple tabbed text terminal interface which is serviced by a simple command interpreter.
* Otherwise, the system presents the a single terminal session over the first serial port.

![Zircon built-in terminal in QEMU x64](../.gitbook/assets/core\_x64\_qemu.png)

But the most important aspect of the Zircon kernel is what it does not have:

* Drivers, except debug serial
* Filesystem interfaces or implementations
* Networking interfaces or implementations
* Graphic interfaces or implementations
* Identity or user management interfaces or implementations
* Module or program loading facilities
* Input

Most of these facilities do exist, but they are run as services, not only isolated from corrupting or interfering with kernel but also isolated from each other.


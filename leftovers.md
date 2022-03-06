---
description: (not part of the book atm)
---

# Leftovers

Zircon includes an ELF loader service and requires all Zircon executables be position-independent ELF binaries. At this layer, only a restricted set of C and C++ is supported. Support for Zircon has been up streamed to both GCC and LLVM tool chains so there is no need to procure Zircon-specific tools for creating regular programs. The layers above Zircon introduce support for executable formats such as Dart, Java or HTML/JS.


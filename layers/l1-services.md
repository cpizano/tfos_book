# L1: Services

The next layer up from Zircon is the core services that in other systems will are often found implemented inside kernel. In particular

* Networking (TCP) stack
* Graphics and input stack
* Software updater
* Media services
* Bluetooth services
* File system servers

Nothing at this layer is compulsory. Every single piece uses the public APIs of Zircon and can be replaced to meet specific needs. For example, in a system where there is very limited storage and only Bluetooth LE hardware there would be no need to include Media, Networking or Graphics.

{% hint style="info" %}
The other reason to have this layer is trust: Zircon has very few dependencies and it is expected to be closely controlled by the "first party", which is typically the organization making the actual device and which is in charge of ensuring the integrity of the core system including using secure (or verified) boot when reasonable. The services layer has much larger dependency footprint and depending on specific circumstances a mix of first party, OEM and third-party software
{% endhint %}

# Lab 1: Running Fuchsia

## First Boot

Running the code in this book requires [FEMU](glossary.md#femu) which is based on [AEMU](https://android.googlesource.com/platform/external/qemu/+/emu-master-dev#welcome-to-the-android-emulator). The emulator it sensitive to the kind of computer and specific operating system you have. The first thing we want to do is make sure FEMU is running properly.

To do so issue at the root of the repository:

```bash
$ ./third_party/fuchsia-sdk/bin/femu.sh
```

This might trigger a download of the emulator from [CIPD](glossary.md#cipd). Afterwards you should see in the terminal:

```bash
Checking for system images and packages
Skipping download, image exists.
Skipping download, packages tarball exists
Using existing archive in /home/cpu/.fuchsia/emulator/aemu-linux-amd64-git_revision_ae3561ab0e50....
Creating disk image...done
+ /home/cpu/.fuchsia/emulator/aemu-linux-amd64-git_revision_ae3561ab0e503b2d91c0622e0a0b61757ce34c1e/emulator -feature ...
handleCpuAccelerationForMinConfig: Checking CPU acceleration capability on host...
handleCpuAccelerationForMinConfig: Host can use CPU acceleration
handleCpuAccelerationForMinConfig: Host CPU acceleration capability status string: [KVM (version 12) is installed and usable.]
handleCpuAccelerationForMinConfig: Selecting KVM for CPU acceleration
pc_memory_init: above 4g size: 180000000
[00000.000] 00000:00000> printing enabled
[00000.000] 00000:00000> zbi: @ 0xffffff8000bdf000 (11379696 bytes)
[00000.000] 00000:00000> UART: enabled with FIFO depth 16
[00000.000] 00000:00000> PMM: boot reserve add [0x100000, 0x3c2fff]
[00000.000] 00000:00000> PMM: boot reserve add [0xbdf000, 0x16b9fff]
[00000.000] 00000:00000> PMM: adding arena 0xffffffff003be020 name 'memory' base 0x100000 size 0x7fede000
[00000.000] 00000:00000> PMM: adding arena 0xffffffff003be020 name 'memory' base 0x100000000 size 0x180000000
[00000.000] 00000:00000> PMM: boot reserve marking WIRED [0x100000, 0x3c2fff]
[00000.000] 00000:00000> PMM: boot reserve marking WIRED [0xbdf000, 0x16b9fff]
[00000.000] 00000:00000> INIT: cpu 0, calling hook 0xffffffff0013a440 (global_prng_seed) at level 0x20001, flags 0x1
[00000.000] 00000:00000> INIT: cpu 0, calling hook 0xffffffff002a5990 (intel_rng_init) at level 0x20001, flags 0x1
[00000.000] 00000:00000> 
[00000.000] 00000:00000> welcome to Zircon
[00000.000] 00000:00000> 

.... many more lines here ...

[00000.233] 00000:00000> INIT: cpu 0, calling hook 0xffffffff0013a870 (global_prng_reseed) at level 0xa0000, flags 0x1
[00000.233] 00000:00000> INIT: cpu 0, calling hook 0xffffffff0017e950 (libobject) at level 0xa0000, flags 0x1
[00000.233] 00000:00000> INIT: cpu 0, calling hook 0xffffffff0023e770 (pmm) at level 0xa0000, flags 0x1
[00000.233] 00000:00000> INIT: cpu 0, calling hook 0xffffffff00286d30 (dpc) at level 0xa0000, flags 0x1

... many more lines here ....

[00000.380] 01028:01047> userboot: finished!
[00000.380] 01058:01061> bootsvc: Starting...

... more lines here  ...

[00000.388] 01147:01165> [component_manager] INFO: Component manager is starting up...

... more lines here ...
```

The important bits are:

* &#x20;line 8:  `Host can use CPU acceleration` if that is not present Fuchsia is going to run painfully slow.
* line 24: `welcome to zircon` this means kernel has started.
* line 37: `bootsvc: Starting` kernel is done and user-mode has begun.

The output you see above is called the **kernel log**. There are other log outputs which output different (and sometimes similar) textual logs. The kernel log uses `[`  to start each line, the other logs use different characters.

{% hint style="info" %}
**Decoding each line**: each line of the kernel log contains:

`[seconds]` `process-id:thread-id` >  {actual log line}

For example:  `[00000.380] 01028:01047> userboot: finished!` happened at .38 seconds into the boot and was issued by process 1028 and thread 1047. Process 0 means the output was done by kernel itself.
{% endhint %}

Also, after some 10 seconds you should also see the FEMU window. This represents the screen output of Fuchsia to a virtual monitor.&#x20;

![Main FEMU window](.gitbook/assets/aemu\_blank\_main.png)

We won't be using graphical output for the first 10 labs, so feel free to ignore it. There is a toolbar next to the main FEMU window. **Closing the toolbar actually exits the FEMU instance,** so unless you want o finish the session don't do it.

Anyhow, congratulations if you got to this point! There are several possible ways things might have gone wrong up to this point and if you got into one of them let us know.

{% hint style="info" %}
The system image that we are running does not have a GUI or desktop environment, so don't be alarmed by the lack of screen output. Instructions will be given later on to build and run such system images.
{% endhint %}

Now go back to the terminal and press enter twice. You should see something like this:

```bash
[00014.900] 02355:02357> paver:[Bind] Failed to get ABR client: ZX_ERR_NOT_SUPPORTED
[00015.156] 16727:17127> [00015.156455][16727][17127][driver_host:composite-device ...
 
$ 
$ 
```

The `$`  means that Fuchsia is ready to accept serial port commands. In other words, FEMU is re-routing what you type in the terminal to a virtual serial port attached to a Fuchsia terminal shell. This is a highly privileged shell, think of it as running **all commands here as root**.

To kick the tires, lets execute a few commands:  `pwd`, `cal` :

```bash
$ pwd
/
```

```bash
$ cal
     October 2020      
Su Mo Tu We Th Fr Sa   
             1  2  3   
 4  5  6  7  8  9 10   
11 12 13 14 15 16 17   
18 19 20 21 22 23 24   
25 26 27 28 29 30 31   
```

There are many more commands readily available, not all of them are meant to run in the serial shell environment. One that that is very useful is `ps` :

```bash
$ ps
TASK                   PSS PRIVATE  SHARED   STATE NAME
j: 1027             192.9M  192.9M                 root
  p: 1058           540.4k    540k     36k         bin/bootsvc
  p: 1147          2896.4k   2896k     36k         bin/component_manager
  p: 2350           228.4k    228k     36k         sh:console
  j: 1365           260.4k    260k                 
    p: 1402         260.4k    260k     36k         pwrbtn-monitor.cm
  j: 1446           304.4k    304k                 
    p: 1471         304.4k    304k     36k         console-launcher.cm
  j: 1526           105.6M  105.6M                 
    p: 1550         940.4k    940k     36k         fshost.cm
    p: 5505          84.8M   84.8M     36k         /boot/bin/blobfs
    p: 8970        8800.4k   8800k     36k         /boot/bin/minfs
    p: 9230          11.3M   11.3M     36k         pkgfs
  j: 1722          1000.4k   1000k                 
    p: 1739        1000.4k   1000k     36k         driver_manager.cm
  j: 1919           368.4k    368k                 
    p: 1930         368.4k    368k     36k         console.cm
  j: 2113            25.6M   25.6M                 zircon-drivers
    p: 3217        1864.4k   1864k     36k         driver_host:sys
    p: 3277         440.4k    440k     36k         driver_host:test
    p: 3348         424.4k    424k     36k         driver_host:root
    p: 3448         408.4k    408k     36k         driver_host:misc
    p: 4113         960.4k    960k     36k         driver_host:pci#1:1af4:1052
    p: 4210        1284.4k   1284k     36k         driver_host:pci#2:1af4:1052
    p: 4307          16.9M   16.9M     36k         driver_host:pci#3:1af4:1001

... many more lines ...
```

Which does what you expect.... kind of. One very important thing to keep in mind, the serial (zircon) shell environment **resembles Linux, but it is nowhere close to it**. You can begin to tell by carefully reading the output above.

The next steps require us to set networking which requires a restart of the emulator. So before that lets learn the nice way to shutdown fuchsia from the serial shell, via `dm poweroff`:

```bash
$ dm poweroff
[01270.935] 28446:28448> [shutdown-shim]: checking power_manager liveness
[01270.937] 28446:28448> [shutdown-shim]: trying to forward command
[01270.938] 16355:16357> [power_manager] INFO: System shutdown (PowerOff)
[01270.941] 01739:01741> [01270.940612][1739][1741][driver_manager.cm] INFO: Setting shutdown system state to 5
[01270.978] 02182:02646> crashsvc: Lost connection to fuchsia.exception.Handler: ZX_ERR_PEER_CLOSED (-24)
[01271.138] 16763:17107> [01271.137751][16763][17107][driver_host:composite-device, driver, display_controller] ...
[01271.140] 16763:16765> [01271.140530][16763][16765][driver_host:composite-device, driver, display_controller] ..
[01271.186] 01550:02057> fshost: received shutdown command over lifecycle interface
[01271.187] 01550:02057> fshost: exit signal detected
[01271.187] 09230:10937> pkgsvr: serving terminated: unknown ordinal
[01271.188] 08970:08972> minfs: Shutting down
[01271.203] 08970:08972> minfs: Unmounted
[01271.204] 05505:05507> blobfs: Shutting down
+ rm -Rf /tmp/tmp.Y7YS8MSkO6
+ [[ -n '' ]]
+ [[ ehxB == *i* ]]
```

`dm` is a command line interface to the [device manager,](glossary.md#device-manager) which controls drivers and also shutdown and we just instructed it to do so. You can explore the device manager commands via `dm --help`.

## Set up and Test Networking

First we need to set the firewall rules. The femu wrapper can tell us the right command via the `--help`

```bash
$ ./third_party/fuchsia-sdk/bin/femu.sh --help
```

Which outputs a wall of text. What we want is `--setup-ufw`:

```bash
$ ./third_party/fuchsia-sdk/bin/femu.sh --setup-ufw

+ is-mac
++ uname -s
+ [[ Linux == \D\a\r\w\i\n ]]
+ return 1
+ sudo ufw allow proto udp from fe80::/10 to any port 33331:33340 comment 'Fuchsia Netboot Protocol'
[sudo] password for cpu: 
Skipping adding existing rule (v6)
+ sudo ufw allow proto tcp from fe80::/10 to any port 8083 comment 'Fuchsia Package Server'
Skipping adding existing rule (v6)
+ sudo ufw allow proto udp from fe80::/10 port 33340 comment 'Fuchsia Netboot TFTP Source Port'
Skipping adding existing rule (v6)
+ set +xv
```

Typically this only needs to be done once per session. With that done then simply start the emulator with `-N`:

```bash
$ ./third_party/fuchsia-sdk/bin/femu.sh -N

```

The first time you run the command above it might ask you to set [tun/tap](https://en.wikipedia.org/wiki/TUN/TAP) for QEMU. Follow the instructions in the terminal.

After all the output in the Fuchsia serial terminal you can verify that we have network via `ifconfig`:

{% code title="(fuchsia console)" %}
```bash
$ ifconfig
lo	HWaddr  Id:1
	inet addr:127.0.0.1  Bcast:127.255.255.255  Mask:255.0.0.0
	inet6 addr: ::1/128 Scope:Link
	metric:100
	Up
ethp0003	HWaddr 52:54:00:63:5e:7a Id:2
	inet addr:0.0.0.0  Bcast:255.255.255.255  Mask:0.0.0.0
	inet6 addr: fe80::9177:6e64:56ad:2e15/64 Scope:Link
	inet6 addr: fe80::5054:ff:fe63:5e7a/128 Scope:Link
	metric:100
	Unknown
```
{% endcode %}

In particular there is `ethp0003` with two link-local ipv6 addresses (they start with `fe80`). Either can be pinged from host with the right incantation. From a different terminal window type:

```bash
$ ping6 fe80::5054:ff:fe63:5e7a%qemu
PING fe80::5054:ff:fe63:5e7a%qemu(fe80::5054:ff:fe63:5e7a%qemu) 56 data bytes
64 bytes from fe80::5054:ff:fe63:5e7a%qemu: icmp_seq=1 ttl=64 time=1.48 ms
64 bytes from fe80::5054:ff:fe63:5e7a%qemu: icmp_seq=2 ttl=64 time=0.991 ms
... more lines follow ...
```

Notice we need to append `%qemu` which identifies the interface used by QEMU. This is necessary when using link-local addresses.

If you got to this point then you are ready for the main event. Connecting to the fuchsia instance via ssh. Open a new host terminal and issue `fssh`:

```bash
$ ./third_party/fuchsia-sdk/bin/fssh.sh
Warning: Permanently added 'fe80::5054:ff:fe63:5e7a%qemu' (ED25519) to the list of known hosts.
$ 
```

If that succeeds the command prompt `$` here executes commands in the fuchsia instance. Lets verify this issuing `uname`:

```bash
$ uname -a
Zircon step-atom-yard-juicy prerelease git-309f241d0302..2678b559-dirty x86_64
```

The string `step-atom-yard-juicy` is equivalent to the Ethernet address of the QEMU instance and another way to uniquely identify this Fuchsia instance in the network. You can Leave this ssh instance open for the next lab.


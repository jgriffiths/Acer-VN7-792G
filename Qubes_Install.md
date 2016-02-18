# Installing Qubes on the Acer Aspire V

## Overview

This guide covers installing [Qubes](https://www.qubes-os.org/) on the
Acer Aspire V series of laptops. Some of these instructions may be
specific to the V 17 Nitro Black Edition VN7-792G.

You should read this entire guide before attempting any install.


## Note

This procedure is documented for informational purposes only. If you choose
to follow the directions below, you do so *AT YOUR OWN RISK* and in the
full knowledge that doing so could *RENDER YOUR DEVICE UNUSABLE*.

Further, these instructions *COMPLETELY ERASE WINDOWS AND THE RECOVERY
PARTITION* from the device, since I have no interest in running any Windows
version. This means that should something go wrong, you will have *NO WAY
TO RESTORE* the old functionality.


## Preparation

Read the preparation instructions for installing Linux [here](./Linux_Install.md).
You may even wish to install Linux first to get a feel for the process.


## Installation

Insert your bootable Qubes install USB and boot. I used 3.1 RC2 which is
available from [here](https://www.qubes-os.org/downloads/).

When the installer screen appears press `TAB` to edit the boot line which
starts the install process. The boot command line will appear for
editing.

The boot options for the kernel are in the middle of the command line,
surrounded by `---`. You need to add:

```
modprobe.blacklist=nouveau i915.preliminary_hw_support=1
```

Then press `ENTER` and the installation will start. These commands disable
the Nvidia graphics card and enable the onboard Intel graphics card
respectively.

Install to the hard drive(s) as you see fit.

When the install is finished and the machine re-boots, press `e` at the
GRUB menu to edit the kernel command line for booting. A screen of boot
instructions will appear. The kernel options are on the line starting
with `module` after the line starting 'echo Loading Linux '. Add:

```
modprobe.blacklist=nouveau i915.preliminary_hw_support=1
```

and press CTRL-x to boot. Once the system boots, finish the installer
questions and click next to let it run.

Once the setup is complete the system will bring up X to log in which
triggers a bug in the Intel drm manager and causes the screen to go blank
and/or screwy.  Leave it for a few minutes to let anything running finish
and then power down and turn back on.

You will need to press `e` and re-edit the kernel command line as above.
Press CTRL-x after editing. The system should boot to the Qubes desktop.

From here you have a Linux install in dom 0 and so the commands listed
in the [Linux Install](./Linux_Install.md) post install section should
be run e.g. updating GRUB. Note the kernel command line parameters for
Qubes are "modprobe.blacklist=nouveau i915.preliminary_hw_support=1"
rather than those listed in the Linux install.

You should update dom0 and the template VMs immediately as they contain
fixes for this hardware (e.g. the new firmware for the wireless card).

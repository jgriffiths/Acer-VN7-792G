# fedora-VN7-792G

Setup/Run Fedora Linux on Acer Aspire v17 Nitro VN7-792G laptops.

## Note

This procedure is documented for informational purposes only. If you choose
to follow the directions below, you do so *AT YOUR OWN RISK* and in the
full knowledge that doing so could *RENDER YOUR DEVICE UNUSABLE*.

Further, these instructions *COMPLETELY ERASE WINDOWS AND THE RECOVERY
PARTITION* from the device, since I have no interest in running any Windows
version. This means that should something go wrong, you will have *NO WAY
TO RESTORE* the old functionality.


## Preparation

NOTE: If you plan to do a BIOS updgade, flash it from Windows before you
install Linux (unless you plan to dual boot). At present there is no way
to flash the BIOS from outside Windows.

By default the BIOS prompt is not displayed when booting, and secure boot
prevents anything except windows from being booted. The first thing to do is
undo this.

From Windows 10 choose Shutdown and hold SHIFT while pressing Restart. The
laptop should reboot to the BIOS screen. If not, reboot and press F2.

Once in the BIOS:
- Change the trackpad from 'Advanced' to 'Simple'
- Set a temporary supervisor password
- Change the boot type to 'Legacy'
- Move 'USB HDD' to the top of the boot order list
- Set the supervisor password to blank to disable it
- Press F10 to save and exit

Your laptop should now be able to boot from USB.


## Installation

Insert your bootable USB. I used The Fedora 23 KDE Spin (Fedora-Live-KDE-x86_64-23-10.iso)
but you may wish to try another flavour.

When the Fedora boot screen appears press TAB on the line to boot the live CD.

Type the following to add them to the kernel boot options:

```
nouveau.runpm=0 nouveau.modeset=0
```

Press ENTER and the live CD will boot to the desktop. There will be no wireless.

Install to the hard drive(s) using the Desktop icon.

When the install is finished and the machine reboots, press 'e' when the grub
menu appears and navigate to the end of the line starting with 'linux16'.

At the end of this line type:

```
nouveau.runpm=0 nouveau.modeset=0
```

Then press CTRL-x to boot. you should now be running Linux.


## Post Installation


### GRUB Command Line

As root, edit /etc/default/grub and add the line:

```
GRUB_CMDLINE_LINUX_DEFAULT="nouveau.runpm=0 nouveau.modeset=0"
```

NOTE: If there is already a line starting with GRUB_CMDLINE_LINUX_DEFAULT then just add
the options to the end of that line rather than adding another one.

As root, regenerate the grub config with:

```
grub2-mkconfig -o /boot/grub2/grub.cfg
```

Now when you reboot, you should not need to manually edit the grub config.


### Wireless

NOTE: The latest Fedora 23 kernels now contain firmware for the wireless card,
so updating using `dnf update` from a wired connection and then rebooting
should be all you need to do to get support.

If your wireless card is not recognised after you have run `yum update` and
rebooted, you need to install its firmware. This fix comes from
http://community.acer.com/t5/Linux/Linux-on-VN7-792G/td-p/391339.

As root, run the following commands to fetch and install the firmware:

```
mkdir -p /lib/firmware/ath10k/QCA6174/hw3.0/
rm /lib/firmware/ath10k/QCA6174/hw3.0/* 2> /dev/null
dnf install -y wget
wget -O /lib/firmware/ath10k/QCA6174/hw3.0/board.bin https://github.com/FireWalkerX/ath10k-firmware/blob/7e56cbb94182a2fdab110cf5bfeded8fd1d44d30/QCA6174/hw3.0/board-2.bin?raw=true
wget -O /lib/firmware/ath10k/QCA6174/hw3.0/firmware-4.bin https://github.com/FireWalkerX/ath10k-firmware/blob/7e56cbb94182a2fdab110cf5bfeded8fd1d44d30/QCA6174/hw3.0/firmware-4.bin_WLAN.RM.2.0-00180-QCARMSWPZ-1?raw=true
chmod +x /lib/firmware/ath10k/QCA6174/hw3.0/*
```

These commands are available in this repo as `install_wireless_fw.sh`.

You will need to reboot following this to pick up the new firmware.

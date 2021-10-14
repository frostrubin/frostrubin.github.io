---
title: Pixelbook Linux Setup

layout: page
category: wiki
---

## About
This page is a collection of various guides and hints that detail how to set up Linux booting on a Google Pixelbook.
I have not yet tested any of this, so far it is just a collection.

## Sources
- [Chromium OS - Disk Format](https://www.chromium.org/chromium-os/chromiumos-design-docs/disk-format)
- [chrx.org](https://chrx.org)
- [mrchromebox.tech](https://mrchromebox.tech)
- [ArchLinux Wiki > Chrome OS Devices](https://wiki.archlinux.org/title/Chrome_OS_devices#Introduction)
- [Fuchsia > Hardware > Pixelbook](https://fuchsia.dev/fuchsia-src/development/hardware/pixelbook)
- [Chromium OS > Developer Information for Devices > CTRL + F "Pixelbook"](http://www.chromium.org/chromium-os/developer-information-for-chrome-os-devices)
- [GitHub > MrChromebox > Scripts (normally run in developer mode to do everything automatically](https://github.com/MrChromebox/scripts)
- [GitHub > chrx > install-chroot.sh](https://github.com/reynhout/chrx/blob/master/dist/chrx-install-chroot)
- [Medium > Adrian Carroll > How to dual boot ChromeOS and Linux](https://medium.com/@adrian.carroll7/how-to-dual-boot-chromeos-and-linux-a-step-by-step-guide-68ed5b073e1b)
- [Fascinating Captain > How do I dual boot Chrome OS and Linux using a USB drive](http://www.fascinatingcaptain.com/projects/dual-boot-chrome-os-and-linux/)
- [Saagar Jha > Dual Booting Chrome OS and elementary OS](https://saagarjha.com/blog/2019/03/13/dual-booting-chrome-os-and-elementary-os/)

## Introduction
The Goal is to adapt the Pixelbook setup so that it can dual-boot Chrome OS ( in Developer Mode ) and Linux.
Without flashing the firmware, explicitly NOT having to remove/disable the firmware write-protect.
Without curl'ing unknown scripts into a root shell.
Using [Legacy Boot Mode](https://mrchromebox.tech/#bootmodes).

**All data on the Pixelbook is wiped in the process.**

## Needed Hardware
- Google Pixelbook
- USB Stick for Linux install Image (4GB to 8GB)
- USB Stick for Chrome OS Recovery Image (8GB)

## Preparations
You **MUST** prepare a USB Stick with a Chrome OS Recovery Image. If anything goes wrong during the installation/setup procedure, the device might end up 
without an OS to run. You don't want that.

- Install the [Chromebook Recovery Utility](https://chrome.google.com/webstore/detail/chromebook-recovery-utili/pocpnlppkickgojjlmhdmidojbmbodfm)
and create a bootable Recovery USB Stick.
- Use the same utility to download the Linux distribution of your Choice. Rename downloaded .iso file to .bin to use it in the Recovery Utility.

Further Safety Net: [Google Support > Chromebook wiederherstellen](https://support.google.com/chromebook/answer/1080595)

## Developer Mode
- [Chromium OS - Developer Mode](https://chromium.googlesource.com/chromiumos/docs/+/HEAD/developer_mode.md)
- [Chromium OS - Firmware Keyboard Interface](https://chromium.googlesource.com/chromiumos/docs/+/master/debug_buttons.md#Firmware-Keyboard-Interface)

Note that Developer Mode disables security features and may leave your device open to attack. Only enable if you understand the risks.

- Turn off your chromebook
- Press and hold the Esc key, refresh key, then press the power button at the same time. You should enter Recovery mode.
- When the "Chrome OS is missing or damaged. Please insert a recovery USB stick or SD Card." message shows up, press and hold the Ctrl and D keys simultaneously.
- Some Chromebooks may require you to turn OS verification off. Press Enter (if required).
- Wait for the device to restart and go through the Chromebook setup process. This will take several minutes. Initially it may not appear to be doing anything. Let the device sit for a minute or two. You will hear two loud BEEPs early in the process. The process is complete when you hear two more loud BEEPs.
- You will get an odd screen saying that OS verification is off. Keep in mind this screen will happen every single time you boot up.
- Press Ctrl and D to restart successfully.

From now on this there will be a warning screen on every boot. The screen will time out after 30 seconds, playing a warning beep.
From the warning screen, the following keyboard shortcuts are available:
- Ctrl + D: Boot the system from the internal disk (Chrome OS in Developer Mode)
- Ctrl + U: Boot the system from an external USB stick or SD card. The option crossystem dev_boot_usb=1 must be set from the command line before this option is available.
- Ctrl + L: Chain-load an alternative bootloader (e.g. SeaBIOS). The option crossystem dev_boot_legacy=1 must be set from the command line before this option is available.

## Enable Legacy Boot Mode & USB Boot
- Start the Chromebook
- You should see a screen that says "OS verification is OFF" and approximately 30 seconds later (or use CTRL + D to speed up) the boot will continue. 
- Wait for the Welcome or Login screen to load. Ignore any link for "Enable debugging features".
- Get to a command prompt. Details:
- One way to get the login prompt is through something called VT-2, or “virtual terminal 2”. You can get to VT-2 by pressing:
CTRL + ALT + XXX where the XXX key is the key in the F2 position which may be the refresh key or another key.
Once you have the login prompt, you should see a set of instructions telling you about command-line access. By default, you can login as the chronos user with no password. This includes the ability to do password-less sudo. The instructions on the screen will tell you how you can set a password. They also tell you how to disable screen dimming.

- In order to get back to the browser press:
CTRL + ALT + YYY where the YYY key is the left-arrow key just above the number 1 on your keyboard.
NOTE: The top-rows of the keyboard on a Chrome OS device are actually treated by Linux as the keys F1 through F10. Thus, the refresh key is actually F2 and the back key is actually F1.
NOTE: Kernel messages show up on VT-8.
- Press Ctrl + Alt + Refresh to enter a command shell. If pressing this key combination has no effect, try rebooting the Pixelbook once more.
- Enter 'chronos' as the user with a blank password
- Follow the on-screen instructions to set a password! Since your device will be permanently in developer mode, this is important!
- Enable USB booting by running sudo crossystem dev_boot_usb=1
- Enable Legacy Boot mode by running sudo crossystem dev_boot_legacy=1
- (Optional) Default to USB booting by running sudo crossystem dev_default_boot=usb (Not recommended because booting default from usb is insecure)

If you ever want to become root, use sudo su -

## Partitioning
- [Chromium OS > Disk Format](https://www.chromium.org/chromium-os/chromiumos-design-docs/disk-format)

Chromium OS comes with 12 partitions pre-installed. Removing ANY of these partitions breaks chrome OS.
The goal of "proper" partitioning is to keep ChromeOS intact _and_ have a working Linux system. To do this, one has to understand the partitioning scheme.

| Partition | Usage                                     | Purpose                                                                 |
| --------- | ----------------------------------------- | ----------------------------------------------------------------------- |
| 1	        | STATE user state, aka "stateful partition | User's browsing history, downloads, cache, etc. Encrypted per-user.     |
| 2	        | KERN-A kernel A                           | Initially installed kernel.                                             |
| 3	        | ROOT-A rootfs A                           | Initially installed rootfs.                                             |
| 4	        | KERN-B kernel B                           | Alternate kernel, for use by automatic upgrades.                        |
| 5	        | ROOT-B rootfs B                           | Alternate rootfs, for use by automatic upgrades.                        |
| 6	        | KERN-C kernel C                           | Minimal-size partition for future third kernel.                         |
| 7	        | ROOT-C rootfs C                           | Minimal-size partition for future third rootfs.                         |
| 8	        | OEM OEM customization                     | Web pages, links, themes, etc. from OEM.                                |
| 9	        | RESERVED MiniOS A	                        | Recovery partition A                                                    |
| 10	      | RESERVED MiniOS B	                        | Recovery partition B, for upgrades. Must reside at the end of the disk. |
| 11	      | RFWF Hibernate	                          | Small partition reserved for hibernation state.                         |
| 12	      | EFI-SYSTEM EFI System Partition           | Contains 64-bit grub2 bootloader for EFI BIOSes, and second-stage syslinux bootloader for legacy BIOSes. |

Each minimal-size partition (including the C kernel and C rootfs) is only 512 bytes, and is shoved into some space lost to filesystem alignment (between the primary partition table and the stateful partition). 64M of empty space is set aside for use by those reserved partitions if they ever need it.

Bootable USB keys have the same layout, except that kernel B and rootfs B are minimal-size, and partition 1 is limited to 720M. The total USB image size is around 1.5G. When the USB image is installed on a fixed drive, the B image is duplicated from the A image, and partition 1 is made as large as possible so that the entire disk is in use.

We want to use partition 6 or 7 to store our Linux installation. 
This is made possible because the _physical_ layout on the Disk is actuall different from the GPT partitioning table:
![Chrome OS physical partitioning](https://github.com/frostrubin/frostrubin.github.io/blob/master/wiki/images/chrome_os_partition_layout.png?raw=true)

With this understood, it becomes time to think about the sizes you want to allocate:
The current plan is to use 10 GB for STATE and the remaining space for Linux.

The command we will be using throughout this section is cgpt, which handles partitions on Chrome OS. **It is quite important that Chrome OS is the only thing that touches your partitions**. Alledgely, manipulating the partitions with other tools causes the "Chrome OS is missing or damaged".

cpgt requires superuser privileges to run, and always takes as an argument the device it will work on. 
For eMMC backed-computers, this will likely be /dev/mmcblk0; if you have an SSD this might be /dev/sda. 

According to [this guide](https://saagarjha.com/blog/2019/03/13/dual-booting-chrome-os-and-elementary-os/), you can do this all by hand:

First, list the partitions on the disk: 
```
$ sudo cgpt show /dev/mmcblk0
   start        size    part  contents
       0           1          PMBR (Boot GUID: BB000A0D-0001-0EB4-CD10-AC3C0075F4C3)
       1           1          Pri GPT header
       2          32          Pri GPT table
 8704000    52367312       1  Label: "STATE"
                              Type: Linux data
                              UUID: 56FACA9B-B561-5E4E-B225-67FF9101E64A
   20480       32768       2  Label: "KERN-A"
                              Type: ChromeOS kernel
                              UUID: 0981A5B2-C79A-E145-802A-DEA62009F8DA
                              Attr: priority=1 tries=0 successful=1 
 4509696     4194304       3  Label: "ROOT-A"
                              Type: ChromeOS rootfs
                              UUID: BDBF49E7-B855-D04E-B09E-FDF2FE9E72A9
   53248       32768       4  Label: "KERN-B"
                              Type: ChromeOS kernel
                              UUID: F7D51A74-2D18-5649-B352-B3C779269C71
                              Attr: priority=0 tries=15 successful=0 
  315392     4194304       5  Label: "ROOT-B"
                              Type: ChromeOS rootfs
                              UUID: D47975C2-3B96-F54A-AAA7-394802C5BED2
   16648           1       6  Label: "KERN-C"
                              Type: ChromeOS kernel
                              UUID: 64A4302A-7FFD-0543-9ADD-004D20919615
                              Attr: priority=0 tries=15 successful=0 
   16649           1       7  Label: "ROOT-C"
                              Type: ChromeOS rootfs
                              UUID: 09C55DBA-67B7-A14A-BC05-C93124403597
   86016       32768       8  Label: "OEM"
                              Type: Linux data
                              UUID: DB2D7C8A-7F32-1F46-BC47-313EAB282E35
   16450           1       9  Label: "reserved"
                              Type: ChromeOS reserved
                              UUID: 531EB8EE-4240-2D44-A85F-27521C3934B0
   16451           1      10  Label: "reserved"
                              Type: ChromeOS reserved
                              UUID: F2E20DFD-8967-DE4F-96A2-F780936B29A2
      64       16384      11  Label: "RWFW"
                              Type: ChromeOS firmware
                              UUID: 9AC55AEB-1593-C247-BC54-9C9D9998990B
  249856       65536      12  Label: "EFI-SYSTEM"
                              Type: EFI System Partition
                              UUID: CDCA82E4-547E-BE4E-AD55-C3B5EFD5DF79
                              Attr: legacy_boot=1 
61071327          32          Sec GPT table
61071359           1          Sec GPT header
```

The partitions are listed with their labels, partition number, start block, and size. Blocks are 512 bytes in length; notice that while the table is listed in logical order, it is not in physical order–the blocks are actually in a different order on disk.

It is recommended that you create - and understand - a table like below to grasp the logical order of partitions:


| Number | Label      | Start   | Size     |
| ------ | ---------- | ------- | -------- | 
| 11	   | RWFW	      | 64	    | 16384    | 
| 6	     | KERN-C	    | 16648	  | 1        | 
| 7	     | ROOT-C	    | 16649	  | 1        | 
| 9	     | reserved	  | 16450	  | 1        | 
| 10	   | reserved	  | 16451	  | 1        | 
| 2	     | KERN-A	    | 20480	  | 32768    | 
| 4	     | KERN-B	    | 53248	  | 32768    | 
| 8	     | OEM	      | 86016	  | 32768    | 
| 12	   | EFI-SYSTEM	| 249856  | 65536    | 
| 5	     | ROOT-B	    | 315392  | 4194304  | 
| 3	     | ROOT-A	    | 4509696	| 4194304  | 
| 1	     | STATE	    | 8704000	| 52367312 | 

Luckily, the STATE partition (which is the first one, logically) is right at the very end physically, which means we can shrink it easily. To do this, we can use cgpt add, which takes the partition number with -i, starting block with -b, and size in blocks with -s. For this, we will keep its starting point, block 8704000, the same and shrink the partition down to 10485760 blocks (5 GB):

```
sudo cpgt add -i 1 -b 8704000 -s 10485760 /dev/mmcblk0
```

Unfortunately, KERN-C and ROOT-C are wedged in the middle of other partitions, making it impossible to increase their size without overwriting the subsequent ones. However, we can just change their base to fall right after STATE and expand them in the new space we have created. 

Thus, KERN-C (the sixth partition) will now start at block 8704000+10485760+1=19189761 and span 1048576 blocks, while ROOT_C (the seventh) starts at 19189761+1048576+1=20238337. ROOT-C’s size is the amount we shaved off of STATE minus the size of KERN-C, or (52367312-10485760)-1048576=40832976. In cgpt, this translates to

``` 
sudo cgpt add -i 6 -b 19189761 -s 1048576 /dev/mmcblk0
sudo cgpt add -i 7 -b 20238337 -s 40832976 /dev/mmcblk0
```

With this done, you can reboot the system:

```
sudo reboot
```

When Chrome OS boots up, it will repair itself, taking about five minutes (there’s a small, somewhat accurate timer in the top left that tracks progress). Once it’s done booting, drop back into a shell; we now need to format the new partitions as ext4 so they will be usable from Linux.

Once you’re back in a shell, use mkfs.ext4 to initialize the partitions we will be using for Linux (KERN-C and ROOT-C, which are the sixth and seventh partitions in /dev/mmcblk0):

```
sudo /sbin/mkfs.ext4 /dev/mmcblk0p6
sudo /sbin/mkfs.ext4 /dev/mmcblk0p7
sudo reboot
```

To achieve this, we look at the routines of the chrx setup.
- [GitHub > chrx > setup-storage.sh](https://github.com/reynhout/chrx/blob/master/chrx-setup-storage)




## Install Linux
- Plug the USB drive with the Linux Installer into the Pixelbook. Avoid using a USB hub.
- Reboot by typing sudo reboot
- On the "OS verification is OFF" screen press CTRL+L to boot SeaBIOS.
- Press ESC to get the SeaBIOS Boot menu. Choose the Linxu Install USB Stick.
- ( Alternative would have been to press Ctrl+U directly after startup, to boot from USB immediately, however this is not recommended )
- If the device tries to boot from USB, either because that is the default or you pressed Ctrl+U, and the device fails to boot from USB you'll hear a fairly loud BEEP. Note that ChromeOS bootloader USB enumeration during boot has been observed to be slow. If you're having trouble booting from USB, it may be helpful to remove other USB devices until the device is through the bootloader and also avoid using a USB hub.
- XXX ???

## SeaBios
- Can be reached via CTRL + L on Boot
- You may get an error saying “graphics initialization failed”; apparently this is a bug that can be fixed [by typing "help" and pressing enter twice](https://ubuntuforums.org/showthread.php?t=1594003).

## Disable USB Boot
This does not completely prevent USB boot, since the Legacy Boot Mode (SeaBios) reachable via CTRL + L allows for USB booting too.
- Boot via CTRL + D
- Enter VT2
- sudo crossystem dev_boot_usb=0
- sudo reboot 

## Change GBB Options to prevent accidental Developer Mode Deactivation
This step requires a short disabling of the Firmware write protection, changing GBB Flags, then re-enabling the write protection

- [ArchLinux Wiki > Chrome OS Devices > Boot to SeaBios by Default](https://wiki.archlinux.org/title/Chrome_OS_devices#Boot_to_SeaBIOS_by_default)

- Enter a superuser shell by booting in developer mode, CTRL + D, accessing VT2 and logging in as chronos
- Disable Firmware write protection via flashrom --wp-disable
- Check that write protection is disabled flashrom --wp-status
- Run set_gbb_flags.sh with no parameters /usr/share/vboot/bin/set_gbb_flags.sh
This will list all of the available flags. The ones of interest to us are:

```
GBB_FLAG_DEV_SCREEN_SHORT_DELAY 0x00000001  
GBB_FLAG_FORCE_DEV_SWITCH_ON 0x00000008
GBB_FLAG_FORCE_DEV_BOOT_LEGACY 0x00000080
GBB_FLAG_DEFAULT_DEV_BOOT_LEGACY 0x00000400
```

- So, to set SeaBIOS as default, with a 1s timeout, prevent accidentally exiting Developer Mode via spacebar, and ensure Legacy Boot Mode remains enabled in the event of battery drain/disconnect, we set the flags as such: /usr/share/vboot/bin/set_gbb_flags.sh 0x489
- Enable back the software write protection flashrom --wp-enable

## Final Result
- Boot Pixelbook
- Press CTRL + D or wait 30 seconds -> Boots Chrome OS
- Press CTRL + L -> Opens SeaBios, which boots Linux

## Linux Specific Fixes
- Fix Suspend & More: https://wiki.archlinux.org/title/Chrome_OS_devices#Introduction
- Suspend via Script on specific USB Port: https://itectec.com/ubuntu/ubuntu-how-to-use-a-key-press-to-wake-a-suspended-laptop-when-using-a-kvm-switch/
- [GitHub > FascinatingCaptain > CBFixesAndTweaks](https://github.com/fascinatingcaptain/CBFixesAndTweaks)

## Linux Software
- sudo apt update
- sudo apt install ssh rsync vim
- sudo dpkg-reconfigure tzdata
- sudo apt install xbindkeys xdotool xbacklight xvkbd

## Install Chrome
```
curl -O https://dl-ssl.google.com/linux/linux_signing_key.pub
sudo apt-key add linux_signing_key.pub
sudo add-apt-repository -y "deb https://dl.google.com/linux/chrome/deb/ stable main"
sudo apt update
sudo apt install google-chrome-stable
```

## Linux Ideas
- openbox
- tint2 dock
- obconf
- obmenu
- lxappearance
- lxappearance-obconf

- https://www.youtube.com/watch?v=eRKtkmQ4yGI

## Windows
- [Reddit > Chrultrabook > Getting Started](https://www.reddit.com/r/chrultrabook/comments/aufp1q/getting_started_read_this_first/)
- [Reddit > Pixelbook > Windows Firmware Status Tracker](https://www.reddit.com/r/PixelBook/comments/aqpns2/windows_10_pixelbook_uefi_firmware_status_tracker/)
- [mrchromebox.tech > fwscript](https://mrchromebox.tech/#fwscript)

Yes, this OS exists too and is - alledgedly - relatively well supported on the Pixelbook
The WiFi Drivers [can be found here](https://www.intel.com/content/www/us/en/download/19351/windows-10-wi-fi-drivers-for-intel-wireless-adapters.html?product=99445)
Windows® 10 WiFi package drivers 22.70.0 for the AX210/AX200/9000/8000 series Intel® Wireless Adapters.

- Install WiFi drivers, then everything else should be automatically installed when running Windows Update.
- TapToClick Utility fixes missing tap-to-click functionality in the Pixelbook's touchpad driver
[KBL audio drivers] (https://mrchromebox.tech/files/windrv/kbl_cros_drivers_1.0.exe) 
- Run the driver installer, then go into Device Manager and point any unknown devices to the driver installation folder to search. At the end, you'll have a handful of unknown devices without drivers still.

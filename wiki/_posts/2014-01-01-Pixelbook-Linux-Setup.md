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
- [Fuchsia > Hardware > Pixelbook](https://fuchsia.dev/fuchsia-src/development/hardware/pixelbook)

## Introduction
The Goal is to adapt the Pixelbook setup so that it can dual-boot Chrome OS ( in Developer Mode ) and Linux.
Without flashing the firmware, explicitly NOT having to remove/disable the firmware write-protect.
Doing steps manually. Using [Legacy Boot Mode](https://mrchromebox.tech/#bootmodes).
All data on the Pixelbook is wiped in the process.

## Needed Hardware
- Google Pixelbook
- USB Stick for Linux install Image (4GB to 8GB)
- USB Stick for Chrome OS Recovery Image (8GB)

## Preparations
You MUST prepare a USB Stick with a Chrome OS Recovery Image. If anything goes wrong during the installation/setup procedure, the device might end up 
without an OS to run. You don't want that.

- Install the [Chromebook Recovery Utility](https://chrome.google.com/webstore/detail/chromebook-recovery-utili/pocpnlppkickgojjlmhdmidojbmbodfm)
and create a bootable Recovery USB Stick.
- Use the same utility to download the Linux distribution of your Choice. Rename downloaded .iso file to .bin to use it in the Recovery Utility.

## Developer Mode
[Chromium OS - Developer Mode](https://chromium.googlesource.com/chromiumos/docs/+/HEAD/developer_mode.md)
[Chromium OS - Firmware Keyboard Interface](https://chromium.googlesource.com/chromiumos/docs/+/master/debug_buttons.md#Firmware-Keyboard-Interface)

Note that Developer Mode disables security features and may leave your device open to attack. Only enable if you understand the risks.

- Turn off your chromebook
- Press and hold the Esc key, refresh key, then press the power button at the same time. You should enter Recovery mode.
- When the "Chrome OS is missing or damaged. Please insert a recovery USB stick or SD Card." message shows up, press and hold the Ctrl and D keys simultaneously.
- Some Chromebooks may require you to turn OS verification off. Press Enter (if required).
- Wait for the device to restart and go through the Chromebook setup process. This will take several minutes. Initially it may not appear to be doing anything. Let the device sit for a minute or two. You will hear two loud <BEEP>s early in the process. The process is complete when you hear two more loud <BEEP>s.
- You will get an odd screen saying that OS verification is off. Keep in mind this screen will happen every single time you boot up.
- Press Ctrl and D to restart successfully.

From now on this there will be a warning screen on every boot. The screen will time out after 30 seconds, playing a warning beep.
From the warning screen, the following keyboard shortcuts are available:
- Ctrl + D: Boot the system from the internal disk (Chrome OS in Developer Mode)
- Ctrl + U: Boot the system from an external USB stick or SD card. The option crossystem dev_boot_usb=1 must be set from the command line before this option is available.
- Ctrl + L: Chain-load an alternative bootloader (e.g. SeaBIOS). The option crossystem dev_boot_legacy=1 must be set from the command line before this option is available.

## Enable Legacy Boot Mode
- Start the Chromebook
- You should see a screen that says "OS verification is OFF" and approximately 30 seconds later (or use CTRL + D to speed up) the boot will continue. 
- Wait for the Welcome or Login screen to load. Ignore any link for "Enable debugging features".
- Press Ctrl + Alt + Refresh/F3 to enter a command shell. If pressing this key combination has no effect, try rebooting the Pixelbook once more.
- These are 
-Enter 'chronos' as the user with a blank password
Enable USB booting by running sudo crossystem dev_boot_usb=1
(optional) Default to USB booting by running sudo crossystem dev_default_boot=usb.
Plug the USB drive into the Pixelbook.
Reboot by typing sudo reboot
On the "OS verification is OFF" screen press Ctrl+U to bypass the timeout and boot from USB immediately. (See Tips and Tricks for other short circuit options)




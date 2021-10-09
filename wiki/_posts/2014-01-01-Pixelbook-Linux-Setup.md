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

Further Safety Net: [Google Support > Chromebook wiederherstellen](https://support.google.com/chromebook/answer/1080595)

## Developer Mode
[Chromium OS - Developer Mode](https://chromium.googlesource.com/chromiumos/docs/+/HEAD/developer_mode.md)
[Chromium OS - Firmware Keyboard Interface](https://chromium.googlesource.com/chromiumos/docs/+/master/debug_buttons.md#Firmware-Keyboard-Interface)

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
- Press Ctrl + Alt + Refresh/F3 to enter a command shell. If pressing this key combination has no effect, try rebooting the Pixelbook once more.
- These are classical Linux Virtual Terminals, VT1 to VT11
- Enter 'chronos' as the user with a blank password
- Enable USB booting by running sudo crossystem dev_boot_usb=1
- Enable Legacy Boot mode by running sudo crossystem dev_boot_legacy=1
- (Optional) Default to USB booting by running sudo crossystem dev_default_boot=usb (Not recommended because booting default from usb is insecure)

- Plug the USB drive with the Linux Installer into the Pixelbook. Avoid using a USB hub.
- Reboot by typing sudo reboot
- On the "OS verification is OFF" screen press Ctrl+U to bypass the timeout and boot from USB immediately.
If the device tries to boot from USB, either because that is the default or you pressed Ctrl+U, and the device fails to boot from USB you'll hear a fairly loud BEEP. Note that ChromeOS bootloader USB enumeration during boot has been observed to be slow. If you're having trouble booting from USB, it may be helpful to remove other USB devices until the device is through the bootloader and also avoid using a USB hub.





## Windows
[Reddit > Chrultrabook > Getting Started](https://www.reddit.com/r/chrultrabook/comments/aufp1q/getting_started_read_this_first/)

Yes, this OS exists too and is - alledgedly - relatively well supported on the Pixelbook
The WiFi Drivers [can be found here](https://www.intel.com/content/www/us/en/download/19351/windows-10-wi-fi-drivers-for-intel-wireless-adapters.html?product=99445)
Windows® 10 WiFi package drivers 22.70.0 for the AX210/AX200/9000/8000 series Intel® Wireless Adapters.

- Install WiFi drivers, then everything else should be automatically installed when running Windows Update.
- TapToClick Utility fixes missing tap-to-click functionality in the Pixelbook's touchpad driver
[KBL audio drivers] (https://mrchromebox.tech/files/windrv/kbl_cros_drivers_1.0.exe) 
- Run the driver installer, then go into Device Manager and point any unknown devices to the driver installation folder to search. At the end, you'll have a handful of unknown devices without drivers still.

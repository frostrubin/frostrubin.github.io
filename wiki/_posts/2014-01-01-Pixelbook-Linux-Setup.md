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

- Turn off your chromebook
- Turn on your Chromebook and
- Press and hold the Esc key, refresh key, and the power button at the same time.
- When the "Chrome OS is missing or damaged. Please insert a recovery USB stick or SD Card." message shows up, press and hold the Ctrl and D keys simultaneously.
- Some Chromebooks may require you to turn OS verification off. Press Enter (if required).
- Wait for the device to restart and go through the Chromebook setup process.
- You will get an odd screen saying that OS verification is off. Keep in mind this screen will happen every single time you boot up.
- Press Ctrl and D to restart successfully.




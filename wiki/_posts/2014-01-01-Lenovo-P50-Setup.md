---
title: Lenovo P50 Setup

layout: page
category: wiki
---

## Preparations

- Create an empty USB Stick with MBR Partition
  - Look up actual disk name in Disk Utility
  - Afterwards used the Terminal `diskutil partitionDisk disk2 1 MBR MS-DOS newdisk R`
- Use the [Windows 10 Media Creation Tool](https://www.microsoft.com/de-de/software-download/windows10ISO) on a Windows computer to create an installation medium.
- Create an empty USB Hard Drive
  - Look up actual disk name in Disk Utility
  - Afterwards used the Terminal `diskutil partitionDisk disk4 1 MBR MS-DOS newdisk R`

## Install
Attach both drives to a windows PC. In this example, `F:` is the stick with the installation files, `E:` is the USB SSD where we want to install Windows 10.

1. Open cmd.exe
2. `diskpart`
Inside the Diskpart console: 
3. `list disk` (Note the number of the USB SSD)
4. `select disk 1`
5. `clean`
6. `create partition primary`
7. `format fs=ntfs quick`
8. `active`
9. `assign letter=e`
10. `exit`
11. Close the command windows.
12. Open a command window with administrator privileges
13. `dism /apply-image /imagefile=f:\sources\install.esd /index:1 /applydir:e:\
14. `bcdboot e:\Windows /s e: /f ALL`

## Setup

- Create an administrator account
- Install all updates
- Install [Lenovo Drivers](http://support.lenovo.com/de/en/products/laptops-and-netbooks/thinkpad-p-series-laptops/thinkpad-p50?tabName=Downloads&linkTrack=Mast:SubNav:Support:Drivers%20and%20Software|Drivers%20and%20Software&beta=false)

- Create a normal (standard) user account

---
title: Windows Backup einrichten

layout: page
category: wiki
---

Unter Windows 7 ist es besonders einfach, ein brauchbares Backup ähnlich der Time Machine einzurichten.

1. `Control Panel > Backup and Restore`
2. Select "Backup Files" (for simple file backups) or "Backup Computer" (for whole system backup)
3. If you select "Backup Computer", this is where the fun starts. Vista/Win7 will actually back up your entire computer to a VHD file. This is the same file format that is used in Microsoft Virtual Machine technologies. So, it is a complete, full clone of your machine at that point in time. You can use it to restore the entire box. In Windows 7, you can even MOUNT the VHD file as a physical drive.

- [Wikipedia - Shadow Copy](http://en.wikipedia.org/wiki/Shadow_Copy)
- [Microsoft.com - Shadow Copy](http://www.microsoft.com/windows/windows-vista/features/shadow-copy.aspx)
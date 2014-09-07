---
title: Linux Minimal Debian to boot Chrome

layout: page
category: wiki
---

This how-to describes the setupup for a lightweight Debian Installation that boots directly into a Chrome Session. This is a single-user scenario, so no login manager, no passwords are used.

## Installing Debian
- Get the [Debian Testing Netinstall Image](http://www.debian.org/devel/debian-installer/)
- Install Languages, etc.
- Create a normal user `chrome` with a password of your choice.
- Choose to only install "basic packages"

## Choose default boot volume
Normally, the new Debian Installation sets itself as the default at boot up. However, since we dont want to waste a whole laptop to this experiment, it is only a secondary OS. So, make the first one (in my case a version of ubuntu) default again.

`sudo nano /etc/default/grub` and change the `GRUB_DEFAULT` (starts counting at 0) to the according entry.
`sudo update-grub` writes the new settings to the boot sector.

## Install Xserver and window manager
````
apt-get install xserver-xorg beep bsdgames
apt-get install openbox openbox-themes obconf obmenu
apt-get install xinit
apt-get install apt-file
````

## Install Google Chrome Beta
````
curl -C - -O http://dl.google.com/linux/direct/google-chrome-beta_current_i386.deb
sudo dpkg -i google-chrome-beta_current_i386.deb
````
Beim Sudo Install evtl. Abhängigkeiten auflösen:
````
apt-get install libasound2
apt-get -f
sudo dpkg -i google-chrome-beta_current_i386.deb
````

## Run Chrome directly at startup
````
apt-get install rungetty
nano /etc/inittab
````
change `1:2345:respawn:/sbin/getty 38400 tty1` to `1:2345:respawn:/sbin/rungetty --autologin chrome tty1`

## Make X the standard session
`nano .bash_profile`
````
if [ -z "$DISPLAY" ] && [ $(tty) == /dev/tty1 ]; then
  startx;
fi 
````

## Run Google Chrome directly
`nano ~/.config/openbox/autostart.sh` and insert `google-chrome &`
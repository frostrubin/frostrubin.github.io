---
title: Chrome OS Setup

layout: page
category: wiki
---

## Hardware
- Google Pixelbook
- Power Adapter: 45W USB-C

## Switching to Beta Channel
- Settings => About Chrome OS => Detailed Build Information => Change Channel

## Settings
- Go over all Settings
- Go over all Android Settings

## Most importand Key bindings
- Get a List of Keyboard Shortcuts: CTRL+ALT+/
- Open a Terminal: CTRL+ALT+T
  - Type 'help' or 'help_advanced' to get a list of all possible commands
- Keyboard Backlight: ALT+Brighntness Up/Down
- Screenshot Full: CTRL+Switch Windows
- Screenshot Part: CTRL+SHIFT+Switch Windows
- Switch betwenn last two Keyboard Layouts: CTRL+SPACE

## Install Extensions & Chrome Apps
- [Umlaut Helper](https://github.com/frostrubin/Chrome-Extensions)
- [Adblock Plus](https://chrome.google.com/webstore/detail/adblock-plus/cfhdojbkjhnklbpkdaibdccddilifddb)
- [Extension Developer Tools](https://chrome.google.com/webstore/detail/chrome-apps-extensions-de/ohmmkhmmmpcnpikjeljgnaoabkaalbgc)
- [Calculator](https://chrome.google.com/webstore/detail/calculator/joodangkbfjnajiiifokapkpmhfnpleo)
- [Google Keep](https://chrome.google.com/webstore/detail/google-keep-notes-and-lis/hmjkmjkepdijhoojdojkdfohbdgmmhki)
- [JSTorrent](https://chrome.google.com/webstore/detail/jstorrent/anhdpjpojoipgpmfanmedjghaligalgb)
- [Secure Shell](https://chrome.google.com/webstore/detail/secure-shell/pnhechapfaindjhompbnflcldabbghjo)
- [Web Server for Chrome](https://chrome.google.com/webstore/detail/web-server-for-chrome/ofhbbkphhbklhfoeikjpcbhemlocgigb)

## Install Android Apps
- [RAR](https://play.google.com/store/apps/details?id=com.rarlab.rar&hl=en)
- [File Manager+](https://play.google.com/store/apps/details?id=com.alphainventor.filemanager&hl=en)

## Enable U2F Button
- Open the Console (Ctrl + Alt + T), and enter
```
u2f_flags u2f,user_keys
```

See also: https://sites.google.com/a/chromium.org/dev/chromium-os/u2f-ecdsa-vulnerability

## Enable GPU for Linux
1. Enable here: chrome://flags/#crostini-gpu-support
2. Restart
3. sudo apt update; sudo apt dist-upgrade
4. glxinfo -B
5. sudo apt install cros-gpu-alpha ( Make sure you want this ! )

## Backup Linux Container
1. Enable here: chrome://flags/#crostini-backup
2. Restart
3. lxc image list
4. lxc publish penguin --alias backup reports

## Interesting Links
### General
- [Chrome OS Recovery](https://support.google.com/chromebook/answer/1080595)
- [All Chrome Extensions made by Google](https://chrome.google.com/webstore/category/collection/by_google)
- [All Android Apps made by Google](https://play.google.com/store/apps/dev?id=5700313618786177705&hl=en)
- [Building a more Secure Dev Environment](https://blog.lessonslearned.org/building-a-more-secure-development-chromebook/)


### Extension Development
- [List of APIs](https://developer.chrome.com/extensions/api_index)
- [Extra Keyboards for Chrome OS](https://github.com/google/extra-keyboards-for-chrome-os)
- [Hangul IME Extension with Candidate Window](https://github.com/googlei18n/google-input-tools/blob/fc9f11d80d957560f7accf85a5fc27dd23625f70/chrome/os/nacl-hangul/js/ime.js)

### Linux Containers
- [Containers & VMs in Chrome OS](https://chromium.googlesource.com/chromiumos/docs/+/master/containers_and_vms.md)
- [So you bought a Pixelbook](https://blog.drewolson.org/so-you-bought-a-pixelbook/)

#### Script to rename Files by Parent Folder Name
    #!/usr/bin/env bash

    # Rename files to the name of their parent folder,
    # followed by a counter.

    set -e

    mkdir -p ./output

    counter=0
    oldparent=""

    find . -type f -not -path "./output/*" | sort | while read line; do
        echo "Processing file '$line'"
        parent=$(dirname "$line")
        parent=${parent##*/}
        if [ "$oldparent" == "$parent" ]; then
            let counter++
        else
            oldparent="$parent"
            counter=1
        fi
        extension="${line##*.}"
        cp "$line" "./output/$parent $counter.$extension"
    done

#### Script to rename Files (dash to dot)
    #!/usr/bin/env bash

    set -e
    mkdir -p ./output

    find . -type f -not -path "./output/*" -not -path '*/\.*' | while read line;
    do
        echo "$line"
        newname="./output/${line##*/}"
        cp "$line" "${newname//-/.}"
    done

### Create Bootable Media
1. Download Googles Official [Chromebook Recovery Utility](https://chrome.google.com/webstore/detail/chromebook-recovery-utili/jndclpdbaamdhonoechobihbbiimdgai)
2. Rename your .ISO file to .BIN
3. Use the Cog Menu in the App to create a bootable USB Stick

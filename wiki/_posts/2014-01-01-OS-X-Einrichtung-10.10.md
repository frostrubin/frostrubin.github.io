---
title: OS X Einrichtung 10.10

layout: page
category: wiki
---

## Datensicherung

1. `my sync` ist ausgeführt
2. Time Machine Backup ist erstellt
3. Firefox Passwörter exportieren
4. Dateien aus `~` sichern
5. WLAN Passwort aufschreiben

## Systeminstallation

### Installationsmedium

1. [Installer](https://itunes.apple.com/WebObjects/MZStore.woa/wa/viewSoftware?id=915041082&mt=12) aus dem App Store herunterladen
2. Einen 8GB USB Stick mit HFS+/GUID formatieren ("Untitled" als Name vergeben)
3. `sudo /Applications/Install\ OS\ X\ Yosemite.app/Contents/Resources/createinstallmedia --volume /Volumes/Untitled --applicationpath /Applications/Install\ OS\ X\ Yosemite.app --nointeraction`

### Installation

1. Den Mac mit gedrückter ALT-Taste starten. Vom USB Stick booten
2. Neu partitionieren, OS X installieren
3. Administratorkonto anlegen & keine iCloud Login Daten eingeben
 
## Grundlegende Einrichtung

### Administrator Aktivitäten
 
- Updates einspielen
- Normales Benutzerkonto anlegen
 
### Weiter als normaler User
 
- `Sharing` > Hostname ändern
- `General > Sidebar Icon Size` auf `Small` setzen
- `General > Recent Items` auf `None` setzen
- `Mission Control > Dashboard Off` setzen
- `Accessibility > Enable Dragging with Drag Lock` setzen
- `Accessibility > Reduce Transparency` setzen
- `Security & Privacy > Advanced` Apple Remote ausschalten
- In den Time Machine Preferences bestimmte Ordner ausschließen: `/Applications`, `~/Library/Caches`, `/Library/Caches`, `~/Documents`
- In den Time Machine Einstellungen den Schalter auf `Off` setzen
- Drucker anschließen und Treiber per Software Update installieren
 
## Homeverzeichnis einrichten

### Backup einspielen

    # Neues /pub anlegen
    $ su administrator
    $ sudo mkdir /pub
    $ sudo chmod 755 /pub
    $ sudo chown bernhard:staff /pub

    # Bash Dateien wiederherstellen
    # 1. ~/.bash_profile
    # 2. ~/.ppdirs.txt
    # 3. ~/.my_helpers.sh

## User-Interface aufhübschen
 
### Versteckte OS X Einstellungen machen

## Dock 

    # Klick auf Programm versteckt alle anderen Programme
    defaults write com.apple.dock single-app -bool YES

    # Icons von versteckten Programmen transparent zeigen
    defaults write com.apple.dock showhidden -bool YES
 
    # Dateien können direkt auf Programmicons gezogen werden
     defaults write com.apple.dock enable-spring-load-actions-on-all-items -bool YES
 
    # Inhalt des Docks kann nicht mehr verändert werden
    defaults write com.apple.Dock contents-immutable -bool YES

    # Größe des Docks kann nicht mehr verändert werden
    defaults write com.apple.Dock size-immutable -bool YES

    # Position des Docks kann nicht mehr verändert werden
    defaults write com.apple.Dock position-immutable -bool YES

## Finder 

    # Vollständige Pfadangabe im Finder
    defaults write com.apple.finder _FXShowPosixPathInTitle -bool YES
 
    # Erweiterter "Drucken" Dialog als Standard
    defaults write -g PMPrintingExpandedStateForPrint -bool YES
 
    # Erweiterter "Speichern unter" Dialog als Standard
    defaults write -g NSNavPanelExpandedStateForSaveMode -bool YES

# Werte setzen

    killall Dock
    killall Finder

## Software-Installation

### Obligatorische Software

- Mac App Store Apps
- [Calibre](http://calibre-ebook.com/download)
- [Dropbox](http://www.getdropbox.com)
  - Upload Rate nicht limitieren
- [Firefox](http://www.mozilla.com/en-US/firefox/personal.html) + Adblock Plus
- [GitHub](http://mac.github.com/) + Command Line Tools während Installation wählen
- [Google Chrome](https://www.google.com/landing/chrome/beta/)
  - Add Adblock [filters for Facebook](http://facebook.adblockplus.me/en/)
- [gsutil](https://cloud.google.com/storage/docs/gsutil_install)
- [Handbrake](http://handbrake.fr/downloads.php)
- [JDownloader](http://jdownloader.org/download/index)
- [Max](http://sbooth.org/Max)
- [MiddleClick](http://clement.beffa.org/labs/projects/middleclick/)
- [NameChanger](http://www.mrrsoftware.com/MRRSoftware/NameChanger.html)
- [Sublime Text](http://www.sublimetext.com/)
- [µTorrent](http://www.utorrent.com)
- [VLC](http://www.videolan.org/vlc)

### Terminal Settings

- Font: Courier 13pt.
- Text: RGB10,6,6
- Bold Text: RGB125,40,25
- Selection: RGB115,115,80 Opacity 76%
- Cursor: RGB55,35,35 Opacity 65%
- Background Color: RGB107,107,107
- Paste Newlines as Carriage Returns: Off
- Set locale environment variables on startup: Off

### Max

Apple MPEG-4 Audio (AAC)

- Extension: m4a
- Quality: Maximum
- Bitrate: 320
- VBR: No

Apple MPEG-4 Audio (Apple Lossless)

- MP3
- Quality: Best

### Handbrake

- Video Quality: 17
- Languages
  - DE, EN AC3 Passthrough
  - DE, EN AAC Stereo Auto 192

### Server Connections
Connect to some servers to get them into the recent server list

- [https://webdav.mediencenter.t-online.de](https://webdav.mediencenter.t-online.de)
- [https://webdav.hidrive.strato.com](https://webdav.hidrive.strato.com)

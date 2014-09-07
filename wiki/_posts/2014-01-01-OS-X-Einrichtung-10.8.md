---
title: OS X Einrichtung 10.8

layout: page
category: wiki
---

## Systeminstallation
### Installationsmedium

#### Aus dem App Store

1. [Installer](http://itunes.apple.com/de/app/os-x-mountain-lion/id537386512?mt=12) aus dem App Store herunterladen
2. Einen 8GB USB Stick mit HFS+/GUID formatieren
3. Im Finder `/Applications/Installer/Contents/SharedSupport/InstallESD.dmg` finden
4. In Disk Utility im Reiter "Restore" das DMG auf die "Quelle" ziehen, den USB Stick als "Ziel" verwenden

#### Von einer existierenden Recovery HD

1. Mit dem [OS X Recovery Disk Assistant](http://support.apple.com/kb/DL1433) ein Installationsmedium erstellen

### Datensicherung

1. `my sync` ist ausgeführt
2. Time Machine Backup ist erstellt
3. Firefox Passwörter exportieren
4. iTunes Smart Playlists mit [Doug's Script](http://dougscripts.com/itunes/scripts/ss.php?sp=exportsmartcriteria) [(download)](./exportsmartcriteria.zip) sichern
5. Dateien aus `~` sichern

### Installation

1. Den Mac mit gedrückter ALT-Taste starten. Vom USB Stick booten.
2. Entweder neu partitionieren, oder die existierende Partition leeren
3. Administratorkonto anlegen & keine iCloud Login Daten eingeben
 
## Grundlegende Einrichtung
### Als Administrator
 
- Updates einspielen
- Normales Benutzerkonto anlegen, gleich die richtige Shell vergeben (per Rechtsklick auf den Benutzernamen).
- Systemeinstellungen absichern
 
### Grundeinrichtung als normaler User
 
- Hostname ändern
- `General > Sidebar Icon Size` auf `Small` setzen
- `General > Recent Items` auf `None` setzen
- `Accessibility > Enable Dragging with Drag Lock` setzen
- Apple Remote Pairen oder ausschalten
- Drucker anschließen und Treiber per Software Update installieren
- In den Time Machine Preferences bestimmte Ordner ausschließen: `/Applications`, `~/Library/Caches`, `/Library/Caches`, `~/Documents`
- In den Time Machine den Schalter auf `Off` setzen
 
## Homeverzeichnis einrichten
### Backup einspielen
```bash
# Neues /pub anlegen
$ su administrator
$ sudo mkdir /pub
$ sudo chmod 755 /pub
$ sudo chown bernhard:staff /pub
```

```bash
# Symlinks setzen
$ ln -s /Users/bernhard/Unix/bash_profile.sh /Users/bernhard/.bash_profile
```

## User-Interface aufhübschen
 
### Versteckte OS X Einstellungen machen
```bash
## Dock 
# Icons von versteckten Programmen transparent zeigen
  defaults write com.apple.Dock showhidden -bool YES
 
# Dateien können direkt auf Programmicons gezogen werden
  defaults write com.apple.dock enable-spring-load-actions-on-all-items -boolean YES
 
# Das Dock an eine der 3 Seiten bringen ("left", "bottom", "right")
  defaults write com.apple.dock orientation -string left
 
# Das Dock ein einer Ecke anbringen ("start", "middle",  "end")
  defaults write com.apple.dock pinning -string end
 
# Klick auf Programm versteckt alle anderen Programme
  defaults write com.apple.dock single-app -bool TRUE

# Größe des Docks setzen
  defaults write com.apple.dock tilesize -int 40
 
# Inhalt des Docks kann nicht mehr verändert werden
  defaults write com.apple.Dock contents-immutable -bool true

# Größe des Docks kann nicht mehr verändert werden
  defaults write com.apple.Dock size-immutable -bool true

# Position des Docks kann nicht mehr verändert werden
  defaults write com.apple.Dock position-immutable -bool true

## Finder 
# Vollständige Pfadangabe im Finder
  defaults write com.apple.finder _FXShowPosixPathInTitle -bool YES
 
# Erweiterter "Drucken" Dialog als Standard
  defaults write -g PMPrintingExpandedStateForPrint -bool TRUE
 
# Erweiterter "Speichern unter" Dialog als Standard
  defaults write -g NSNavPanelExpandedStateForSaveMode -bool TRUE

# Per default lokal (nicht iCloud) speichern
  defaults write NSGlobalDomain NSDocumentSaveNewDocumentsToCloud -bool FALSE

# "Drucken" App automatisch schließen wenn alles gedruckt ist
  defaults write com.apple.print.PrintingPrefs "Quit When Finished" -bool TRUE

# Erweiterte Optionen für Disk Utility
# defaults write com.apple.DiskUtility advanced-image-options 1

# Dashboard deaktivieren
  defaults write com.apple.dashboard mcx-disabled -boolean YES

# Werte setzen
  killall Dock
  killall Finder
```

## Icons
- [Leopard folders on demand](http://sebdominguez.deviantart.com/art/Leopard-folders-on-demand-193163771) nutzen um die Icons eigener Ordner zu ändern 

## Software-Installation
### Obligatorische Software
- Mac App Store Apps
- [Calibre](http://calibre-ebook.com/download)
- [Dropbox](http://www.getdropbox.com)
  - Upload Rate nicht limitieren
- [Firefox](http://www.mozilla.com/en-US/firefox/personal.html) + Adblock Plus
- [GitHub](http://mac.github.com/)
- [Google Chrome](https://www.google.com/landing/chrome/beta/)
  - Add Adblock [filters for Facebook](http://facebook.adblockplus.me/en/)
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
- Background Color: RGB125,110,105
- Paste Newlines as Carriage Returns: Off
- Set locale environment variables on startup: Off

### Max
Apple MPEG-4 Audio (AAC)
* Extension: m4a
* Quality: Maximum
* Bitrate: 320
* VBR: No

Apple MPEG-4 Audio (Apple Lossless)

MP3
* Quality: Best

### Handbrake
- Video Quality: 17
- Languages
  - DE, EN AC3 Passthrough
  - DE, EN AAC Stereo Auto 192

### Mavericks Tipp:
[Java 7 Usage HowTo](http://mosx.tumblr.com/post/64402950499/os-x-tip-execute-java-apps-like-minecraft-or)
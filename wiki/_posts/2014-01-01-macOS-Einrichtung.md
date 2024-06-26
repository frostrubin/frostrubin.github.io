---
title: macOS Einrichtung

layout: page
category: wiki
---

## Datensicherung

1. Firefox Passwörter exportieren
2. Firefox & Chrome Lesezeichen exportieren
3. Dateien aus `~` sichern
4. WLAN Passwort aufschreiben

## System

### Installation

1. Neu partitionieren, macOS installieren
2. Administratorkonto anlegen & keine iCloud Login Daten eingeben
 
## Grundlegende Einrichtung

### Administrator Aktivitäten
 
- Updates einspielen
- Normales Benutzerkonto anlegen
 
### Weiter als normaler User
 
- `Sharing` > Hostname ändern
- `General > Sidebar Icon Size` auf `Large` setzen
- `General > Recent Items` auf `None` setzen
- `Mission Control > Dashboard Off` setzen
- `Accessibility > Enable Dragging with Drag Lock` setzen
- `Accessibility > Reduce Transparency` setzen
- `Security & Privacy > Advanced` Apple Remote ausschalten
- `Security & Privacy > FileVault` einschalten
- `Dictation & Speech > Shortcut` Off wählen
- `Keychain Access` > Anderes Passwort für Login Keychain vergeben
- Drucker anschließen und Treiber per Software Update installieren
 
## Homeverzeichnis einrichten

## User-Interface aufhübschen
 
### Versteckte macOS Einstellungen machen

#### Dock 

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

#### Finder 

    # Vollständige Pfadangabe im Finder
    defaults write com.apple.finder _FXShowPosixPathInTitle -bool YES
 
    # Erweiterter "Drucken" Dialog als Standard
    defaults write -g PMPrintingExpandedStateForPrint -bool YES
 
    # Erweiterter "Speichern unter" Dialog als Standard
    defaults write -g NSNavPanelExpandedStateForSaveMode -bool YES

#### Werte setzen

    killall Dock
    killall Finder

## Software-Installation

Achtung! NUR noch Apps installieren, die kein Admin Passwort verlangen. 

### Obligatorische Software

- [aws-cli](https://aws.amazon.com/cli/) mit den Parametern 'eu-west-1' und 'json'
- [Firefox](https://www.mozilla.org/en-US/firefox/new/) + Adblock Plus
- [Google Chrome](https://www.google.com/landing/chrome/beta/)
  - Add Adblock [filters for Facebook](http://facebook.adblockplus.me/en/), [filters for cookie law](http://prebake.eu/)
- [BetterDisplay](https://github.com/waydabber/BetterDisplay)
- [JDownloader](http://jdownloader.org/download/index)
- [MiddleClick](http://clement.beffa.org/labs/projects/middleclick/)
- [Sublime Text](http://www.sublimetext.com/)
- [µTorrent](http://www.utorrent.com)
- [VLC](http://www.videolan.org/vlc)

### Terminal Settings

- Terminal.app aufrufen und `Terminal > Secure Keyboard Entry` wählen
- "Novel" Setting wählen. Größe: 160x30
- Paste Newlines as Carriage Returns: Off

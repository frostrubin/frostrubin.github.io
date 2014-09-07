---
title: Scripts Praktische Terminal Kommandos

layout: page
category: wiki
---

### rsync
```
rsync -av  /Volumes/Backup\ 2/Daten /Volumes/Backup\ 2/neuiii
```

### rsync zu Strato HiDrive
```
#!/bin/bash
hidrive_ssh_key="/tmp/HiDriveRSA.txt"

# Get the SSH Key
echo "Getting HiDrive SSH Key"
idrsa=$(security 2>&1 >/dev/null find-generic-password -gs HiDriveIDRSA | cut -d '"' -f 2|sed s/\\\\012/\\\\n/g)
echo -e "$idrsa" > "$hidrive_ssh_key"
chmod 600 "$hidrive_ssh_key"

rsync -avz -e "ssh -i $hidrive_ssh_key -l username" /Users/bernhard/Desktop/Daten \
username@rsync.hidrive.strato.com:/users/username/Your/Folder/Hierarchy
```

### T-Online Mediencenter mounten
```
osascript -e 'tell application "Finder" to mount volume "https://webdav.mediencenter.t-online.de"' &> /dev/null
```

### Crontab Header
```
#M S T M W Befehl
#* * * * * auszuführender Befehl
#┬ ┬ ┬ ┬ ┬
#│ │ │ │ └──── Wochentag (0-7) (Sonntag =0 oder =7)
#│ │ │ └────── Monat (1-12)
#│ │ └──────── Tag des Monats (1-31)
#│ └────────── Stunde (0-23)
#└──────────── Minute (0-59)
# Examples:
#01 * * * * /etc/cron.hourly
#02 4 * * * /etc/cron.daily
#22 4 * * 0 /etc/cron.weekly
#42 4 1 * * /etc/cron.monthly
PATH=/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin
```


### Dateien finden, die nicht vom Typ m4a sind
```bash
  find . ! -name '*m4a*'
```
  
### Berechtigungen aller m4a Dateien ändern
```bash
  find . -name "*.m4a" -exec chmod 670 {} \;
```

### Berechtigungen aller Unterordner ändern
```bash
  find . -type d -exec chmod 775 {} \;
```

### Datei in mehrere Unterordner kopieren
```bash
  for i in ./*; do cp ./Preview.jpg $i/Preview.jpg; done
```

### Dateierweiterung aller Dateien im Ordner ändern
```
ls -d *.txt | sed 's/\(.*\).txt$/mv "&" "\1.md"/' | sh
```

### Bestimmte Zeilen löschen
```bash
cat tree.txt |sed '/.IFO/d' > liste.txt
```

### Ein Bild 17° nach Links drehen und zu png konvertieren
```bash
  convert -rotate 348 -background none \
  sysprefs.jpg new.png
```

### Text an den Anfang einer Datei schreiben
```bash
  for i in ./*; do echo "~~NOTOC~~" | cat - $i > ./new/$i; done
```

### Bestimmte Dateien entfernen
```bash
  find . -name "Thumbs.db" -exec rm -rf {} \;
```

### Ordnerbäume vergleichen
```bash
  diff -urp /originaldirectory /modifieddirectory
```

### Ordnerstruktur kopieren
```bash
  find . -type d -exec mkdir -p /tmp/copy_dest2/{} \;
```
oder
```
rsync -a -f"+ */" -f"- *" /Volumes/Disk1 ~/Desktop/ppdirs/
```

### Dateien vergleichen
```bash
  opendiff file1.txt file2.txt
```

### Leerzeichen aus Dateiname entfernen
```bash
  for i in *.png; do echo "$i"; mv "$i" `echo "$i" |sed s/" "//g`; done
```

### Dateiname erweitern
```bash
  for i in *.png; do echo "$i"; mv "$i" scpm_"$i"; done
```

### Check ob $variable eine zahl ist
```bash
  if [ -z $(echo $var | grep [0-9]) ]; then echo "NON NUMERIC"; fi
```

### Dateiname und Extension bestimmen
```bash
  FILENAME=`echo ${FILE##*/}`;
  FILEPATH=`echo ${FILE%/*}`;
  NOEXT=`echo ${FILENAME%\.*}`;
  EXT=`echo ${FILE##*.}`
```

### Tastendruck im Terminal einlesen
```bash
  stty cbreak -echo; KEY=$(dd bs=1 count=1 2>/dev/null); stty -cbreak echo
```

### Datei zurückdatieren (YYYYMMDDhhmmss)
```bash
  touch -t 20090101050202 /location/of/file/filename.png
```

### Binärzahlen erstellen
```bash
  echo {0..1}{0..1}{0..1}{0..1}
```

### Große Dummy Dateien erstellen
```bash
  mkfile 12000m 12GB
```

#### Alternativ
```bash
  dd if=/dev/urandom of=~/Desktop/12GB bs=1000000 count=12000
```

### CSS Newlines einfügen
```bash
cat touch_screen.css | sed "s/}/}\\`echo -e '\r'`/g" > 2.css
```
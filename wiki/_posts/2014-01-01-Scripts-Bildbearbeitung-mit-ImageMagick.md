---
title: Scripts Bildbearbeitung mit ImageMagick

layout: page
category: wiki
---

# Scripts: Bildbearbeitung mit ImageMagick #
ImageMagick ist ein mächtiges Kommandozeilen Tool zur Bildbearbeitung.
Hier habe ich einige nützliche Beispiele gesammelt.   
Wirklich gute Scripte gibt es bei [FMW Concepts](http://www.fmwconcepts.com/imagemagick/3Drotate/index.php).

## Transparenten Rand an ein Bild anfügen ##
```bash
convert -resize 200x500 source.png source.png|\
convert -size 200x420 xc:transparent source.png \
-gravity south -composite target.png
```

## Bild rotieren ##
```bash
# This rotates an image by 17° and converts it to png
convert -rotate 348 -background none source.jpg target.png
```

## CD Cover resizen ##
```bash
#!/bin/bash

size=65
SAVEIFS=$IFS
IFS=$(echo -en "\n\b")
for i in $(ls ./Women/);do
echo "$i"
#the \! forces the size to EXACTLY 65x65
convert -gravity Center -resize \
${size}x${size}\! ./Women/"$i" ./thumbs/"${i%\.*}".png 
done
IFS=$SAVEIFS
```

## EXIF Titel setzen ##
Mithilfe von [ExifTool](http://www.sno.phy.queensu.ca/~phil/exiftool) kann man EXIF Daten bearbeiten.
```bash
#!/bin/bash
 
size=65
SAVEIFS=$IFS
IFS=$(echo -en "\n\b")
for i in $(ls ./thumbs/);do
echo "$i"
exiftool -title=""${i%\.*}"" ./thumbs/"$i"
done
IFS=$SAVEIFS

find ./thumbs/ -name "*.jpg_original" -exec rm -rf {} \;
find ./thumbs/ -name "*.png_original" -exec rm -rf {} \; 
```

## Cover zum Hochladen vorbereiten ##
```bash
#!/bin/bash
 
SAVEIFS=$IFS
IFS=$(echo -en "\n\b")
# Convert every Image to jpg
for i in $(ls ./thumbs/);do
  if [[ $i == *.png* ]]; then
    echo Konvertiere: "$i"
    convert ./thumbs/"$i" ./thumbs/"${i%\.*}".jpg
    rm -f ./thumbs/"$i"
  fi
done

# Set simple jpeg title
for i in $(ls ./thumbs/);do
  echo Setze Titel für: "${i%\.*}"
  exiftool -title=""${i%\.*}"" ./thumbs/"$i"
done

# Delete exif original remains
echo Lösche Originale
find ./thumbs/ -name "*.jpg_original" -exec rm -rf {} \;

# Change File Names
for i in $(ls ./thumbs/);do
echo Benenne "$i" um
text="$i"
text=$(echo "$i"|sed 's/!//g')
text=$(echo "$text"|sed 's/ñ/n/g')
text=$(echo "$text"|sed 's/?//g')
text=$(echo "$text"|sed 's/⚡//g')
text=$(echo "$text"|sed 's/é/e/g')
text=$(echo "$text"|sed 's/ /_/g')
text=$(echo "$text"|sed 's/ó/o/g')
text=$(echo "$text"|sed 's/á/a/g')
text=$(echo "$text"|sed 's/,//g')
text=$(echo "$text"|sed 's|/|_|g')
text=$(echo "$text"|sed 's/½/12/g')
text=$(echo "$text"|sed 's/&/-/g')
text=$(echo "$text"|sed 's/ê/e/g')
text=$(echo "$text"|sed 's/[()]//g')
text=$(echo "$text"|sed 's/[[]]//g')
text=$(echo "$text"|sed "s/\\'//g")
text=$(echo "$text"|sed "s/[:]/_/g")
mv ./thumbs/"$i" ./thumbs/"$text"
done

IFS=$SAVEIFS
```

## Rand entfernen ##
```bash
for i in `ls ./*.png`; do 
  echo "$i"
  convert -chop 141x214 "$i" "$i"
  convert -rotate 180 "$i" "$i"
  convert -chop 141x214 "$i" "$i"
  convert -rotate 180 "$i" "$i"  
done
```

## Bild rechteckig machen (für Icon) ##
```bash
size=512
convert \
  <(convert -resize ${size}x${size} xc:transparent png:-) \
  <(convert -resize ${size}x${size} source.jpg png:-) \
  -gravity Center -composite target.png
```

## PDF zu PNG ##
### Jede Seite als extra Bild
```bash
convert -density 200 'input.pdf' output.png
```

### Eine bestimmte Seite
```bash
convert -density 200 'input.pdf[4]' output.png
```

## ICNS zu PNG (Batch) ##
```bash
mkdir -p ./OutPutPNG
 
SAVEIFS=$IFS
IFS=$(echo -en "\n\b")
for i in $(ls ./test/);do
   echo "$i"
   sips -s format png ./test/"$i" \
        --out OutPutPNG/"${i%\.*}".png
done
IFS=$SAVEIFS
```

## Perspektivisch kippen ##
```bash
convert source.png -matte -virtual-pixel transparent \
-distort Perspective \
'0,0,0,0 0,262,0,262 240,0,240,37 240,262,240,237' target.png
```
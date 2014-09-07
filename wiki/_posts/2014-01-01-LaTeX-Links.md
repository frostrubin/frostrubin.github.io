---
title: LaTeX Links

layout: page
category: wiki
---

## Links ##

- [MacTeX - Komplette Distribution mit allem drum und dran](http://www.tug.org/mactex)
- [LaTeX bei Wikibooks](http://en.wikibooks.org/wiki/LaTeX/Introduction)
- [LaTeX-Online-Compiler](http://nirvana.informatik.uni-halle.de/~thuering/php/latex-online/latex.php?sprachauswahl=1)
- [LaTeX Lab](http://docs.latexlab.org)
- [Einführung in das "hyperref" Paket](http://www.math.uni-hamburg.de/home/iffland/Materialien/Einf_hyperref.pdf)
- [Stefan Macke's Diplomarbeit](http://blog.stefan-macke.com/2007/10/30/neue-version-meiner-latex-vorlage-fuer-die-diplomarbeit-an-der-fhwt)
- [Stefan Macke's Masterarbeit](http://blog.stefan-macke.com/2009/04/24/latex-vorlage-fuer-meine-masterarbeit-an-der-ohm-hochschule-nuernberg)

## LaTeX Terminal Kommandos ##

### Wörter zählen ###
```bash
detex /pub/Tex\ Works/Projektarbeit\ II/Projektarbeit\ II.tex | wc -w
```

### Abkürzungsverzeichnis ertellen ###
```bash
makeindex Projektarbeit\ II.nlo -s nomencl.ist -o Projektarbeit\ II.nls
```

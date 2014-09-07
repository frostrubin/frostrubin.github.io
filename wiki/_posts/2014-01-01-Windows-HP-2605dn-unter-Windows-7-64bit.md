---
title: Windows HP 2605dn unter Windows 7 64bit

layout: page
category: wiki
---

# Windows: HP 2605dn unter Windows 7 64bit

1. [HP Universal Treiber](http://h20000.www2.hp.com/bizsupport/TechSupport/SoftwareIndex.jsp?lang=en&cc=us&prodNameId=1140730&prodTypeId=18972&prodSeriesId=1140727&swLang=8&taskId=135&swEnvOID=4063) runterladen
2. HP Universal Print Driver for Windows PCL6 x64 installieren
3. Bei der Druckerinstallation auf "Windows Update" (innerhalb der Treibersuche) gehen - dort wird dann der Treiber für "2605/2605dn/2605dtn PS" gefunden. Die Installation scheitert jedoch.
4. Mit der Installation fortfahren - nun hat man den Universal Treiber installiert.
5. Unter `Start > Geräte und Drucker > HP Color LaserJet 2605dn > Drucker anpassen > Geräteeinstellungen` muss der Gerätetyp von _Automatische Erkennung_ auf _Farbe_ gestellt sowie Modul für beidseitigen Druck von _Nicht installiert_ auf _Installiert_ gestellt werden.
6. Um zu verhindern, dass `GET /DevMgmt/DiscoveryTree.xml HTTP/1.1` Seiten gedruckt werden, kann man nun noch unter `Start > Geräte und Drucker` einen Rechtsklick auf den Drucker machen. In den `Druckereigenschaften` im Reiter `Geräteeinstellungen` kann man nun unter `Installierbare Optionen > Druckerstatusbenachrichtigung` die Einstellung auf _Deaktiviert_ setzen.

# Quellen
[Thomas Lutz](http://www.thomaslutz.de/2009/12/18/windows-7-64bit-hp-color-laserjet-2605dn/)
[SolveYourTech - Youtube](http://www.youtube.com/watch?v=ibISyLRq3B8)


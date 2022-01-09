# CoverageMaps
Radio coverage maps

Die nachfolgende (Installations-)Anleitung wurde erstmalig unter:

https://community.swisscom.ch/t5/Mobile/2G-Abschaltung-2020-neue-weisse-Flecken-kein-Empfang/td-p/617747

veröffentlicht (ab Beitrag Nr. 15).


Installationsanleitung Signal-Server
================================================================================

Bei Bedarf nach einigermassen präzise und mit der Realität übereinstimmenden 
Netzabdeckungskarten sollten diese Netzabdeckungskarten selber mit dem 
kostenlosen Programm Signal-Server berechnet werden. Dazu benötigt man lediglich
die genauen Koordinaten der gewünschten Mobilfunkantenne im WGS84-Format.

https://de.wikipedia.org/wiki/World_Geodetic_System_1984

Diese Koordinaten im WGS84-Format erhält man auf der BAKOM-Funksenderkarte über 
das Kontextmenü (rechte Maustaste).

https://map.geo.admin.ch/?topic=funksender

https://de.wikipedia.org/wiki/Kontextmen%C3%BC


Allgemein
--------------------------------------------------------------------------------
Signal-Server ist:

- ein Programm zur Berechnung der Netzabdeckung eines Funksystems (coverage map).
- ein Programm zur Berechnung von Sichtverbindungen (P2P Analysis) und der 
  Fresnelzonen von Richtfunkverbindungen (Richtstrahl).


> Zwar sind bequeme Bedienoberflächen (GUI) für die Bedienung von Signal-Server
> erhältlich. Jedoch sind diese sehr mangelhaft. Deshalb kann der Einsatz einer 
> Bedienoberfläche (GUI) für Signal-Server aktuell NICHT empfohlen werden!

Diese Installationsanleitung wurde für die Linuxdistribution:

SUSE Linux Enterprise Desktop 15 SP3  (SLED)

geschrieben und getestet. 

Die Angabe des Tilde-Zeichen "~" entspricht dem Heimverzeichnis des
aktuellen Benutzers. Zum Beispiel:

`/home/foo`


Programmstart
--------------------------------------------------------------------------------
Erst nachdem die unten beschriebene Installation durchgeführt wurde, darf 
das Programm das erste Mal gestartet werden.

Falls keine Bedienoberfläche (GUI) für Signal-Server installiert ist, kann 
Signal-Server auf der Kommandozeile mit Rechenarbeit beauftragt werden. 

Dabei sind die Parameter von Signal-Server zu beachten. Informationen zu den 
Parametern von Signal-Server erhält man beim Programmaufruf ohne Parameter:

```
# cd /var/mail/Signal-Server/
# ./signalserver
```

Hier einige Beispiele vom Programmaufruf von Signal-Server:

- Netzabdeckungskarte berechnen (Normalauflösung: 90 Meter/3 Bogensekunden)

```
# cd /var/mail/Signal-Server/
# ./signalserver -dbg -sdf ~/DEM/vfp -m -dbm -erp 1.0 -rt -90 -f 446.2 -R 100 -pm 2 -lat 46.5466 -lon 7.0186 -txh 1.0 -rxh 1.0 -o /tmp/Test_Netzabdeckung
```

- Sichtverbindung kontrollieren und Fresnelzone berechnen (Normalauflösung: 90 Meter/3 Bogensekunden)

```
# cd /var/mail/Signal-Server/
# ./signalserver -ng -dbg -sdf ~/DEM/vfp -m -dbm -erp 1.0 -rt -90 -f 6000.0 -R 100 -pm 2 -lat 46.5466573 -lon 7.0186223 -txh 1.0 -rla 46.6043527 -rlo 7.3164414 -rxh 1.0 -o /tmp/Test_Richstrahl
```

- Netzabdeckungskarte berechnen (Hoch auflösend: 30 Meter/1 Bogensekunde)

```
# cd /var/mail/Signal-Server/
# ./signalserverHD -dbg -sdf ~/DEM/vfp -m -dbm -erp 1.0 -rt -90 -f 446.2 -R 100 -pm 2 -lat 46.5466 -lon 7.0186 -txh 1.0 -rxh 1.0 -o /tmp/Test_Netzabdeckung_HD
```

- Sichtverbindung kontrollieren und Fresnelzone berechnen (Hoch auflösend: 30 Meter/1 Bogensekunde)

```
# cd /var/mail/Signal-Server/
# ./signalserverHD -ng -dbg -sdf ~/DEM/vfp -m -dbm -erp 1.0 -rt -90 -f 6000.0 -R 100 -pm 2 -lat 46.5466573 -lon 7.0186223 -txh 1.0 -rla 46.6043527 -rlo 7.3164414 -rxh 1.0 -o /tmp/Test_Richstrahl_HD
```

> Beim Aufruf von Signal-Server ist zu beachten, dass beim Parameter "-o"
> die Angabe des Dateinamens genügend Zeichen enthält!

Folgende Funkausbreitungsmodellen (radio propagation models) 
sind tauglich für die Berechnung von Netzabdeckungskarten in ländlichen 
und unverbauten Regionen:

|Funkausbreitungsmodell|-pm|Berücksichtigt Freiraumdämpfung|Berücksichtigt Höhenmodell (DEM)|
|------------------------|-----|------------------|-------------------|
|ITM|1|Ja|Ja|
|LOS|2|Nein|Ja|
|FSPL|7|Ja|Nein|

Signal-Server gibt die Netzabdeckungskarte  als Grafikdatei im PPM-Format 
`(*.ppm)` aus. Mit Hilfe von ImageMagick kann die Grafikdatei ins PNG-Format 
`(*.png)` umgewandelt werden:

```
# convert /tmp/Test_Netzabdeckung.ppm -transparent white /tmp/Test_Netzabdeckung.png
```

Als nächster Schritt ist von Hand eine *.kmz-Datei zu erstellen.

In einem Texteditor die XML-Datei doc.kml erstellen. Beim Speichern der XML-
Datei darauf achten, dass die Zeichenkodierung UTF-8 verwendet wird. Mit oder
ohne BOM (Byte Order Mark) ist wahrscheinlich egal.

https://de.wikipedia.org/wiki/Extensible_Markup_Language

https://de.wikipedia.org/wiki/UTF-8

https://de.wikipedia.org/wiki/Byte_Order_Mark

Die XML-Datei doc.kml mit diesem Inhalt ausstatten:

```
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns:kml="http://www.opengis.net/kml/2.2" xmlns:atom="http://www.w3.org/2005/Atom">
<GroundOverlay>
    <name>Titel - Netzabdeckungskarte</name>
    <Icon>
        <href>Test_Netzabdeckung.png</href>
    </Icon>
    <LatLonBox>
        <north>49.0</north>
        <east>10.0</east>
        <south>45.0</south>
        <west>5.0</west>
    </LatLonBox>
</GroundOverlay>
</kml>
```

- Unter `<name>` den Titel der Netzabdeckungskarte angeben. 

- Unter `<href>` den Dateinamen der von Signal-Server erstellten Grafik mit der 
  Netzabdeckungskarte angeben. Nur Dateiformat PNG `(*.png)` zulässig!

- Unter `<LatLonBox>` müssen die korrekten Angaben der Positionen der beiden 
  äusseren Bildecken der von Signal-Server erstellten Grafik mit der 
  Netzabdeckungskarte eingetragen werden.

  Signal-Server gibt diese Positionen bei der Berechnung der Netzabdeckungskarte
  aus. Zum Beispiel:

```
|49.000000|10.000000|45.000000|5.000000|
```

  Bei fehlenden Debug-Parameter "-dbg" ist dies in der Regel die einzige 
  Ausgabe von Signal-Server beim Berechnen einer Netzabdeckungskarte. 
  Diese Ausgabe muss in die korrekte LatLonBox-Angabe umgemünzt werden. Zum 
  Beispiel:

```
    <LatLonBox>
        <north>49.0</north>
        <east>10.0</east>
        <south>45.0</south>
        <west>5.0</west>
    </LatLonBox>
```

Nach dem Erstellen und Editieren der XML-Datei doc.kml können die Dateien:

- XML-Datei doc.kml
- *.png-Datei mit der Netzabdeckungskarte

zu einem ZIP-Archiv `(*.zip)` gepackt werden. Diesem neu erstellten ZIP-Archiv 
muss die Dateiendung angepasst werden:

- Alt:  *.zip
- Neu:  *.kmz

Dieses ZIP-Archiv mit der Dateiendung `(*.kmz)` kann dann Google Earth als 
Projekt "gefüttert" werden. Siehe dazu das Kapitel "Google Earth".


Google Earth
--------------------------------------------------------------------------------
Die händisch erstellte *.kmz-Datei kann in Google Earth dargestellt werden.

https://de.wikipedia.org/wiki/Google_Earth

- Im Webbrowser Google Earth starten.
  https://earth.google.com/web/

- Unter "Projekte" den Eintrag "KML-Datei vom Computer importieren" wählen.

- Händisch erstellte *.kmz-Datei  hochladen.

- Die Deckkraft der Ebene "Plot: " anpassen. Im Hamburgermenü von dieser Ebene
  den Eintrag "Deckkraft des Merkmals ändern".

Für die Anpassung der Deckkraft siehe auch dieses Youtube-Video ab 13:00 min:
https://www.youtube.com/watch?v=MWYoX3-G3gM


OpenStreetMap (OSM) und OpenTopoMap
--------------------------------------------------------------------------------
Alternativ zu Google Earth kann die Darstellung mit Kartenmaterial von 
Open Street Map (OSM) im Webbrowser erfolgen. 

https://de.wikipedia.org/wiki/OpenStreetMap

Die in diesem Kapitel vorgestellte Lösung erlaubt die Darstellung mit diesem 
Kartenmaterial:

- OpenStreetMap (OSM)
https://www.openstreetmap.org/

- OpenTopoMap
https://opentopomap.org/

Vorgängig ist eine HTML-Datei mit der Dateiendung *.html im Texteditor zu 
erstellen. Beim Speichern der HTML-Datei darauf achten, dass die 
Zeichenkodierung UTF-8 verwendet wird. Mit oder ohne BOM (Byte Order Mark) ist 
wahrscheinlich egal.

https://de.wikipedia.org/wiki/Hypertext_Markup_Language

https://de.wikipedia.org/wiki/UTF-8

https://de.wikipedia.org/wiki/Byte_Order_Mark

Diese HTML-Datei ist im Texteditor mit folgenden Inhalt zu befüllen. Leerzeichen
am Zeilenanfang beachten!

=> Siehe Verzeichnis `OpenLayers/*.html`

Wird diese HTML-Datei in einem Webbrowser geöffnet, so wird die 
Netzabdeckungskarte auf der OpenStreetMap-Karte oder OpenTopoMap-Karte 
dargestellt. Dazu werden vom Webbrowser gemäss den Anweisungen in der HTML-Datei
zwei JavaScripts aus dem Internet heruntergeladen und für die Darstellung dieser
HTML-Datei eingesetzt:

- OpenLayers
https://de.wikipedia.org/wiki/OpenLayers

https://openlayers.org/

- ol-layerswitcher
https://medium.com/gis-tips/openlayers-3-adding-a-layer-switcher-9b63ae9e5253

https://github.com/walkermatt/ol-layerswitcher

Die Netzabdeckungskarte wird vom JavaScript OpenLayers aus der in der HTML-
Datei in der Zeile:

```
url: 'file:///tmp/coveragemap.png',
```

angegebene Grafikdatei `(*.png)` geladen. Im abgebildeten Beispiel ist dies die 
Grafikdatei unter:

```
/tmp/coveragemap.png
```

welche lokal auf dem Linux-Rechner abgelegt wurde (SSD oder Festplatte). Die 
Grafikdatei Coveragemap.png sollte die mit Signal-Server berechnete 
Netzabdeckungskarte enthalten.

Diese url-Zeile kann in der HTML-Datei den eigenen Wünschen entsprechend 
angepasst werden. Beim Einsatz des Webbrowsers Firefox sind die Hinweise unter:
https://kb.mozillazine.org/Links_to_local_pages_do_not_work
zu beachten.

Weiter muss in der HTML-Datei die Zeile:

```
const imageExtent = [6.0, 46.0, 8.0, 48.0];
```

angepasst werden. Diese Zeile muss die korrekten Angaben der Positionen der 
beiden äusseren Bildecken der von Signal-Server erstellten Grafik mit der
Netzabdeckungskarte enthalten. Siehe dazu die Angaben zur Erstellung der
*.kmz-Datei für Google Earth.

Die Darstellung der Netzabdeckungskarte erfolgt im Webbrowser mit der 
Kartenprojektion:

EPSG:3857  => WGS 84 Web Mercator

https://de.wikipedia.org/wiki/Kartennetzentwurf

https://en.wikipedia.org/wiki/Web_Mercator_projection

Bei grossflächigen Netzabdeckungskarten kann die Darstellung mit der 
Kartenprojektion "WGS 84 Web Mercator" fehlschlagen. In dem Fall muss die
HTML-Datei umkonfiguriert werden, damit der Webbrowser beim nächsten Versuch
die Kartenprojektion:

EPSG:4326 (WGS 84)

https://en.wikipedia.org/wiki/World_Geodetic_System

https://de.wikipedia.org/wiki/World_Geodetic_System_1984

https://de.wikipedia.org/wiki/World_Geodetic_System

verwendet. Die am Zeilenanfang mit dem Minuszeichen (-) gekennzeichneten Zeilen 
müssen durch die mit dem Pluszeichen (+) gekennzeichneten Zeilen ersetzt werden.
Auch hier wieder: Leerzeichen am Zeilenanfang beachten!

```
46c46
-       const transformCenter = ol.proj.transform(center, 'EPSG:4326', 'EPSG:3857');			// Transform coordinates from WGS 84 to Web Mercator (projection of view)
---
+       const transformCenter = ol.proj.transform(center, 'EPSG:4326', 'EPSG:4326');			// Transform coordinates from WGS 84 to WGS 84 (projection of view)
48c48
-       const viewImageExtent = ol.proj.transformExtent(imageExtent, 'EPSG:4326', 'EPSG:3857');	// Transform image corners coordinates from WGS 84 to Web Mercator (projection of view)
---
+       const viewImageExtent = ol.proj.transformExtent(imageExtent, 'EPSG:4326', 'EPSG:4326');	// Transform image corners coordinates from WGS 84 to WGS 84 (projection of view)
99c99
        view: new ol.View({
- 	  projection: 'EPSG:3857',			// Web Mercator
---
        view: new ol.View({
+ 	  projection: 'EPSG:4326',			// WGS 84
```

Den Unterschied zwischen der Kartenprojektion EPSG:3857 und EPGS:4326 ist sehr
gut anhand der Darstellung von Grönland ersichtlich, wenn am Bildschirm ganz
Europa sichtbar ist.

Zur Fehlersuche sollte im Webbrowser Firefox die Taste `<F12>` betätigt werden. 
Danach die HTML-Datei neu laden. Interessante Angaben für die Fehlersuche
enthält das Register "Konsole" und "Netzwerkanalyse" im Werkzeugkasten: 

```
Werkzeuge für Webentwickler
```


Installation Signal-Server
--------------------------------------------------------------------------------
Signal-Server berechnet die Netzabdeckungskarten und die Sichtverbindungen (P2P)
und deren Fresnelzonen.

https://github.com/Cloud-RF/Signal-Server

https://de.wikipedia.org/wiki/Fresnelzone

Signal-Server wurde in der Programmiersprache C/C++ geschrieben. 

Getester Git-Stand:  `8dc0a43  vom 13.07.2021`

Anforderungen:
- Linux
- ImageMagick
- C/C++-Compiler: GCC,G++ oder clang

```
# git clone https://github.com/Cloud-RF/Signal-Server /var/mail/Signal-Server/
# cd /var/mail/Signal-Server/
# cd src
# make 
# make install
# make clean
```

Ein wenig Git-Pflege betreiben:

```
# touch /var/mail/Signal-Server/.gitignore
# vim /var/mail/Signal-Server/.gitignore

signalserver
signalserverHD
signalserverLIDAR
utils/sdf/usgs2sdf/srtm2sdf
utils/sdf/usgs2sdf/srtm2sdf-hd

# cd /var/mail/Signal-Server/
# git status
# git diff
```


Installation Höhenmodelle 
--------------------------------------------------------------------------------
Nach der Installation von Signal-Server können die Daten für das Höhenmodell
heruntergeladen werden. Benötigt wird ein "Digitales Höhenmodell" (DEM):

https://de.wikipedia.org/wiki/Digitales_H%C3%B6henmodell

Generell sind für die Schweiz folgende Höhenmodelle zu beachten:
https://info.skitourenguru.ch/index.php/algorithmus/31-dem

Randbemerkung:	Der Einsatz von Skitourenguru ist allen WintersportlerInnen
abseits der markierten Pisten zu empfehlen:

https://www.heise.de/hintergrund/Lawinenrisiko-Algorithmus-Das-steckt-hinter-der-Web-App-Skitourenguru-6029169.html?seite=all

Signal-Server unterstützt für das Höhenmodell mehrere verschiedene Datenformate.
Mehr Informationen zu den von Signal-Server unterstützten Datenformate bietet
die README.md-Datei von Signal-Server:

https://github.com/Cloud-RF/Signal-Server#readme

Höhenmodelle für den weltweiten Einsatz mit normaler Auflösung (90 Meter; 
3 Bogensekunden) können von der Webseite von ViewFinderPanorma (VFP) im 
HGT-Format `(*.hgt)` heruntergeladen werden. Gewünschte Kachel auf der Weltkarte 
auswählen und herunterladen.

http://viewfinderpanoramas.org/Coverage%20map%20viewfinderpanoramas_org3.htm

Diese HGT-Dateien im Verzeichnis:

```
~/DEM/vfp
```

ablegen.

Die im Lieferumfang von Signal-Server enthaltene Konverterprogramme für die 
Höhenmodelle kompilieren:

```
# cd /var/mail/Signal-Server/utils/sdf/usgs2sdf
# ./build srtm2sdf
```

Bash-Skript anpassen (Leerschläge am Zeilenanfang beachten!):

```
# vim /var/mail/Signal-Server/utils/sdf/convert_sdf.sh
```

```
#!/bin/bash
for file in *.hgt
 do
 /var/mail/Signal-Server/utils/sdf/usgs2sdf/srtm2sdf -d /dev/null $file
 done
```

Höhenmodelle im HGT-Format `(*.hgt)` ins SDF-Format `(*.sdf)` umwandeln:

```
# cd ~/DEM/vfp
# /var/mail/Signal-Server/utils/sdf/convert_sdf.sh 
```

Nach der Konvertierung müssen die Höhenmodelle diese Schreibweise für den 
Dateinamen zwingend einhalten:

- Normale Auflösung für "signalserver":  `ba:be:la:le.sdf`
- Hohe Auflösung für "signalserverHD":   `ba:be:la:le-hd.sdf`

```
ba  Anfang Breitengrad (zum Beispiel:  46° N -> 46)
be  Ende Breitengrad   (zum Beispiel:  47° N -> 47)

la  Anfang Längengrad  (zum Beispiel:   7° E -> 353)
lb  Ende Längengrad    (zum Beispiel:   8° E -> 352)
```

Installation von Signal-Server und die Höhenmodelle testen mit einigen Aufrufen 
ohne Bedienoberfläche (GUI). Siehe dazu Kapitel "Programmstart".



Installation von hochauflösenden Höhenmodelle
--------------------------------------------------------------------------------
Auf der Webseite von ViewFinderPanorma (VFP) sind für einige Regionen auch 
Höhenmodelle in höherer Auflösung (30 Meter; 1 Bogensekunde) erhältlich. Diese 
hochauflösenden Höhenmodelle benötigen eine spezielle Behandlung. 

Diese hochauflösenden Höhenmodelle werden im weiteren "HD-Höhenmodelle" genannt. 

HD-Höhenmodell von der Webseite von ViewFinderPanorma (VFP) im HGT-Format 
`(*.hgt)` heruntergeladen. Gewünschte Kachel auf der Weltkarte auswählen und
herunterladen.

http://viewfinderpanoramas.org/Coverage%20map%20viewfinderpanoramas_org1.htm

oder alternativ von der Webseite:

http://viewfinderpanoramas.org/dem3.html

herunterladen.

Diese HGT-Dateien im Verzeichnis:

```
~/DEM/vfp
```

ablegen.

Bash-Skript anpassen (Leerschläge am Zeilenanfang beachten!):

```
# vim /var/mail/Signal-Server/utils/sdf/convert_sdf_hd.sh
```

```
#!/bin/bash
for file in *.hgt
 do
 /var/mail/Signal-Server/utils/sdf/usgs2sdf/srtm2sdf-hd -d /dev/null $file
 done
```

Höhenmodelle im HGT-Format `(*.hgt)` ins SDF-Format `(*-hd.sdf)` umwandeln:

```
# cd ~/DEM/vfp
# /var/mail/Signal-Server/utils/sdf/convert_sdf_hd.sh 
```


Deinstallation
--------------------------------------------------------------------------------
Signal-Server deinstallieren:

```
# rm -R /var/mail/Signal-Server/
```

ImageMagick deinstallieren.


**Installationsanleitung OsmoCOM - Ubuntu Server 20.04**
---

# Einleitung
Diese Anleitung beschreibt die Installation und Konfiguration von OsmoCOM für den Betrieb eines eigenen Mobilfunknetzwerks (Campus-Netzwerk). 

Wichtig:

    Achtung:

    Leider ist diese Anleitung unvollständig! 
    
    Fehlende Anweisungen sind an der Kennzeichnung
    "TODO" ersichtlich!

OsmoCOM ist eine Sammlung kostenloser, Open Source-Software zum Betreiben eines eigenen Mobilfunknetzwerks.   
- https://osmocom.org/     
- https://osmocom.org/projects/cellular-infrastructure    
- https://osmocom.org/projects/cellular-infrastructure/wiki    
- https://osmocom.org/projects/cellular-infrastructure/wiki/Osmocom_Network_In_The_Box    

OsmoCOM ermöglicht den Betrieb eines eigenen 2G/GSM-Mobilfunknetzwerks auf einem Raspberry Pi 4 mit einem LimeSDR Mini. Der Raspberry Pi 4 kann mit einer USB-Powerbank ab "Batterie" betrieben werden.

Diese Anleitung gilt für Ubuntu Server 20.04.

Eine gute Übersicht über die Struktur eines 2G/GSM-Mobilfunknetzwerks bietet:   
- https://en.wikipedia.org/wiki/GSM#/media/File:Gsm_structures.svg    
- https://de.wikipedia.org/wiki/Global_System_for_Mobile_Communications     

Kommerzieller Support und Hardware zu OsmoCOM ist von sysmocom erhältlich:    
https://www.sysmocom.de/    

## Lesehinweise
\#  Befehlseingabe im Terminal (Bash) mit normalen Benutzerrechten (Benutzer: foo). Keine root-/Administratorrechte!

\$  Befehlseingabe im Terminal (Bash) als Benutzer root. Mit Administratorrechten!

£  Fernzugriff mit Telnet und normalen Benutzerrechten (Benutzer: foo). Keine root-/Administratorrechte!
    
... Platzhalter für diverse Angaben

:=  Zuweisung des Werts von der rechten Texthälfte an die Variable in der linken Texthälfte.
		
z.B. 	zum Beispiel

## Referenzen
Diese Anleitung basiert auf den Angaben von folgenden Webseiten:

- 9-teilige Anleitung "GSM with Osmocom" mit LimeSDR   
https://nickvsnetworking.com/gsm-with-osmocom-part-1-intro/

# Hardware

## Anforderungen an den SDR
Für den Betrieb eines 2G/GSM-Mobilfunknetzwerks mit einem SDR muss die eingesetzte SDR-Hardware das gleichzeitige Senden und Empfangen von Funksignalen (Full Duplex) unterstützten. Weiter muss die SDR-Hardware den Funkbetrieb mit Zeitstempeln (timestamping) unterstützen. 

Aktuell die billigste SDR-Hardware, welche den Funkbetrieb mit "Full Duplex" und "Timestamping" unterstützt, ist der LimeSDR Mini. 

Die von OsmoCOM unterstützte SDR-Hardware ist hier aufgelistet:    
https://osmocom.org/projects/osmotrx/wiki/OsmoTRX#RF-Hardware-support   

## Anforderungen an den Rechner
Je mehr Rechenleistung der eingesetzte Rechner aufweist, desto mehr GSM-Funkkanäle kann der SDR für den Betrieb des eigenen 2G/GSM-Mobilfunknetzwerks einsetzen.

Leider ist die Rechenleistung vom Raspberry Pi 4 sehr limitiert. Deshalb gilt zu beachten:

    Achtung:
    
    Gemäss den Angaben unter:
    https://osmocom.org/projects/osmotrx/wiki/OsmoTRX#Embedded-Platform-Support
    
    ist das mit dem Raspberry Pi 4 betriebene, eigene 2G/GSM-Mobilfunknetzwerk
    auf zwei GSM-Funkkanäle beschränkt!

Auch die Anforderungen an die Speichergrösse (RAM) sind zu beachten:

    Achtung:

    Der Rechner muss über mindestens 4 GB RAM verfügen!

## Anforderungen an die Mobilfunkantenne(n)
Als Mobilfunkantennen reichen im Testbetrieb die im Lieferumfang des LimeSDR-Mini enthaltenen zwei "duck antennas". 

Beim gleichzeitigen Betrieb von zweien Mobilfunkantennen, ist darauf zu achten, dass die Ausrichtung der Sendeantenne in einem Winkel von 90° verschoben zur Ausrichtung der Empfangsantenne erfolgt. Damit wird eine Kreuzpolarisation erzeugt, welche die Störeinflüsse der Sendeantenne auf die Empfangsantenne minimiert.

Für den Dauerbetrieb sollte eine einzige, professionelle Mobilfunkantenne verwendet werden. Mobilfunkantennen für den professionellen Einsatz sind zum Beispiel beim Lieferanten FTH Henning:  
https://www.fts-hennig.de/Antennen/  
erhältlich.

## Langzeitbetrieb
Soll die SDR-Hardware im Langzeitbetrieb eingesetzt werden, müssen weitere Hardwarevoraussetzungen erfüllt werden. Unter Langzeitbetrieb wird der unterbruchsfreie Einsatz über eine Zeitdauer von mehreren Tagen (24h/7 Tage-Betrieb) verstanden.

Die SDR-Hardware ist mit einer gut funktionierenden Kühlung auszustatten. Entweder wird aktiv gekühlt mit einem Lüfter. Oder es kommt eine rein passive Kühlung zum Einsatz. Bei Lüftern geht früher oder später das Kugellager kaputt, oder die Lüftungsschlitzen verstopfen, deshalb ist eine rein passive Kühlung langlebiger, zuverlässiger und schont die Ohren. 

Eine ordentliche, rein passive Kühlung von SDR-Hardware verfügt über ein Alugehäuse, welches über ein Wärmeleitpad die Wärme von den heisslaufenden Chips abtransportiert. Bei rein passiver Kühlung wird durch Konvektion die kalte Luft von unten angesogen, am Gehäuse erwärmt und steigt als warme Luft nach oben.  
https://de.wikipedia.org/wiki/W%C3%A4rmeleitpad

USB-Verbindungen sind nicht für den Langzeitbetrieb geeignet. Deshalb sollte zwischen der Rechnerhardware (z.B. Raspberry Pi) und der SDR-Hardware eine langzeitstabile Kommunikationsverbindung realisiert werden. 

Spezialrechnerhardware bieten Bussysteme an, über welche die Kommunikation zwischen SDR und Rechner langzeitstabil abgewickelt werden kann. Soll kein Spezialrechner eingesetzt werden, muss mit Gigabit-Ethernet-Netzwerkkabel eine langzeitstabile Netzwerkverbindung zwischen SDR und Rechner realisiert werden.

Der Einsatz eines Ethernet-Netzwerkkabels hat den Vorteil, dass für die Rechnerhardware kostengünstige COTS-Hardware eingesetzt werden kann. Und die Rechnerhardware relativ leicht durch eine neuere, leistungsfähigere Rechnerhardware ersetzt werden kann.

Für den Betrieb an einem SDR geeignete Spezialrechnerhardware ist vom Hersteller Ettus als USRP E313 erhältlich:  
- https://www.ettus.com/product-categories/usrp-embedded-series/  
- https://www.ettus.com/wp-content/uploads/2019/01/USRP_E313_Datasheet-1.pdf

Auch der Hersteller Fairwaves hat geeignete Spezialhardware als UmTRX oder UmSITE im Angebot:   
- https://osmocom.org/projects/osmotrx/wiki/Fairwaves_UmTRX_UmSITE    
- https://fairwaves.co/products/equipment/    
- https://umtrx.org/products/
- http://umtrx.org/wp/wp-content/uploads/2016/06/UmTRX-2.3.1-datasheet-9-Jun-2016.pdf   
- https://fairwaves.co/equipment/umsite-tm3/    
- https://fairwaves.co/equipment/umsite-tm5/   

In der Auflistung der Spezialhardware darf auch der LimeNET-micro nicht fehlen:   
- https://osmocom.org/projects/osmotrx/wiki/LimeSDR_Family#LimeNet-Micro    
- https://osmocom.org/projects/osmotrx/wiki/LimeNET-micro

## Anforderungen an die Frequenz- und Phasensynchronisation der SDR-Hardware
Mobilfunknetzwerke stellen hohe Anforderungen an die Frequenz- und Phasensynchronisation des im Downlink ausgestrahlten Funksignals. Üblich sind zeitliche Genauigkeiten unter 10 μs. 

Um diese Anforderungen an die Synchronisation des ausgestrahlten Funksignals zu erfüllen, muss der SDR mit einer sehr genauen Zeitbasis, wie einer Atomuhr synchronisiert sein. 

Fehlt die Takt- und Frequenzsynchronisation vom SDR zu einer Atomuhr, führt dies zu massiven Performanceeinbussen und Verbindungsverluste. Diese Performanceeinbussen sind in Form von erheblich reduzierten erreichbaren Datenübertragungsraten im eigenen Mobilfunknetzwerk spürbar und messbar.

Hochwertige SDR-Hardware verfügt über einen Anschluss zum Einspeisen eines externen Taktsignals über eine mechanisch robuste N- oder SMA-Steckverbindung. Dieser Anschluss wird häufig mit "EXT CLOCK" bezeichnet.

Leider ist der LimeSDR-Mini kein hochwertiger SDR. Der LimeSDR-Mini verfügt zwar über einen mit einer mechanisch ungenügend robusten U.FL-Steckverbindung ausgerüsteten "EXT CLOCK"-Anschluss auf der Leiterplatte. Aber zur Nutzung von diesem "EXT CLOCK"-Anschluss müssen auf der Leiterplatte vom LimeSDR-Mini einige Nullohmwiderstände umgelöstet werden!  
- https://wiki.myriadrf.org/LimeSDR-Mini_v1.1_hardware_description  
- https://discourse.myriadrf.org/t/enabling-external-clock-on-limesdr-mini-1-2/3066

Die Synchronisation des SDR erfolgt in der Regel über einen GPSDO. Die im Aussenbereich montierte GNSS-Empfangsantenne leitet das vom GNSS-Satelliten empfangene, hochgenaue Atomuhr-Zeitsignal über ein langes Antennenkabel zum im Innenbereich montierten GPSDO.

Der GPSDO wandelt das GPS-Signal in ein SDR-kompatibles Taktsignal um. Einige SDR-Hardware erwartet ein externes Taktsignal als Rechtecksignal mit einer Taktfrequenz von 10 MHz. Andere SDR-Hardware erwartet ein externes Taktsignal als Rechtecksignal mit einer deutlich höheren Taktfrequenz. Vom GPSDO wird das externe Taktsignal über ein Antennenkabel über den "EXT CLOCK"-Anschluss dem SDR zugeführt.

In der Regel wird im GPSDO mit dem Zeitsignal vom GNSS-Satelliten ein lokaler Quarzoszillator synchronisiert. Dieses Synchronisationsverfahren wird GPSDO genannt. Beim Ausfall des GNSS-Zeitsignals läuft dieser Quarzoszillator frei und ist alleinig für die Frequenz- und Phasensynchronisation des Funksenders zuständig.

Ob der Quarzoszillator als temperaturstabilisierter Quarzoszillator (TCXO), mit einem Quarzofen (OCXO) oder als Rubidium-Oszillator realisiert wird, hängt von den Anforderungen ab, wie lange das Mobilfunknetzwerk ohne externe Synchronisation betrieben werden soll. Rubidium-Oszillatoren sind langfristig stabiler und genauer als Quarzoszillatoren.

Eine nette Übersicht über die Genauigkeit der verschiedenen Bauarten von lokalen Quarzoszillatoren bietet der Hersteller Meinberg in diesem PDF:    
https://www.meinberg.de/download/docs/other/oscillator-options-de.pdf

Der Hersteller Leo Bodnar bietet einige SDR-kompatible GPSDO an:  
https://www.leobodnar.com/shop/index.php?main_page=index&cPath=107

Einige vom Hersteller Leo Bodnar angebotene SDR-kompatible GPSDO können über ein USB-Kabel von einer USB-Powerbank mit Strom versorgt werden. 

Dieses informative Youtube-Video zeigt auf, wie ein PlutoSDR mit dem GPSDO "Mini Precision GPS Reference Clock" vom Hersteller Leo Bodnar über GPS-Satellitenmit dem Zeitsignal einer Atomuhr synchronisiert wird:  
https://www.youtube.com/watch?v=o2IeXpsOQig

Auch für andere SDR-Hardware ist dieses Youtube-Video informativ. In diesem Video wird die Konfiguration der Frequenz des vom GPSDO ausgegebenen Taktsignal aufgezeigt.

## Funklizenz und funkregulatorische Anforderungen
Zum Betrieb eines eigenen Mobilfunknetzwerk ist eine entsprechende Funklizenz von der nationalen Funkaufsichtbehörde erforderlich. In der Schweiz ist dafür das BAKOM zuständig. 

Eine Übersicht über alle in der Schweiz für Mobilfunknetzwerke nutzbaren Mobilfunkfrequenzbändern bietet die Webseite:  
https://de.wikipedia.org/wiki/Mobilfunkfrequenzen_in_der_Schweiz

Ein modernes, handelsübliche Mobiltelefon unterstützt nicht alle 2G/GSM-Mobilfunkfrequenzbänder.

    Achtung:

    Ein modernes, handelsübliche Mobiltelefon unterstützt  
    nur diese 2G/GSM-Mobilfunkfrequenzbänder (Quadband):

     900 MHz  Band 8  FDD	E-GSM		
    1800 MHz  Band 3  FDD   DCS-1800	

     850 MHz  Band 5  FDD   GSM-850		
    1900 MHz  Band 2  FDD	PCS-1900

    Ältere Mobiltelefone unterstützen nicht alle oben
    aufgeführte 2G/GSM-Mobilfunkbänder! 	

Ein Mobiltelefon, welche alle vier oben aufgeführten 2G/GSM-Mobilfunkbänder unterstützt, wird als Quadband-tauglich bezeichnet.    
https://de.wikipedia.org/wiki/Quadband

Ein Mobiltelefon, welches nicht die vier oben aufgeführten 2G/GSM-Mobilfunkfrequenzbänder unterstützt, wird abhängig von der Anzahl der unterstützten 2G/GSM-Mobilfunkbänder wie folgt eingestuft:

- Singleband-tauglich (nur 1 2G/GSM-Mobilfunkband unterstützt)
- Dualband-tauglich (Unterstützung für 2 2G/GSM-Mobilfunkbänder)
- Triband-tauglich (Unterstützung für 3 2G/GSM-Mobilfunkbänder)

Ob ein Mobiltelefon Quadband-tauglich ist, kann mit der Suchfunktion von der Webseite:  
https://www.gsmarena.com/

herausgefunden werden. Einfach die genaue Modellbezeichnung des Mobiltelefons eingeben und im Suchresultat in der Spalte "Network" über den Text "EXPAND" die Zeile mit den Netzwerkangaben aufklappen und darin die vom Mobiltelefon unterstützten 2G/GSM-Mobilfunkfrequenzbänder entnehmen. Zum Beispiel für ein Quadband-taugliches Mobiltelefon:

    2G bands:   GSM 850 / 900 / 1800 / 1900

Hier eine Übersicht über alle 2G/GSM-Mobilfunkfrequenzbändern weltweit:   
- https://en.wikipedia.org/wiki/GSM_frequency_bands       
- https://www.sqimway.com/gsm_band.php     

Für den digitalen Zugfunk wird in Europa unter der Bezeichnung "GSM-R" ein modifiziertes 2G/GSM-Mobilfunknetzwerk betrieben. GSM-R funkt in einem eigenen, Spezial-Mobilfunkfrequenzband. Dieses GSM-R-Mobilfunkfrequenzband kann nur mit Spezialhardware verwendet werden!   
https://de.wikipedia.org/wiki/Zugfunk   
https://de.wikipedia.org/wiki/GSM-R   

Mit der Funklizenz verknüpft sind funkregulatorische Anforderungen. Insbesondere darf der Funksender keine anderen Funkanwendungen stören. Deshalb ist für jeden eingesetzten SDR im Sendepfad zwischen dem Leistungsverstärker (Power Amplifier -> kurz: PA) und der Mobilfunkantennne ein sehr gut filternder Bandpass zwingend erforderlich. Die funkregulatorischen Anforderungen definieren die genauen Anforderungen an diesen Bandpass.  
https://de.wikipedia.org/wiki/Bandpass

Da die SDR-Hardware über ihre Sendeeinheit(en) üblicherweise nur ein sehr schwaches Funksignal im ein- oder zweistelligen Milliwatt-Bereich liefert, muss dieses (Mobil-)Funksignal mit einem Leistungsverstärker (PA) verstärkt werden.    
- https://limemicro.com/app/uploads/2015/08/LMS7002M_Measurements-v1_05.pdf    
- https://wiki.batc.org.uk/LimeSDR_Mini_Output_Power_Levels    

Wie stark das Mobilfunksignal mit dem Leistungsverstärker (PA) verstärkt werden darf, ist der Funklizenz zu entnehmen.

Im Funksender muss der Sende- und Empfangspfad mit einem Diplexer oder Duplexer zwischen Mobilfunkantenne und SDR getrennt werden. Jeder SDR verfügt über einen Eingang für das empfangene Funksignal und über einen Ausgang für das zu versendende Funksignal. 

In einigen wenigen SDR erfolgt die Trennung von Sende- und Empfangspfad erst auf der Leiterplatine des SDR.  
https://de.wikipedia.org/wiki/Duplex_(Nachrichtentechnik)

Teurere SDR-Hardware kann gleichzeitig über mehrere Eingänge und Ausgänge Funksignale empfangen und senden. Eine solcher SDR ist für den MIMO-Betrieb erforderlich.  
https://de.wikipedia.org/wiki/MIMO_(Nachrichtentechnik)

Ein Überblick über die einzelnen Kompoonenten einer mit SDR-Hardware realisierten Mobilfunkantenne bietet Seite 7 von diesem Foliensatz:  
https://osmocom.org/attachments/download/2616/SDR%20based%20BTS.pdf

Wenn Frequenzduplex (FDD) eingesetzt wird, erfolgt die Trennung von Sende- und Empfangspfad mit einem Diplexer. Beim Einsatz eines Diplexer wird der aus funkregulatorischen Anforderungen erforderliche Bandpass üblicherweise im Diplexer realisiert.  
https://de.wikipedia.org/wiki/Diplexer

Hinweis:  Im englischen Sprachraum ist keine Unterscheidung zwischen Diplexer und Duplexer üblich. Beide werden schlicht: "Duplexer" genannt.

Diplexer sind zum Beispiel beim deutschen Hersteller Sysmocom erhältlich.  
https://shop.sysmocom.de/RF/Duplexers/

Einige leicht verständliche Fotos und Abbildungen zum Einsatz eines Diplexers bietet:    
https://librecellular.org/user/hardware

Diplexer werden üblicherweise "massgeschneidert" für jede Mobilfunkantenne gefertigt. Damit der Hersteller den für diese Mobilfunkantenne bestellten 
Diplexer auf diese Mobilfunkantenne massgeschneidert liefern kann, muss der Besteller bei der Diplexer-Bestellung die vier gewünschten Grenzfrequenzen 
angeben:

- Untere und obere Grenzfrequenz für den Downlink (Sendepfad)
- Untere und obere Grenzfrequenz für den Uplink (Empfangspfad)

Der Besteller entnimmt die Angaben der vier Grenzfrequenzen seiner Funklizenz.  
https://de.wikipedia.org/wiki/Grenzfrequenz

## SIM-Karte
In eigenen Mobilfunknetzwerken können Mobilgeräte (UE) eingesetzt werden, die mit einer fremden SIM-Karte bestückt sind.

Mit einer Spezialkonfiguration erlaubt das eigene 2G/GSM-Kernnetzwerk das Einbuchen mit SIM-Karten, deren Daten nicht vorgängig in der SIM-Karten-Datenbank des Kernnetzwerks hinterlegt wurden. Im weiteren wird die SIM-Karten-Datenbank Teilnehmerdatenbank des Kernnetzwerks (HLR) genannt. 

Wichtigster Inhalt der Teilnehmerdatenbank (HLR) ist das symmetrische Schlüsselmaterial (Master key => K respektive Ki). Siehe dazu diesen Artikel:   
https://nickvsnetworking.com/hss-usim-authentication-in-lte-nr-4g-5g/

Zum Hinterlegen der SIM-Kartendaten (ohne Spezialkonfiguration) sind selber ausgestellte SIM-Karten erforderlich. Für diesen Einsatzzweck geeignete SIM-Karten sind zum Beispiel bei Sysmocom erhältlich:

- https://www.sysmocom.de/products/sim/
- https://librecellular.org/user/subscribers

Eine SIM-Karte, welche nicht in der Teilnehmerdatenbank (HLR) eingetragen ist, kann sich (ohne Spezialkonfiguration) nicht in dieses Kernnetzwerk einbuchen. Einzige und wichtige Ausnahme: Notrufe (Europa: 112 / Nordamerika: 911) können auch ohne SIM-Karte oder mit fehlendem Eintrag in der Teilnehmerdatenbank (HSS) abgesetzt werden.    
https://de.wikipedia.org/wiki/Notruf#Sprachnotruf_per_Telefon

Die Konfigurationskniffe, welche das Einbuchen von fremden SIM-Karten in das eigene 2G/GSM-Mobilfunknetzwerk erlauben, sind ausführlich im Kapitel 6.3 - "Configuring the Subscribers Create on Demand Feature" der OsmoHLR-Bedienungsanleitung beschrieben:
- http://ftp.osmocom.org/docs/latest/osmohlr-usermanual.pdf
- https://nickvsnetworking.com/gsm-with-osmocom-part-7-the-hlr-home-location-register-and-friends/

Erst ab der dritten Generation des Mobilfunkstandards (3G/UMTS) wird die gegenseitige Authentifizierung (mutual authentication) unterstützt. Die gegenseitige Authentifizierung mit symmetrischem Schlüsselmaterial erfolgt im 3G/UMTS-Mobilfunknetzwerk über das Verfahren AKA. Auch alle neueren, im Produktiveinsatz befindlichen Generationen des Mobilfunkstandards (4G/LTE + 5G) verwenden das Verfahren AKA (in einer verbesserten Form). 
- https://www.umtsworld.com/technology/security.htm
- https://nickvsnetworking.com/hss-usim-authentication-in-lte-nr-4g-5g/
- https://en.wikipedia.org/wiki/Authentication_and_Key_Agreement#AKA_in_UMTS
- https://www.ericsson.com/en/blog/2021/9/authentication-and-key-agreements

# Software 

##  Hinweise zur IT-Security
    Sicherheitswarnung:
     
    Für die zu installierenden Softwarepakete:
    
    liblimesuite20.01-1
    limesuite
    libsoapysdr0.7
    python3-soapysdr
    libwxgtk3.0-gtk3-0v5
    libwxbase3.0-0v5
    libglew2.1
    libfltk1.3
    limesuite-udev
    
    libmbedcrypto3
    libmbedx509-0
    libmdedtls12
    
    libbctoolbox1
    libortp13
    libosmocodec0
    libosmocoding0
    libosmotrau2
        
    libosmocore11
    libosmovty4
    libosmo-mgcp-client3
    libosmogsm10
    libosmonetif6
    libosmo-sigtran0
    libosmoabis6
    libosmoctrl0
    
    osmo-libasn1c1
    libosmo-ranap2
    libsmpp34-1

    osmo-bsc
    osmo-bts
    osmo-hlr
    osmo-msc 
    osmo-stp
    osmo-mgw

    libosmogb6
    libosmosim0
    libosmocore-dev
    liblimesuite-dev
        
    gewährleistet der Hersteller Canonical keine Sicherheitsupdates!

Für diese Softwarepakete muss regelmässig (1x pro Woche) auf der Webseite:  
https://ubuntu.com/security/cves  
kontrolliert werden, welche bekannte Sicherheitslücken diese Softwarepakete enthalten. Allenfalls müssen diese Softwarepakete aus Sicherheitsgründen deinstalliert werden!

    Sicherheitswarnung:
    
    Für die aus dem Quellcode übersetzte Software
    
    OsmoTRX
    
    stellt der Hersteller Canonical keine Sicherheitsupdates zur Verfügung!

Für diese Software muss regelmässig (1x pro Woche) kontrolliert werden, ob diese Software bekannte Sicherheitslücken enthält. Wenn ja, muss die von der Sicherheitslücke betroffene Software aus Sicherheitsgründen ausser Betrieb genommen werden oder aktualisiert werden!

Zahlreiche Programme von OsmoCOM horchen auf einigen Serverports und erlauben eingehende Netzwerkverbindungen. Die Programme von OsmoCOM horchen auf diesen Serverports um (Fern-)diagnose und Fernwartung zu ermöglichen. Dieser Fernzugriff erfolgt über eine unverschlüsselte Netzwerkverbindung. Aus Sicherheitsgründen sind nicht benötigte Serverports zu schliessen oder deren Zugriff mit einem Passwort zu schützen.

Der Zugriff auf alle Serverports der OsmoCOM-Programme ist mit möglichst strikten Firewallregeln einzugrenzen.

## Umkonfiguraton des Betriebssystems
Hinweise, Tricks und Tipps zum Einsatz eines Raspberry Pi 4 bieten die Beiträge unter:  
https://www.lancom-forum.de/fragen-zur-lancom-systems-routern-und-gateways-f41/vdsl-umzug-glasfaser-neuer-router-t17926.html#p101750

### Softwarepaketverwaltung 
Die Softwarepaketverwaltung mit APT so konfigurieren, dass keine empfohlenen (recommends) und vorgeschlagene (suggests) Softwarepakete bei der Installation von Softwarepaketen mit dem Befehl "apt install <Softwarepaket> mitinstalliert werden. Dazu muss die Konfigurationsdatei /etc/apt/apt.conf.d/01custom angepasst werden.

Vorgängig den Texteditor NANO installieren:

    $ apt update
    $ apt install nano

Dann die Konfigurationsdatei /etc/apt/apt.conf.d/01custom anpassen:

    $ nano /etc/apt/apt.conf.d/01custom

    APT
    {
      // Empfohlene und vorgeschlagene Pakete sollen von APT nicht installiert werden
      Install-Recommends "false";
      Install-Suggests "false";

      // Metapackages sind von dieser Regelung ausgenommen
      Install-Recommends-Section { "metapackages"; "universe/metapackages"; };
    };

Komplette Softwarepakete-Installation aktualisieren:

    $ apt update
    $ apt upgrade
    $ apt full-upgrade
    $ apt autoremove --purge
    $ reboot

Einige für die Konfigurationsarbeiten verwendete Werkzeuge müssen installiert 
werden:

    $ apt install diff
    $ apt install iproute2
    $ apt install iptables
    $ apt install util-linux
    $ apt install coreutils
    $ apt install grep
    $ apt install conntrack
    $ apt install telnet

Anzahl der nicht mit Sicherheitsupdates versorgten Softwarepakete feststellen:

    $ pro security-status
    548 packages installed:
      539 packages from Ubuntu Main/Restricted repository
      9 packages from Ubuntu Universe/Multiverse repository
	
Nicht mit Sicherheitsupdates versorgte Softwarepakete entstammen dem Paketdepot (Repository) Universe oder Multiverse!

### Drosselung der CPU-Taktfrequenz
Damit OsmoCOM korrekt funktioniert, muss die automatische Drosselung der CPU-Taktfrequenz ausgeschaltet werden. Dadurch steigt der Stromverbrauch des Raspberry Pi massiv an!

Programm cpupower installieren:

    $ apt install linux-tools-raspi

Systemd-Dienst zur automatischen Drosselung der CPU-Taktfrequenz ausschalten:

    $ systemctl disable ondemand.service
    $ systemctl mask ondemand.service

Rechner neustarten:

    $ reboot

Aktuelle CPU-Taktfrequenz auslesen:

    $ cpupower frequency-info
      current policy: frequency should be within 600 MHz and 1.50 GHz.
                      The governor "performance" may decide which speed to use
                      within this range.
      current CPU frequency: 1.50 GHz (asserted by call to hardware)

### Hardware-Watchdog
Als nächster muss der Hardware-Watchdog aktiviert werden. Der Hardware-Watchdog ist eine Hardwareschaltung, welche den Rechner neustartet, wenn der Hardware-Watchdog nicht regelmässig eine Meldung von einem Software-Watchdog erhält. 

Der Software-Watchdog ist eine auf dem Rechner laufende Software, welche sich regelmässig beim Hardware-Watchdog meldet. 

Erhält der Hardware-Watchdog nicht innerhalb einer vorkonfigurierten Zeitspanne eine neue Meldung vom Software-Watchdog, startet der Hardware-Watchdog automatisch den Rechner neu.

Kontrolle, ob ein Hardwareschaltung für den Hardware-Watchdog vorhanden ist, vom Linux-Kernel unterstützt wird und der Hardware-Watchdog funktioniert:

    $ ls /dev/watchdog*
    /dev/watchdog   /dev/watchdog0

    $ wdctl
    Gerät:        /dev/watchdog
    Identität:    Broadcom BCM2835 Watchdog timer [Version 0]
    Zeitüberschreitung: 15 Sekunden
    Vorherige Zeitüberschreitung:  0 Sekunden
    Zeit übrig:   14 Sekunden
    FLAG           DESCRIPTION               STATUS BOOT-STATUS
    KEEPALIVEPING  Keep alive ping reply          1           0
    MAGICCLOSE     Supports magic close char      0           0
    SETTIMEOUT     Set timeout (in seconds)       0           0

Hardware-Watchdog testen:

    $ cat >> /dev/watchdog
    => Rechner startet nach 15 Sekunden automatisch neu

Als Software-Watchdog sollte Systemd verwendet werden. Dazu muss Systemd in der Konfigurationsdatei /etc/systemd/system.conf umkonfiguriert werden:

    $ nano /etc/systemd/system.conf

    RuntimeWatchdogSec=30
    RebootWatchdogSec=10min
    ShutdownWatchdogSec=10min
    KExecWatchdogSec=30

Informationen zu den einzelnen Konfigurationsparametern liefert die entsprechende Man-Page:

    # man systemd-system.conf

Generell sollten unter Linux zu allen Befehlen, Programmen und Konfigurationsdateien bei Erstanwendung die entsprechende Man-Page konsultiert werden!

Nach dem Speichern der geänderten Systemd-Konfigurationsdatei den Rechner neustarten, damit die Änderungen übernommen werden:

    $ reboot

    $ wdctl
    wdctl: /dev/watchdog: Watchdog wird bereits verwendet, wird abgebrochen
    wdctl: /dev/watchdog kann nicht geöffnet werden: ...

Linux-Kernel einfrieren lassen  => **!!! Vorgängig alle Daten sichern !!!**

    $ echo c > /proc/sysrq-trigger
    => Rechner wird nach 30 Sekunden automatisch neu gestartet.

### Firewallregeln
Die Firewallkonfiguration ist so anzupassen, dass auf der Loopback-Netzwerkschnittstelle Netzwerkverbindungen mit dem Netzwerkprotokoll SCTP gestattet sind.  
- https://de.wikipedia.org/wiki/Stream_Control_Transmission_Protocol  
- https://www.ionos.de/digitalguide/server/knowhow/sctp-stream-control-transmission-protocol/

Die Konfiguration der Linux-Firewall im Allgemeinen wurde unter:  
https://www.lancom-forum.de/fragen-zum-thema-firewall-f15/bandbreite-zum-surfen-zur-ausenstelle-begrenzen-t17321.html#p106507  
ausführlich beschrieben.

Aktuelle Filtertabellen der Linux-Firewall abfragen:

    $ iptables -L -n -v -t filter

    ...		target		prot	opt	in 	out	source		destination

    Chain INPUT (policy DROP ...)
    ...		ACCEPT		all		--	*	*	0.0.0.0/0	0.0.0.0/0	ctstate RELATED,ESTABLISHED
    ...		ACCEPT		tcp 	--	lo	*	127.0.0/8	127.0.0.8	ctstate NEW
    ...		ACCEPT		udp		--	lo 	*	127.0.0/8	127.0.0.8	ctstate NEW

    Chain FORWARD (policy DROP ...)

    Chain OUTPUT (policy DROP ...)
    ...		ACCEPT		all		--	*	*	0.0.0.0/0	0.0.0.0/0	ctstate RELATED,ESTABLISHED
    ...		ACCEPT		tcp 	--	* 	lo	127.0.0/8	127.0.0.8	ctstate NEW
    ...		ACCEPT		udp		--	* 	lo	127.0.0/8	127.0.0.8	ctstate NEW

Neue Firewallregeln für SCTP ergänzen:

    $ iptables -A INPUT -s 127.0.0/8 -d 127.0.0/8 -i lo -p sctp -m conntrack --ctstate NEW -j ACCEPT
    $ iptables -A OUTPUT -s 127.0.0/8 -d 127.0.0/8 -o lo -p sctp -m conntrack --ctstate NEW -j ACCEPT

Achtung:  	

    Achtung:  Die neuen Firewallregeln für SCTP gehen bei jedem Rechnerneustart verloren!

Damit die SCTP-Firewallregeln den Rechnerstart überleben, muss ein Verfahren zum Speichern und Laden der Firewallregeln in/aus einer Konfigurationsdatei verwendet werden. Siehe dazu:  
https://www.lancom-forum.de/fragen-zum-thema-firewall-f15/bandbreite-zum-surfen-zur-ausenstelle-begrenzen-t17321.html#p106507

Aktuelle Filtertabellen der Linux-Firewall abfragen:

    $ iptables -L -n -v -t filter
    
    ...		target		prot	opt	in 	out	source		destination

    Chain INPUT (policy DROP ...)
    ...		ACCEPT		all		--	*	*	0.0.0.0/0	0.0.0.0/0	ctstate RELATED,ESTABLISHED
    ...		ACCEPT		tcp 	--	lo	*	127.0.0/8	127.0.0.8	ctstate NEW
    ...		ACCEPT		udp		--	lo 	*	127.0.0/8	127.0.0.8	ctstate NEW
    ...		ACCEPT		sctp	--	lo 	*	127.0.0/8	127.0.0.8	ctstate NEW

    Chain FORWARD (policy DROP ...)

    Chain OUTPUT (policy DROP ...)
    ...		ACCEPT		all		--	*	*	0.0.0.0/0	0.0.0.0/0	ctstate RELATED,ESTABLISHED
    ...		ACCEPT		tcp 	--	* 	lo	127.0.0/8	127.0.0.8	ctstate NEW
    ...		ACCEPT		udp		--	* 	lo	127.0.0/8	127.0.0.8	ctstate NEW
    ...		ACCEPT 		sctp	--	* 	lo 	127.0.0/8	127.0.0/8	ctstate NEW

### Überflüssige Echtzeitanwendungen
Überflüssige Echtzeitanwendungen ausschalten:

    $ systemctl status multipathd.service
    $ systemctl stop multipathd.service
    $ systemctl disable multipathd.service
    $ systemctl mask multipathd.service

### Logbuch des Rechners
Das durch Systemd geführte Logbuch in der Konfigurationsdatei /etc/systemd/journald.conf korrekt konfigurieren:

    $ nano /etc/systemd/journald.conf

    [Journal]
    Storage=persistent
    SystemMaxUse=300M

Hilfestellung bietet die man-Page:

    # man journald.conf

## Installation von Lime Suite
Die Lime Suite ist eine Softwaresammlung für die Ansteuerung des LimeSDR Mini. Lime Suite ermöglicht das Einspielen von Firmware-Updates in den LimeSDR Mini.  
https://wiki.myriadrf.org/Lime_Suite

Komplette Lime Suite installieren:

    $ apt install limesuite

udev-Regel für den LimeSDR Mini installieren:

    $ apt install limesuite-udev
    $ reboot

Installation der Lime Suite kontrollieren:

    # LimeUtil --info
    Supported connections:
      * FT601

Ansteuerung des LimeSDR Mini kontrolieren:

    # LimeUtil --find
    * [LimeSDR mini, media=USB 3.0, module=FT601, ...]
 
Darauf achten, dass der LimeSDR Mini mit einem USB 3.0-Kabel an einer USB 3.0-Buchse des Raspberry Pi 4 angeschlossen ist!

    # LimeUtil --make
    Make device
      Device name: LimeSDR-Mini
      Expansion name: UNSUPPORTED
      ...
      Gateware target: LimeSDR-Mini
      ...
      Free Connection... OK
  
Firmware des LimeSDR Mini aktualisieren:

    # LimeUtil --update
    Connected to [LimeSDR Mini ...]
    Existing gateware is same as update (...)

    Programming update complete!

Ausführliche Informationen zum Updateprozess vom LimeSDR Mini findet man unter:  
https://wiki.myriadrf.org/LimeSDR_Firmware_Management

LimeSDR Mini testen. Dazu muss der LimeSDR Mini mit zwei Antennen ausgerüstet sein!

    # LimeQuickTest
    [TESTING STARTED ]
    ->Start time: ...

    ->Device: LimeSDR Mini, media=USB 3.0, module=FT601, ...
    ...

    [ Clock Network Test ]
    ->REF clock test
      Test results: ... - PASSED
    ->VCTCXO test
      Results ... - PASSED
    ->Clock Network Test PASSED

    [ FPGA EEPROM Test ]
    ...
    ->FPGA EEPROM Test PASSED

    [ LMS7002M Test ]
    ->Configure LMS
    ->Run Tests (TX_2 -> LNA_W):
      CH0 (SXR=1000.0 MHz, SXT=1005.0 MHz): ... - FAILED
    ->Run Tests (TX_2 -> LNA_W):
      CH0 (SXR=2100.0 MHz, SXT=2105.0 MHz): ... - PASSED
    ->RF Loopback Test FAILED

    => Board tests FAILED <=
    ...

Der LimeSDR Mini besteht einige Tests nicht, weil er nur über 1x Sendeantenne und 1x Empfangsantenne verfügt. Nur der LimeSDR mit 2x Sendeantenne und 2x Empfangsantenne kann diese Tests bestehen!

## OsmoCOM
OsmoTRX in Kombination mit OsmoBTS ermöglicht den Betrieb einer eigenen 2G/GSM-Mobilfunkantenne (BTS).   
- OsmoTRX (SDR-Ansteuerung) https://osmocom.org/projects/osmotrx/wiki/OsmoTRX     
- OsmoBTS (BTS) https://osmocom.org/projects/osmobts/wiki      

- https://de.wikipedia.org/wiki/Base_Transceiver_Station      

Das eigene 2G/GSM-Kernnetzwerk kann mit Hilfe von: 

- OsmoBSC (BSC) https://osmocom.org/projects/osmobsc/wiki    
- OsmoPCU (PCU) https://osmocom.org/projects/osmopcu/wiki/OsmoPCU    
- OsmoMGW https://osmocom.org/projects/osmo-mgw/wiki    
- OsmoSTP https://osmocom.org/projects/osmo-stp/wiki    
- OsmoMSC (MSC) https://osmocom.org/projects/osmomsc/wiki    
- OsmoSGSN (SGSN) https://osmocom.org/projects/osmosgsn/wiki/OsmoSGSN    
- OsmoGGSN (GGSN) https://osmocom.org/projects/openggsn/wiki/OsmoGGSN    
- OsmoHLR (HLR) https://osmocom.org/projects/osmo-hlr/wiki/OsmoHLR    
- Osmo-sip-connector https://osmocom.org/projects/osmo-sip-conector/wiki    

- https://de.wikipedia.org/wiki/Base_Station_Controller    
- https://de.wikipedia.org/wiki/Mobile-services_Switching_Centre    
- https://de.wikipedia.org/wiki/Packet_Control_Unit    
- https://de.wikipedia.org/wiki/General_Packet_Radio_Service#SGSN_(Serving_GPRS_Support_Node)    
- https://de.wikipedia.org/wiki/General_Packet_Radio_Service#GGSN_(Gateway_GPRS_Support_Node)    
- https://de.wikipedia.org/wiki/Home_Location_Register   

realisiert werden. 

### Installation von OsmoCOM
OsmoCOM ermöglicht den Betrieb eines eigenen 2G/GSM-Mobilfunknetzwerks mit Ansteuerung der SDR-Hardware über die Lime Suite. 

#### Installation mit Binärpaketen
Die Installation der meisten Softwarekomponenten für den Betrieb eines eigenen 2G/GSM-Mobilfunknetzwerks ist sehr einfach mit Softwarepaketen möglich (DEB):

    $ apt install osmo-bsc
    $ apt install osmo-bts
    $ apt install osmo-hlr
    $ apt install osmo-mgw
    $ apt install osmo-msc
    $ apt install osmo-stp

#### Installation aus Quellcode
Leider fehlt der über das Softwarepaket (DEB) installierten Software OsmoTRX das Modul für die Ansteuerung des LimeSDR (Mini). Die Ansteuerung von LimeSDR (Mini) wird erst ab der Version 1.0 von OsmoTRX unterstützt. Deshalb muss OsmoTRX aus dem Quellcode kompiliert und installiert werden.

    $ apt install automake
    $ apt install libtool
    $ apt install pkg-config
    $ apt install g++
    $ apt install make
    $ apt install libosmocore-dev
    $ apt install libusb-1.0-0-dev
    $ apt install libfftw3-dev
    $ apt install libboost-dev
    $ apt install liblimesuite-dev

Für die Kompilierung sind keine root-Rechte erforderlich!

    # cd ~
    # git clone https://gitea.osmocom.org/cellular-infrastructure/osmo-trx.git
    # cd ~/osmo-trx
    # git checkout 1.1.1

    # autoreconf --install
    # ./configure --with-uhd=no --with-usrp1=no --with-lms=yes

Mit root-Rechten das Programm OsmoTRX installieren und die Zugriffsrechte setzen:

    $ cp /home/foo/osmo-trx/Transceiver52M/osmo-trx-lms /usr/local/bin/
    $ chown root:root /usr/local/bin/osmo-trx-lms
    $ chmod u=rwx,g=rx,o=rx /usr/local/bin/osmo-trx-lms
    $ ls -alh /usr/local/bin/osmo-trx-lms
    -rwxr-xr-x ... root root 4.1M ... /usr/local/bin/osmo-trx-lms

Zum Abschluss ist eine leere Konfigurationsdatei für OsmoTRX zu erstellen:

    $ touch /etc/osmocom/osmo-trx-lms.cfg
    $ chown root:root /etc/osmocom/osmo-trx-lms.cfg
    $ chmod u=rw,g=r,o=r /etc/osmocom/osmo-trx-lms.cfg
    $ ls -alh /etc/osmocom/osmo-trx-lms.cfg
    -rw-r--r-- ... root root ... /etc/osmocom/osmo-trx-lms.cfg

### Konfiguration von OsmoCOM
OsmoCOM wird über Konfigurationsdateien dauerhaft konfiguriert. Die Konfigurationsparametern sind in den Bedienungsanleitungen von OsmoCOM ausführlich beschrieben. 

Die offiziellen Bedienungsanleitungen von OsmoCOM sind hier erhältlich:   
https://ftp.osmocom.org/docs/ 

Einige Konfigurationsparametern sind gar nicht oder schlecht dokumentiert. 

Diese Webseite:    
https://osmocom.org/projects/cellular-infrastructure/wiki/Osmocom_Network_In_The_Box     
fungierte als Vorlage für alle Konfigurationen der Programme von OsmoCOM.

Sicherheitswarnung:

    Warnung:

    Die Konfiguration der OsmoCOM-Programme kann während der Laufzeit
    per Telnet über die VTY-Schnittstelle oder über die Kontroll-
    schnittstelle (control interface) geändert werden!

#### OsmoHLR
Starten wir mit der Konfigurationsdatei von OsmoHLR:

    $ nano /etc/osmocom/osmo-hlr.cfg

    line vty
     bind 127.0.0.1
    ctrl
     bind 127.0.0.1
    hlr
     #subscriber-create-on-demand 5 cs
     #store-imei
     #ussd route prefix *#100# internal own-msisdn
     #ussd route prefix *#200# internal own-imsi
     gsup
     bind ip 127.0.0.1
    log stderr
     logging filter all 1
     logging print category 1
     logging print category-hex 0
     logging print level 1
     logging timestamp 0
     logging level all notice

Aus Sicherheitsgründen sollte die Software von OsmoCOM ohne root-Rechte gestartet werden. Deshalb ist der Ersatz aller offiziellen Systemd-Dienste durch eigene, selber erstellte Systemd-Dienste erforderlich.

    $ touch /etc/systemd/system/osmo-hlr_secure.service
    $ chown root:root /etc/systemd/system/osmo-hlr_secure.service
    $ chmod u=rw,g=r,o=r /etc/systemd/system/osmo-hlr_secure.service
    $ ls -alh /etc/systemd/system/osmo-hlr_secure.service
    -rw-r--r-- ... root root ... /etc/systemd/system/osmo-hlr_secure.service

Den eigenen, sicheren Systemd-Dienst editieren:

    $ nano /etc/systemd/system/osmo-hlr_secure.service

    [Unit]
    Description=Osmocom Home Location Register (OsmoHLR)
    Documentation=https://projects.osmocom.org/projects/osmo-hlr

    [Service]
    User=foo
    Group=foo

    StateDirectoryMode=0700
    StateDirectory=osmo-hlr
    WorkingDirectory=%S/osmo-hlr
    UMask=0177

    Type=exec
    ExecStart=/usr/bin/osmo-hlr -c /etc/osmocom/osmo-hlr.cfg -l /var/lib/osmo-hlr/hlr.db
    Restart=always
    RestartSec=2
    OOMPolicy=stop

    [Install]
    WantedBy=multi-user.target

Die Bedeutung der einzelnen Konfigurationsparametern kann in den entsprechenden Man-Pages nachgelesen werden:

    # man systemd.exec
    # man systemd.unit
    # man systemd.service    

Den offiziellen Systemd-Dienst von OsmoHLR beenden und für immer ausschalten:

    $ systemctl status osmo-hlr.service
    $ systemctl stop osmo-hlr.service
    $ systemctl disable osmo-hlr.service
    $ systemctl mask osmo-hlr.service
    $ systemctl status osmo-hlr.service

Den eigenen, sicheren Systemd-Dienst einschalten und starten:

    $ systemctl daemon-reload
    $ systemctl status osmo-hlr_secure.service
    $ systemctl enable osmo-hlr_secure.service
    $ systemctl start osmo-hlr_secure.service
    $ systemctl status osmo-hlr_secure.service    

Die Ausgaben von OsmoHLR kontrollieren:

    $ journalctl -b -u osmo-hlr_secure.service

OsmoHLR gehört zu den wenigen Programmen von OsmoCOM, welches für den Betrieb Daten in eine Datei schreibt oder Daten aus einer Datei liest. Bei der Nutzung des selber erstellten Systemd-Dienstes erstellt OsmoHLR im Verzeichnis /var/lib/osmo-hlr die Teilnehmerdatenbank (HLR). Für die Teilnehmerdatenbank (HLR) verwendet OsmoHLR 3 Dateien:

    $ ls -alh /var/lib/osmo-hlr/
    drwxr-xr-x ... foo foo ... .
    ...
    -rw-r--r-- ... foo foo ... hlr.db
    -rw-r--r-- ... foo foo ... hlr.db-shm
    -rw-r--r-- ... foo foo ... hlr.db-wal

Sicherheitswarnung:

    Warnung:

    Die Teilnehmerdatenbank (hlr.db + hlr.db-shm + hlr.db-wal) enthält in der Regel
    besonders schützenswerte Informationen. 

    Deshalb müssen aus Sicherheitsgründen die Leserechte auf diese Datei besonders
    stark eingeschränkt werden und die Zugriffsrechte regelmässig kontrolliert werden.

#### OsmoMGW
Jegliche Sprachtelefonie wird über das "Media Gateway" (OsmoMGW) abgewickelt. Deshalb ist OsmoMGW für Sprachtelefonie im eigenen 2G/GSM-Mobilfunknetzwerk zwingend erforderlich. 

Es werden zwei "Media Gateways" benötigt (OsmoMGW). Ein "Media Gateway" für den BSC und ein "Media Gateway" für den MSC. Die Konfigurationsdatei für den OsmoMGW vom BSC heisst "osmo-mgw_ran.cfg". Die Konfigurationsdatei für den OsmoMGW vom MSC heisst "osmo-mgw_cn.cfg".

Zwei leere Konfigurationsdateien für OsmoMGW erstellen:

    $ touch /etc/osmocom/osmo-mgw_cn.cfg
    $ touch /etc/osmocom/osmo-mgw_ran.cfg
    
    $ chown root:root /etc/osmocom/osmo-mgw_cn.cfg
    $ chown root:root /etc/osmocom/osmo-mgw_ran.cfg
    
    $ chmod u=rw,g=r,o=r /etc/osmocom/osmo-mgw_cn.cfg
    $ chmod u=rw,g=r,o=r /etc/osmocom/osmo-mgw_ran.cfg

    $ ls -alh /etc/osmocom/
    -rw-r--r-- ... root root ... /etc/osmocom/osmo-mgw_cn.cfg
    -rw-r--r-- ... root root ... /etc/osmocom/osmo-mgw_ran.cfg

Die Konfigurationsdatei vom OsmoMGW für das MSC mit Konfigurationsparametern befüllen:

    $ nano /etc/osmocom/osmo-mgw_cn.cfg

    mgcp
     bind ip 127.0.0.1
     local ip 127.0.0.1
     number endpoints 31
     rtp bind-ip 127.0.0.1
     rtp port-range 10000 11999
     rtp-accept-all 1
     rtp keep-alive 10
     rtp-patch ssrc
     rpt-patch timestamp
    line vty
     bind 127.0.0.1
    log stderr
     logging filter all 1
     logging print category 1
     logging print category-hex 0
     logging print level 1
     logging timestamp 0
     logging level all info

Die Konfigurationsdatei vom OsmoMGW für den BSC mit Konfigurationsparametern befüllen:

    $ nano /etc/osmocom/osmo-mgw_ran.cfg

    mgcp
     bind ip 127.0.0.2
     local ip 127.0.0.2
     number endpoints 31
     rtp bind-ip 127.0.0.2
     rtp port-range 12000 13999
     rtp-accept-all 1
     rtp keep-alive 10
     rtp-patch ssrc
     rpt-patch timestamp
    line vty
     bind 127.0.0.2
    log stderr
     logging filter all 1
     logging print category 1
     logging print category-hex 0
     logging print level 1
     logging timestamp 0
     logging level all info     

Aus Sicherheitsgründen sollte die Software von OsmoCOM ohne root-Rechte gestartet werden. Deshalb ist der Ersatz aller offiziellen Systemd-Dienste durch eigene, selber erstellte Systemd-Dienste erforderlich.

    $ touch /etc/systemd/system/osmo-mgw_cn.service
    $ touch /etc/systemd/system/osmo-mgw_ran.service    

    $ chown root:root /etc/systemd/system/osmo-mgw_cn.service
    $ chown root:root /etc/systemd/system/osmo-mgw_ran.service
        
    $ chmod u=rw,g=r,o=r /etc/systemd/system/osmo-mgw_cn.service
    $ chmod u=rw,g=r,o=r /etc/systemd/system/osmo-mgw_ran.service

    $ ls -alh /etc/systemd/system/osmo-mgw_cn.service
    -rw-r--r-- ... root root ... /etc/systemd/system/osmo-mgw_cn.service

    $ ls -alh /etc/systemd/system/osmo-mgw_ran.service
    -rw-r--r-- ... root root ... /etc/systemd/system/osmo-mgw_ran.service    

Die eigenen, sicheren Systemd-Dienste editieren. Zuerst den Systemd-Dienst für den OsmoMGW für das MSC:

    $ nano /etc/systemd/system/osmo-mgw_cn.service

    [Unit]
    Description=Osmocom Media Gateway (MGW)
    Documentation=https://osmocom.org/projects/osmo-mgw

    [Service]
    User=foo
    Group=foo
    Type=exec
    ExecStart=/usr/bin/osmo-mgw -c /etc/osmocom/osmo-mgw_cn.cfg
    Restart=always
    RestartSec=2
    OOMPolicy=stop

    # Let it process messages quickly enough
    CPUSchedulingPolicy=rr
    CPUSchedulingPriority=2
    CPUSchedulingResetOnFork=false
    LimitMEMLOCK=200M

    [Install]
    WantedBy=multi-user.target

Dann der Systemd-Dienst für den OsmoMGW für den BSC:

    $ nano /etc/systemd/system/osmo-mgw_ran.service

    [Unit]
    Description=Osmocom Media Gateway (MGW)
    Documentation=https://osmocom.org/projects/osmo-mgw

    [Service]
    User=foo
    Group=foo
    Type=exec
    ExecStart=/usr/bin/osmo-mgw -c /etc/osmocom/osmo-mgw_ran.cfg
    Restart=always
    RestartSec=2
    OOMPolicy=stop

    # Let it process messages quickly enough
    CPUSchedulingPolicy=rr
    CPUSchedulingPriority=2
    CPUSchedulingResetOnFork=false
    LimitMEMLOCK=200M

    [Install]
    WantedBy=multi-user.target

Die Bedeutung der einzelnen Konfigurationsparametern kann in den entsprechenden Man-Pages nachgelesen werden:

    # man systemd.exec
    # man systemd.unit
    # man systemd.service       

Den offiziellen Systemd-Dienst von OsmoMGW beenden und für immer ausschalten:

    $ systemctl status osmo-mgw.service
    $ systemctl stop osmo-mgw.service
    $ systemctl disable osmo-mgw.service
    $ systemctl mask osmo-mgw.service
    $ systemctl status osmo-mgw.service

Die eigenen, sicheren Systemd-Dienste einschalten und starten:

    $ systemctl daemon-reload

    $ systemctl status osmo-mgw_cn.service
    $ systemctl enable osmo-mgw_cn.service
    $ systemctl start osmo-mgw_cn.service
    $ systemctl status osmo-mgw_cn.service

    $ systemctl status osmo-mgw_ran.service
    $ systemctl enable osmo-mgw_ran.service
    $ systemctl start osmo-mgw_ran.service
    $ systemctl status osmo-mgw_ran.service         

Die Ausgaben von OsmoMGW kontrollieren:

    $ journalctl -b -u osmo-mgw_cn.service     
    $ journalctl -b -u osmo-mgw_ran.service

#### OsmoSTP
OsmoSTP kümmert sich um die Kommunikation zwischen den Netzwerkelementen im eigenen 2G/GSM-Mobilfunknetzwerk. Deshalb ist OsmoSTP im eigenen 2G/GSM-Mobilfunknetzwerk zwingend erforderlich. 

Die Konfigurationsdatei von OsmoSTP ist komplett zu überarbeiten:

    $ nano /etc/osmocom/osmo-mgw.cfg

    cs7 instance 0
     asp MSC 4302 2905 m3ua
      remote-ip 127.0.0.1
     as MSC m3ua
      asp MSC
      routing-key 3 0.23.1 
     asp BSC 4301 2905 m3ua
      remote-ip 127.0.0.1
     as BSC m3ua
      asp BSC
      routing-key 2 1.23.3
     route-table system
      update route 0.23.1 0.23.1 linkset MSC
      update route 1.23.3 1.23.3 linkset BSC
     xua rkm routing-key-allocation static only
     listen m3ua 2905
      local-ip 127.0.0.1
      accept-asp-connections pre-configured
    
    log stderr
     logging filter all 1   
     logging print category 1
     logging print category-hex 0
     logging print level 1
     logging timestamp 0
     logging level all info        

Sicherheitswarnung:

    Warnung:

    Aus Sicherheitsgründen sollte mit möglichst strikten Firewall-
    regeln die Nutzung vom Netzwerkprotokoll SCTP auf die Loopback-
    Netzwerkschnittstelle eingeschränkt werden!

Aus Sicherheitsgründen sollte die Software von OsmoCOM ohne root-Rechte gestartet werden. Deshalb ist der Ersatz aller offiziellen Systemd-Dienste durch eigene, selber erstellte Systemd-Dienste erforderlich.

    $ touch /etc/systemd/system/osmo-stp_secure.service
    $ chown root:root /etc/systemd/system/osmo-stp_secure.service
    $ chmod u=rw,g=r,o=r /etc/systemd/system/osmo-stp_secure.service
    $ ls -alh /etc/systemd/system/osmo-stp_secure.service
    -rw-r--r-- ... root root ... /etc/systemd/system/osmo-stp_secure.service

Den eigenen, sicheren Systemd-Dienst editieren:

    $ nano /etc/systemd/system/osmo-stp_secure.service

    [Unit]
    Description=Osmocom STP (Signal Transfer Point)
    Documentation=https://osmocom.org/projects/osmo-stp/wiki

    [Service]
    User=foo
    Group=foo
    Type=exec
    ExecStart=/usr/bin/osmo-stp -c /etc/osmocom/osmo-stp.cfg
    Restart=always
    RestartSec=2
    OOMPolicy=stop

    [Install]
    WantedBy=multi-user.target

Die Bedeutung der einzelnen Konfigurationsparametern kann in den entsprechenden Man-Pages nachgelesen werden:

    # man systemd.exec
    # man systemd.unit
    # man systemd.service    

Den offiziellen Systemd-Dienst von OsmoSTP beenden und für immer ausschalten:

    $ systemctl status osmo-stp.service
    $ systemctl stop osmo-stp.service
    $ systemctl disable osmo-stp.service
    $ systemctl mask osmo-stp.service
    $ systemctl status osmo-stp.service

Den eigenen, sicheren Systemd-Dienst einschalten und starten:

    $ systemctl daemon-reload
    $ systemctl status osmo-stp_secure.service
    $ systemctl enable osmo-stp_secure.service
    $ systemctl start osmo-stp_secure.service
    $ systemctl status osmo-stp_secure.service    

Die Ausgaben von OsmoSTP kontrollieren:

    $ journalctl -b -u osmo-stp_secure.service 

#### OsmoMSC
Als nächstes passen wir die Konfigurationsdatei von OsmoMSC an:

    $ nano /etc/osmocom/osmo-mgw.cfg

    network
     # See https://en.wikipedia.org/wiki/Mobile_country_code for 'network country
     # code' (MCC) and 'mobile network code' (MNC)
     # MCC=1 and MNC=1 are for TEST network
     network country code 228
     mobile network code 99

     mm info 1

     # The name of the network:
     short name Test
     long name TestLang

     encryption a5 0

    cs7 instance 0
     point-code 0.23.1
     asp MSC 2905 4302 m3ua
      remote-ip 127.0.0.1
     as MSC m3ua
      asp MSC
      routing-key 3 0.23.1
     
    msc
     # Set this to the IP address which osmo-mgw is listening to:
     # (osmo-mgw provides the media gateway between osmo-bsc and osmo-msc)
     mgw remote-ip 127.0.0.1
     #check-imei-rqd early

    mncc-int
     default-codec tch-f efr

    log stderr
     logging filter all 1
     logging print category 1
     logging print category-hex 0
     logging print level 1
     logging timestamp 0
     logging level all notice

Im Block "network" sind folgende Anpassungen erforderlich:

Die von der Funkaufsichtsbehörde vergebene Mobilfunknetzkennzahl eingeben. Die Mobilfunknetzkennzahl besteht aus dem Ländercode (MCC) und der Netzkennung (MNC).  
https://de.wikipedia.org/wiki/Mobilfunknetzkennzahl

Zum Beispiel für die Mobilfunknetzkennzahl 228-99:

     network country code 228
     mobile network code 99

Dann muss im Block "network" die Bezeichnung des eigenen 2G/GSM-Mobilfunknetzwerks angepasst werden. Zum Beispiel:

     short name Test
     long name TestLang

Damit die Bezeichnung des eigenen 2G/GSM-Mobilfunknetzwerks auf dem Display vom Mobiltelefon angezeigt wird, muss diese Zeile ergänzt werden:

     mm info 1

Aber Achtung:

    Achtung:

    Mit der Konfigurationszeile "mm info 1" wird die Übertragung der
    aktuellen Uhrzeit und der lokalen Zeitzone an das Mobiltelefon 
    aktiviert. 
    
    Deshalb muss gewährleistet sein, dass die Uhrzeit vom Rechner 
    immer mit einer Atomuhr synchronisiert wird und die lokale 
    Zeitzone auf dem Rechner korrekt konfiguriert ist.

    Üblicherweise wird auf Linux-Rechnern heute der Dienst chrony
    und das Netzwerkprotokoll NTP für die Zeitsynchronisation 
    verwendet.

Ein Sicherheitshinweis zur verschlüsselten Kommunikation zwischen Mobiltelefon und Mobilfunkantenne:

    Achtung:

    Mit der Konfigurationszeile "encryption a5 0" wird die 
    verschlüsselte Kommunikation zwischen Mobilgerät (ME) 
    und Mobilfunkantenne (BTS) komplett abgeschaltet. 

    Jegliche Funkkommunikation zwischen Mobiltelefon und 
    Mobilfunkantenne erfolgt unverschlüsselt!

    Der Einsatz von unverschlüsselter Kommunikation ist 
    erforderlich, wenn das Einbuchen von Mobilgeräte mit
    fremden SIM-Karten im eigenen 2G/GSM-Mobilfunknetzwerk
    gestattet wird.

    Die in einem 2G/GSM-Mobilfunknetzwerk unterstützten
    Verschlüsselungsmethoden A5/1, A5/2 und A5/3 gelten
    als leicht knackbar und somit als unsicher.  

    Bei hohen Sicherheitsansprüchen sollte das eigene 
    Mobilfunknetzwerk mit 4G/LTE oder 5G betrieben werden.    

Nur mit einer unverschlüsselten Kommunikation zwischen Mobilgeräte (ME) und Mobilfunkantenne (BTS) können sich fremde SIM-Karte im eigenen 2G/GSM-Mobilfunknetzwerk einbuchen.   
https://ethz.ch/content/dam/ethz/special-interest/infk/inst-infsec/system-security-group-dam/education/sown_as15/2015_lecture10.pdf

Zum Betreiben eines eigenen 4G/LTE-Mobilfunknetzwerks eignet sich die Software srsRAN. Eine ausführliche srsRAN-Anleitung zur Installation und Konfiguration eines eigenen 4G/LTE-Mobilfunknetzwerk ist hier erhältlich:

https://github.com/GrandDixence/CoverageMaps/blob/main/Eigenes_Mobilfunknetzwerk/Installations-Anleitung_srsRAN_Ubuntu_20.04.md

Mit dem Block "cs7" ist die SS7-Konfiguartion zu ergänzen. SS7 wird für die Kommunikation zwischen OsmoBSC und OsmoMSC auf der A-Schnittstelle verwendet. Den Konfigurationsparameter "msc-addr" im Block "msc 0" nicht vergessen! Die SS7-Konfiguration ist sehr komplex und fehleranfällig. Deshalb sei hier für die SS7-Konfiguration auf die ausführlichen Kapiteln zu SS7 in den offiziellen OsmoBSC-, OsmoSTP- und OsmoMSC-Bedienungsanleitung verwiesen.

Mit dem Block "mncc-int" wird die Wahl des Audiocodecs ergänzt. Standardmässig soll der Audiocodec "GSM Enhanced Full Rate" (EFR) auf einem vollwertigen physikalischen Kanal (TCH_F) verwendet werden.  
- https://de.wikipedia.org/wiki/Codec   
- https://en.wikipedia.org/wiki/Enhanced_full_rate    

Aus Sicherheitsgründen sollte die Software von OsmoCOM ohne root-Rechte gestartet werden. Deshalb ist der Ersatz aller offiziellen Systemd-Dienste durch eigene, selber erstellte Systemd-Dienste erforderlich.

    $ touch /etc/systemd/system/osmo-msc_secure.service
    $ chown root:root /etc/systemd/system/osmo-msc_secure.service
    $ chmod u=rw,g=r,o=r /etc/systemd/system/osmo-msc_secure.service
    $ ls -alh /etc/systemd/system/osmo-msc_secure.service
    -rw-r--r-- ... root root ... /etc/systemd/system/osmo-msc_secure.service

Den eigenen, sicheren Systemd-Dienst editieren:

    $ nano /etc/systemd/system/osmo-msc_secure.service

    [Unit]
    Description=Osmocom Mobile Switching Center (MSC)

    After=osmo-hlr_secure.service
    After=osmo-mgw_secure.service

    Wants=osmo-hlr_secure.service
    Wants=osmo-mgw_secure.service

    [Service]
    User=foo
    Group=foo

    StateDirectoryMode=0700
    StateDirectory=osmo-msc
    WorkingDirectory=%S/osmo-msc
    UMask=0177    

    Type=exec
    ExecStart=/usr/bin/osmo-msc -c /etc/osmocom/osmo-msc.cfg
    Restart=always
    RestartSec=2
    OOMPolicy=stop

    [Install]
    WantedBy=multi-user.target

Die Bedeutung der einzelnen Konfigurationsparametern kann in den entsprechenden Man-Pages nachgelesen werden:

    # man systemd.exec
    # man systemd.unit
    # man systemd.service    

Den offiziellen Systemd-Dienst von OsmoMSC beenden und für immer ausschalten:

    $ systemctl status osmo-msc.service
    $ systemctl stop osmo-msc.service
    $ systemctl disable osmo-msc.service
    $ systemctl mask osmo-msc.service
    $ systemctl status osmo-msc.service

Den eigenen, sicheren Systemd-Dienst einschalten und starten:

    $ systemctl daemon-reload
    $ systemctl status osmo-msc_secure.service
    $ systemctl enable osmo-msc_secure.service
    $ systemctl start osmo-msc_secure.service
    $ systemctl status osmo-msc_secure.service    

Die Ausgaben von OsmoMSC kontrollieren:

    $ journalctl -b -u osmo-msc_secure.service

OsmoMSC gehört zu den wenigen Programmen von OsmoCOM, welches für den Betrieb Daten in eine Datei schreibt oder Daten aus einer Datei liest. Bei der Nutzung des selber erstellten Systemd-Dienstes erstellt OsmoMSC im Verzeichnis /var/lib/osmo-msc die SMS-Datenbank. Für die SMS-Datenbank verwendet OsmoMSC eine einzige Datei:

    $ ls -alh /var/lib/osmo-msc/
    drwxr-xr-x ... foo foo ... .
    ...
    -rw-r--r-- ... foo foo ... sms.db
    
Sicherheitswarnung:

    Warnung:

    Die SMS-Datenbank (sms.db) enthält in der Regel aus Datenschutzgründen 
    schützenswerte Informationen in Form von zu versendenden SMS. 

    Deshalb müssen aus Sicherheitsgründen die Leserechte auf diese Datei besonders
    stark eingeschränkt werden und die Zugriffsrechte regelmässig kontrolliert werden.   

#### OsmoBSC
Die Konfigurationsdatei von OsmoBSC ist mit Abstand die anspruchvollste und zeitaufwendigste Konfigurationsdatei:

    $ nano /etc/osmocom/osmo-bsc.cfg

    network
     network country code 228
     mobile network code 99
     encryption a5 0
     handover 0
     bts 0
      type sysmobts
      band GSM-1800
      location_area_code 1
      ip.access unit_id 1800 1
      codec-support fr efr
      # Uncomment the following for enabling GPRS support:
      #gprs mode gprs
      #gprs nsvc 0 remote ip 127.0.0.1
      #gprs nsvc 0 remote udp port 23000
      #gprs nsvc 0 local udp port 23000
      #gprs nsvc 0 nsvci 1800
      #gprs nsei 1800
      #gprs cell bvci 1800
      #
      trx 0
       rf_locked 0
       arfcn 616
       nominal power 1
       timeslot 0
        phys_chan_config CCCH+SDCCH4
       timeslot 1
        phys_chan_config SDCCH8
       timeslot 2
        phys_chan_config TCH/F
       timeslot 3
        phys_chan_config TCH/F
       timeslot 4
        phys_chan_config TCH/F
       timeslot 5
        phys_chan_config TCH/F
       timeslot 6
        phys_chan_config TCH/F
       timeslot 7
        phys_chan_config TCH/F
    e1_input
     e1_line 0 driver ipa
    cs7 instance 0
     point-code 1.23.3
     asp MSC 2905 4301 m3ua
      remote-ip 127.0.0.1
     as MSC m3ua
      asp MSC
      routing-key 2 1.23.3
     sccp-address MSC
      point-code 0.23.1
    msc 0
     msc-addr MSC
     mgw remote-ip 127.0.0.2
     # If the osmo-mgw instance to be used listens to another port number:
     #mgw remote-port 12427
     #
     allow-emergency deny
     codec-list fr2
    log stderr
     logging filter all 1
     logging print category 1
     logging print category-hex 0
     logging print level 1
     logging timestamp 0
     logging level all info     

Im Block "network" sind folgende Anpassungen erforderlich:

Die von der Funkaufsichtsbehörde vergebene Mobilfunknetzkennzahl eingeben. Die Mobilfunknetzkennzahl besteht aus dem Ländercode (MCC) und der Netzkennung (MNC).  
https://de.wikipedia.org/wiki/Mobilfunknetzkennzahl

Zum Beispiel für die Mobilfunknetzkennzahl 228-99:

     network country code 228
     mobile network code 99

Allenfalls muss noch das zu verwendende Mobilfunkfrequenzband umkonfiguriert werden:

    band 900    => Band 8  ( 900 MHz FDD)
    band 1800   => Band 3  (1800 MHz FDD)
    band 850    => Band 5  ( 850 MHz FDD)
    band 1900   => Band 2  (1900 MHz FDD)

Zur Wahl des einzusetzenden Mobilfunkfrequenzband sind die Angaben im Kapitel "Funklizenz und funkregulatorische Anforderungen" zu beachten!

Die mit der Konfigurationszeile "ip.access unit_id" konfigurierte Gerätenummer der Mobilfunkantenne muss mit der Angabe in der OsmoBTS-Konfigurationsdatei übereinstimmen!

Mit dem Konfigurationsparameter "arfcn" wird die ARFCN des im Downlink von der Mobilfunkantenne ausgestrahlten Mobilfunksignals konfiguriert. Für jeden GSM-Funkkanal ist eine eigene ARFCN-Angabe erforderlich.

Die ARFCN-Angabe erfolgt mit der gewünschten Mittenfrequenz des Downlink-Mobilfunksignals.  
- https://de.wikipedia.org/wiki/Mittenfrequenz    
- https://de.wikipedia.org/wiki/ARFCN   

Die ARFCN kann mit dem "ARFCN calculator" auf der Webseite:  
https://www.sqimway.com/gsm_band.php    
berechnet werden. Alternativ kann die Tabelle unter:    
https://www.sqimway.com/gsm_arfcn.php    
verwendet werden.

    Achtung:

    Zur Einhaltung der funkregulatorischen Vorgaben muss bei der Konfiguration des 
    ARFCN zwingend die Angaben des zugewiesenen Mobilfunkfrequenzbands in der
    Funklizenz beachtet und eingehalten werden! 
			
Zum Beispiel für den Downlink im Mobilfunkfrequenzband 8 mit der Mittenfrequenz 938.2 MHz:

Mittenfrequenz:  938.2 MHz   => ARFCN:=16

       arfcn 16

Vorsicht bei der Funknetzplanung:

    Achtung: 

    Bei benachbarten Mobilfunkantennen ist ein Mindestabstand 
    zwischen den GSM-Funkkanäle von 400 kHz einzuhalten!

    Der Mindestabstand von 400 kHz ist auch zwischen allen
    GSM-Funkkanäle einzuhalten, welche gleichzeitig
    von derselben Mobilfunkantenne ausgestrahlt werden!

Wird die Mindestabstandanforderung von 400 kHz zwischen benachbarten GSM-Funkkanälen nicht eingehalten, stören sich die GSM-Funkkanäle gegenseitig! 

Einen lesenswerten Foliensatz zur in der Praxis anzutreffenden Signalform der Mobilfunksignale findet sich unter:
https://www.cept.org/Documents/fm-22/20585/fm22-14-42annex8_gsm-r-compatibility-measurements-bnetza

Ein Sicherheitshinweis zur verschlüsselten Kommunikation zwischen Mobiltelefon und Mobilfunkantenne:

    Achtung:

    Mit der Konfigurationszeile "encryption a5 0" wird die 
    verschlüsselte Kommunikation zwischen Mobilgerät (ME) 
    und Mobilfunkantenne (BTS) komplett abgeschaltet. 

    Jegliche Funkkommunikation zwischen Mobiltelefon und 
    Mobilfunkantenne erfolgt unverschlüsselt!

    Der Einsatz von unverschlüsselter Kommunikation ist 
    erforderlich, wenn das Einbuchen von Mobilgeräte mit
    fremden SIM-Karten im eigenen 2G/GSM-Mobilfunknetzwerk
    gestattet wird.

    Die in einem 2G/GSM-Mobilfunknetzwerk unterstützten
    Verschlüsselungsmethoden A5/1, A5/2 und A5/3 gelten
    als leicht knackbar und somit als unsicher.  

    Bei hohen Sicherheitsansprüchen sollte das eigene 
    Mobilfunknetzwerk mit 4G/LTE oder 5G betrieben werden.    

Nur mit einer unverschlüsselten Kommunikation zwischen Mobilgeräte (ME) und Mobilfunkantenne (BTS) können sich fremde SIM-Karte im eigenen 2G/GSM-Mobilfunknetzwerk einbuchen.   
https://ethz.ch/content/dam/ethz/special-interest/infk/inst-infsec/system-security-group-dam/education/sown_as15/2015_lecture10.pdf

Zum Betreiben eines eigenen 4G/LTE-Mobilfunknetzwerks eignet sich die Software srsRAN. Eine ausführliche srsRAN-Anleitung zur Installation und Konfiguration eines eigenen 4G/LTE-Mobilfunknetzwerk ist hier erhältlich:

https://github.com/GrandDixence/CoverageMaps/blob/main/Eigenes_Mobilfunknetzwerk/Installations-Anleitung_srsRAN_Ubuntu_20.04.md

Mit dem Konfigurationsparameter:

     handover 0

wird die Unterstützung zum Wechsel der Funkzelle (Handover) abgeschaltet.   
https://de.wikipedia.org/wiki/Handover    

Damit OsmoBSC einige Berechnungen korrekt durchführen kann, muss OsmoBSC mit dem Konfigurationsparameter "nominal power" über die Sendeleistung am Antennenanschluss der SDR-Hardware informiert werden. Dieser Konfigurationsparameter hat keinen Einfluss auf die tatsächliche Sendeleistung der SDR-Hardware!

Gemäss den Angaben unter:   
https://wiki.batc.org.uk/LimeSDR_Mini_Output_Power_Levels   
hat der LimeSDR Mini eine Sendeleistung am Antennenanschluss im Bereich von 5 dBm bis -10 dBm (3 mW bis 0.1 Milliwatt). 

Zum Beispiel für eine Sendeleistung von 2.5 mW (4 dBm) am Antennenanschluss der SDR-Hardware:

    nominal power 4

Die Sendeleistung wird bei diesem Konfigurationsparameter in der Einheit dBm angegeben.

Mobilfunk nach dem Standard der 2. Generation des Mobilfunks (2G/GSM) arbeitet stark vereinfacht mit 8 nennenswerten Zeitschlitzen pro GSM-Funkkanal. Jeder weitere GSM-Funkkanal erweitert die Anzahl der gleichzeitig nutzbaren Zeitschlitze um 8. Also:

- 1 GSM-Funkkanal (1x TCH)  =>  8 gleichzeitig nutzbare Zeitschlitze
- 2 GSM-Funkkanäle (2x TCH) => 16 gleichzeitig nutzbare Zeitschlitze
- 3 GSM-Funkkanäle (3x TCH) => 24 gleichzeitig nutzbare Zeitschlitze

Diese 8 nennenswerten Zeitschlitze pro GSM-Funkkanal werden in der Fachliteratur "physikalische Kanäle" genannt (physical channel). Die "physikalischen Kanäle" dürfen nicht mit den "logischen Kanälen" (logical channel) verwechselt werden!

Ein Zeitschlitz eines physikalischen Kanals dauert 0.577 Millisekunden. Ein Rahmen mit 8 dieser Zeitschlitze hat eine Länge von 4.615 ms. Was einer Pulswiederholrate von 217 Hz entspricht.

Alle "physikalischen Kanäle" sind bei der Funknetzplanung mit "logischen Kanälen" zu belegen. Unbelegte "physikalische Kanäle" wären eine Ressourcenverschwendung: Diese Zeitschlitze würden ungenutzt bleiben und keine Daten übertragen. 

Die "logischen Kanäle" sind in zwei wesentliche Gruppen aufgeteilt: 

Verkehrskanäle (Traffic Channel -> TCH)     
https://de.wikipedia.org/wiki/Traffic_Channel   

Steuerkanäle (Control Channel -> CCH)   
https://de.wikipedia.org/wiki/Control_Channel 

Eine verständliche Übersicht über die Steuerkanäle bietet dieses Dokument:    
https://www.ntiprit.gov.in/pdf/gsm2g/GSM_radio_Network_Concepts.pdf    

Ein Verkehrskanal kann entweder für den leitungsvermittelter Datendienst (Circuit Switched Data -> kurz: CSD) oder für den paketorientierten Datendienst (Packet Switched Data -> kurz: PSD) verwendet werden.    
- https://de.wikipedia.org/wiki/Circuit_Switched_Data
- https://de.wikipedia.org/wiki/Paketvermittlung

Der leitungsvermittelte Datendienst wird im 2G/GSM-Mobilfunknetzwerk für die Sprachtelefonie verwendet. Für Sprachtelefonie kommt immer die Leitungsvermittlung (Circuit Switch -> kurz: CS) zum Einsatz. 

Für die moderne Datenübertragung mit IP-Datenpakete und den modernen Internetanschluss (Mobile Daten) kommt im 2G/GSM-Mobilfunknetzwerk die paketorientierte Datenübertragung (Packet Switch -> kurz: PS) zum Einsatz.

CSD ist ein sehr ineffizienter Datendienst. Bei der Nutzung wird ein physikalischer Kanal während der Verbindung auch dann dauerhaft für einen Teilnehmer reserviert, wenn keine Daten gesendet oder empfangen werden. Deshalb sollte CSD nur für die Sprachtelefonie eingesetzt werden! 

Wieviele GSM-Funkkanäle eine Mobilfunkantenne gleichzeitig ausstrahlen soll, muss bei der Funknetzplanung mit einer Schätzung der Spitzenlast dieser Mobilfunkantenne festgelegt werden. Aus der Schätzung der Spitzenlast wird die Anzahl der erforderlichen Verkehrs- und Steuerkanäle für jede Mobilfunkantenne individuell berechnet. 
https://de.wikipedia.org/wiki/Spitzenlast   

Bei der Funknetzplanung ist zu definieren, welche "physikalische Kanäle" mit einem  Steuerkanal (CCH) belegt werden und welche "physikalische Kanäle" als Verkehrskanäle verwendet werden. 

Die Spezifikation ETSI GTS GSM 05.02, Version 5.1.0, Kapitel 6.4 - "Permitted channel combinations" definiert alle zulässigen Kombinationen von logischen Kanälen in einem physikalischen Kanal:    
https://www.etsi.org/deliver/etsi_gts/05/0502/05.01.00_60/gsmts_0502v050100p.pdf

In den Unterblöcken "timeslot" der OsmoBSC-Konfigurationsdatei muss die Belegung der Zeitschlitze (physikalische Kanäle) mit "logischen Kanälen" definiert werden. Die Zuweisung der logischen Kanälen zu den physikalischen Kanälen erfolgt über den Konfigurationsparameter "phys_chan_config".

Immer der erste GSM-Funkkanal enthält im ersten Zeitschlitz (TN0) den Rundfunk-Steuerkanal (Broadcast Control Channel -> BCCH).
- https://en.wikipedia.org/wiki/Broadcast_control_channel    
- https://de.wikipedia.org/wiki/Broadcast   

Gemäss dem Beitrag Nr. 7 unter:   

https://www.finetopix.com/showthread.php/10279-when-are-SDCCH-4-and-SDCCH-8-implemented?p=88696&viewfull=1#post88696   

genügt bei einer Mobilfunkantenne mit nur einem GSM-Funkkanal (one TRX cell) die Verwendung von einem einzigen physikalischen Kanal für alle Steuerkanäle. In diesem Fall ist im ersten Zeitschlitz (TN0) vom ersten GSM-Funkkanal (TRX 0) die Steuerkanalkombination:

    v)   FCCH + SCH + BCCH + CCCH + SDCCH/4(0...3) + SACCH/C4(0...3)

zu verwenden, was in der OsmoBSC-Konfigurationsdatei dem Eintrag:

    trx 0
     ...
     timeslot 0
      phys_chan_config CCCH+SDCCH4
     ...   

entspricht.

Verwendet die Mobilfunkantenne 2 bis 4 GSM-Funkkanäle gleichzeitig (two to four TRX cells), sollten 2 physikalische Kanäle auf dem ersten GSM-Funkkanal für die Übertragung der Steuerkanäle reserviert werden:

Zeitschlitz 0 (TN0) für den Rundfunk-Steuerkanal (BCCH) mit der bereits bekannten Steuerkanalkombination:

    v)   FCCH + SCH + BCCH + CCCH + SDCCH/4(0...3) + SACCH/C4(0...3)

und zusätzlich Zeitschlitz 1 (TN1) mit der neuen Steuerkanalkombination:

    vii) SDCCH/8(0 ..7) + SACCH/C8(0 .. 7)

Der Eintrag in der OsmoBSC-Konfigurationsdatei für diese Steuerkanalkonfiguration sieht wie folgt aus:

    trx 0
     ...
     timeslot 0
      phys_chan_config CCCH+SDCCH4
     timeslot 1
      phys_chan_config SDCCH8
     ...  

Alle verbliebenen physikalischen Kanäle sind als Verkehrskanäle zu definieren. Die verbliebenen physikalischen Kanäle sollten von OsmoBSC je nach Datenvekehrsaufkommen dynamisch zugeteilt werden. Wenn grosser Bedarf an Kapazität für die Sprachtelefonie besteht, werden diese Verkehrskanäle für die Sprachtelefonie verwendet. Ansonsten werden diese Verkehrskanäle für den paketorientierten Datendienst (PSD) eingesetzt.

    trx 0
     ...
     timeslot 2
      phys_chan_config TCH/F_PDCH
     timeslot 3
      phys_chan_config TCH/F_PDCH
     timeslot 4
      phys_chan_config TCH/F_PDCH
     timeslot 5
      phys_chan_config TCH/F_PDCH
     timeslot 6
      phys_chan_config TCH/F_PDCH
     ...            

Mindestens ein physikalischer Kanal auf dem ersten GSM-Funkkanal sollte fest für den paketorientierten Datendienst (PSD) reserviert werden. Dadurch wird verhindert, dass die Sprachtelefonie in einer Überlastsituation alle Verkehrskanäle beansprucht und der paketorientierte Datendienst (PSD) seinen Dienst mangels verfügbaren Verkehrskanälen einstellt.

    trx 0
     ...
     timeslot 7
      phys_chan_config PDCH

Die Verwendung von mehr als 4 GSM-Funkkanäle gleichzeitig auf derselben Mobilfunkantenne ist nicht zu empfehlen. Bei Bedarf an grossen Datenübertragungskapazitäten ist das eigene Mobilfunknetzwerk mit der 4. Generation des Mobilfunkstandards zu realisieren (4G/LTE). 4G/LTE bietet eine deutlich höhere spektrale Effizienz als 2G/GSM.   
- https://de.wikipedia.org/wiki/Spektrale_Effizienz    
- https://en.wikipedia.org/wiki/Spectral_efficiency#Comparison_table    

Zum Betreiben eines eigenen 4G/LTE-Mobilfunknetzwerks eignet sich die Software srsRAN. Eine ausführliche srsRAN-Anleitung zur Installation und Konfiguration eines eigenen 4G/LTE-Mobilfunknetzwerk ist hier erhältlich:

https://github.com/GrandDixence/CoverageMaps/blob/main/Eigenes_Mobilfunknetzwerk/Installations-Anleitung_srsRAN_Ubuntu_20.04.md

Die Ausstrahlung eines ungenutzen GSM-Funkkanal kann von der Mobilfunkantenne (BTS) zum Energiesparen abgeschaltet werden. Wenn diese Mobilfunkantenne auf Grund eines erhöhten Datenverkehrsvolumen wieder einen grösseren Bedarf an "physikalischen Kanälen" hat, schaltet die Mobilfunkantenne die Ausstrahlung vom vorgängig abgeschalteten GSM-Funkkanal wieder ein. 

Mit der Abschaltung eines GSM-Funkkanal verliert die Mobilfunkantenne 8 "physikalische Kanäle". Die Ausstrahlung vom GSM-Funkkanal mit dem Rundfunk-Steuerkanal (BCCH) darf die Mobilfunkantenne nie abschalten! 

Zum Energiesparen und um Funkstörungen zu vermeiden, verfügt das Mobilgerät und die Mobilfunkantenne (BTS) über eine automatische, adaptive Sendeleistungsregelung. Wie hier nachgelesen werden kann, funktioniert leider im 2G/GSM-Mobilfunknetzwerk diese Sendeleistungsregelung mehr schlecht als recht:   
https://www.bag.admin.ch/dam/bag/de/dokumente/str/nis/faktenblaetter-emf/faktenblatt-smartphone.pdf.download.pdf/faktenblatt%20smartphone%20d.pdf

Die Mobilfunkantenne darf die Sendeleistung vom GSM-Funkkanal mit dem Rundfunk-Steuerkanal (BCCH) nicht reduzieren. Der GSM-Funkkanal mit dem Rundfunk-Steuerkanal (BCCH) wird immer mit der vollen Sendeleistung ausgestrahlt!   
https://osmocom.org/projects/cellular-infrastructure/wiki/GsmPowerControl   

Mit dem Block "cs7" ist die SS7-Konfiguartion zu ergänzen. SS7 wird für die Kommunikation zwischen OsmoBSC und OsmoMSC auf der A-Schnittstelle verwendet. Den Konfigurationsparameter "msc-addr" im Block "msc 0" nicht vergessen! Die SS7-Konfiguration ist sehr komplex und fehleranfällig. Deshalb sei hier für die SS7-Konfiguration auf die ausführlichen Kapiteln zu SS7 in den offiziellen OsmoBSC-, OsmoSTP- und OsmoMSC-Bedienungsanleitung verwiesen.

Mit dem Konfigurationsparameter "allow-emergency" wird die Unterstützung von Notrufen im eigenen 2G/GSM-Mobilfunknetzwerk ausgeschaltet:

    msc 0
     ...
     allow-emergency deny
     ...


Wichtiger Hinweis zum Konfigurationsparameter "allow-emergency":

    Achtung:

    Das eigene 2G/GSM-Mobilfunknetzwerk unterstützt keine Notrufe
    ab Mobiltelefon mit fehlender SIM-Karte (Emergency call)!

Die Konfigurationsparametern "codec-support" und "codec-list" regeln die unterstützten Audiocodecs im eigenen 2G/GSM-Mobilfunknetzwerk:

    bts 0
     codec-support fr efr
    ...

    msc 0
     ...
     codec-list fr3

Es sollten im eigenen 2G/GSM-Mobilfunknetzwerk nur folgende Audiocodecs unterstützt werden:

Adaptive Multi-Rate (AMR-NB) -> "fr3"     
- https://de.wikipedia.org/wiki/Adaptive_Multi-Rate    
- https://en.wikipedia.org/wiki/Adaptive_Multi-Rate_audio_codec

GSM Enhanced Full Rate (EFR) -> "fr2"   
https://en.wikipedia.org/wiki/Enhanced_full_rate

GSM Full Rate (FR) -> "fr"    
https://en.wikipedia.org/wiki/Full_Rate

AMR-NB bietet immer die bestmögliche Sprachqualität im eigenen 2G/GSM-Mobilfunknetzwerk. FR und EFR sollte aus Kompatibilitätsgründen im eigenen 2G/GSM-Mobilfunknetzwerk unterstützt werden. 

Audiocodecs auf Basis von "Half Rate" (HR) bieten nur schlechtere Sprachqualität als ihre "Full Rate" (FR)-Pendanz. Deshalb solte im eigenen 2G/GSM-Mobilfunknetzwerk die Unterstützung von allen HR-Audiocodecs ausgeschaltet werden. AMR-NB und EFR bieten eine leicht bessere Sprachqualität als "GSM Full Rate" (FR). 
https://www.elektronik-kompendium.de/sites/kom/1402251.htm

Leider funktioniert in den installierten Versionen der OsmoCOM-Programme die Unterstützung von AMR-NB nicht korrekt. Deshalb wird die Verwendung von EFR konfiguriert.

Aus Sicherheitsgründen sollte die Software von OsmoCOM ohne root-Rechte gestartet werden. Deshalb ist der Ersatz aller offiziellen Systemd-Dienste durch eigene, selber erstellte Systemd-Dienste erforderlich.

    $ touch /etc/systemd/system/osmo-bsc_secure.service
    $ chown root:root /etc/systemd/system/osmo-bsc_secure.service
    $ chmod u=rw,g=r,o=r /etc/systemd/system/osmo-bsc_secure.service
    $ ls -alh /etc/systemd/system/osmo-bsc_secure.service
    -rw-r--r-- ... root root ... /etc/systemd/system/osmo-bsc_secure.service

Den eigenen, sicheren Systemd-Dienst editieren:

    $ nano /etc/systemd/system/osmo-bsc_secure.service

    [Unit]
    Description=Osmocom Base Station Controller (BSC)

    After=osmo-mgw_secure.service
    After=osmo-msc_secure.service
    After=osmo-stp_secure.service

    Wants=osmo-mgw_secure.service
    Wants=osmo-msc_secure.service
    Wants=osmo-stp_secure.service

    [Service]
    User=foo
    Group=foo
    Type=exec
    ExecStart=/usr/bin/osmo-bsc -s -c /etc/osmocom/osmo-bsc.cfg
    RestartSec=2
    Restart=always
    OOMPolicy=stop

    [Install]
    WantedBy=multi-user.target

Die Bedeutung der einzelnen Konfigurationsparametern kann in den entsprechenden Man-Pages nachgelesen werden:

    # man systemd.exec
    # man systemd.unit
    # man systemd.service

Den offiziellen Systemd-Dienst von OsmoBSC beenden und für immer ausschalten:

    $ systemctl status osmo-bsc.service
    $ systemctl stop osmo-bsc.service
    $ systemctl disable osmo-bsc.service
    $ systemctl mask osmo-bsc.service
    $ systemctl status osmo-bsc.service

Den eigenen, sicheren Systemd-Dienst einschalten und starten:

    $ systemctl daemon-reload
    $ systemctl status osmo-bsc_secure.service
    $ systemctl enable osmo-bsc_secure.service
    $ systemctl start osmo-bsc_secure.service
    $ systemctl status osmo-bsc_secure.service    

Die Ausgaben von OsmoBSC kontrollieren:
   
    $ journalctl -b -u osmo-bsc_secure.service  

#### OsmoTRX
Kommen wir zur Konfigurationsdatei von OsmoTRX:

    $ nano /etc/osmocom/osmo-trx-lms.cfg

    trx
     bind-ip 127.0.0.1
     remote-ip 127.0.0.1
     tx-sps 4
     rx-sps 4
     multi-arfcn disable
     swap-channels disable
     egprs disable
     chan 0
      rx-path LNAW
      tx-path BAND2
    log stderr
     logging filter all 1
     logging print category 1
     logging print category-hex 0
     logging print level 1
     logging timestamp 0
     logging level all notice      

Die Zeile "tx-sps" ist zu ergänzen und die Zeilen "rx-path" und "tx-path" müssen angepasst werden. 

Mit den Konfigurationsparametern "rx-path und "tx-path" müssen die korrekten Sende- und Empfangspfade vom LimeSDR Mini konfiguriert werden. Für Mobilfunkfrequenzbänder im Bereich von 2.0 GHz bis 3.5 GHz muss der:

    Empfangspfad: 	RX1_H	=> LNAH
    Sendepfad:		TX1_1	=> BAND1

verwendet werden. Mobilfunkfrequenzbänder > 3.5 GHz werden vom LimeSDR Mini nicht unterstützt. 

Für Mobilfunkfrequenzbänder im Bereich von 10 MHz bis 2 GHz muss der:

    Empfangspfad:   RX1_W	=> LNAW
    Sendepfad:      TX1_2	=> BAND2

verwendet werden.

- https://wiki.myriadrf.org/LimeSDR-Mini_v1.2_hardware_description#RF_Frequency_Range    
- https://gist.github.com/JamesHagerman/fafec6ee2ee076fe7cda4cf4dd74edd0#file-stslte-notes-md    
- https://github.com/srsran/srsRAN_4G/issues/437#issuecomment-586745598

Aus Sicherheitsgründen sollte die Software von OsmoCOM ohne root-Rechte gestartet werden. Deshalb ist der Ersatz aller offiziellen Systemd-Dienste durch eigene, selber erstellte Systemd-Dienste erforderlich.

    $ touch /etc/systemd/system/osmo-trx_secure.service
    $ chown root:root /etc/systemd/system/osmo-trx_secure.service
    $ chmod u=rw,g=r,o=r /etc/systemd/system/osmo-trx_secure.service
    $ ls -alh /etc/systemd/system/osmo-trx_secure.service
    -rw-r--r-- ... root root ... /etc/systemd/system/osmo-trx_secure.service

Den eigenen, sicheren Systemd-Dienst editieren:

    $ nano /etc/systemd/system/osmo-trx_secure.service

    [Unit]
    Description=Osmocom SDR BTS L1 Transceiver (LimeSuite backend)

    After=osmo-mgw_secure.service

    [Service]
    User=foo
    Group=foo
    Type=exec
    ExecStart=/usr/local/bin/osmo-trx-lms -C /etc/osmocom/osmo-trx-lms.cfg
    Restart=always
    RestartSec=2
    OOMPolicy=stop

    # Let it process messages quickly enough
    CPUSchedulingPolicy=rr
    CPUSchedulingPriority=4
    CPUSchedulingResetOnFork=false
    LimitMEMLOCK=1G

    [Install]
    WantedBy=multi-user.target

Die Bedeutung der einzelnen Konfigurationsparametern kann in den entsprechenden Man-Pages nachgelesen werden:

    # man systemd.exec
    # man systemd.unit
    # man systemd.service    

Den eigenen, sicheren Systemd-Dienst einschalten und starten:

    $ systemctl daemon-reload
    $ systemctl status osmo-trx_secure.service
    $ systemctl enable osmo-trx_secure.service
    $ systemctl start osmo-trx_secure.service
    $ systemctl status osmo-trx_secure.service    

Die Ausgaben von OsmoTRX kontrollieren:
   
    $ journalctl -b -u osmo-trx_secure.service    

#### OsmoBTS
Als nächstes packen wir die Konfigurationsdatei von OsmoBTS an:

    $ nano /etc/osmocom/osmo-bts.cfg

    phy 0
     instance 0
      osmotrx rx-gain 20
      osmotrx tx-attenuation 10
     osmotrx ip local 127.0.0.1
     osmotrx ip remote 127.0.0.1
    bts 0
     band 1800
     ipa unit-id 1800 1
     oml remote-ip 127.0.0.1
     trx 0
      phy 0 instance 0
      #rtp continuous-streaminmg
      #rtp port-range 14000 15999
    log stderr
     logging filter all 1
     logging print category 1
     logging print category-hex 0
     logging print level 1
     logging timestamp 0
     logging level all notice

Die Zeile "osmotrx tx-attenuation" ist zu ergänzen und die Zeile "osmotrx rx-gain" muss angepasst werden. Befindet sich die Mobilfunkantenne in einem Gebäude, so sollte die Verstärkung im Empfangspfad und die Verstärkung im Sendepfad reduziert werden. Ein von der SDR-Hardware zu stark empfangenes Mobilfunksignal wird mit einer "Clipping"-Warnung im Logbuch des Rechners notiert.

Je grösser der Wert vom Konfigurationsparameter:

     osmotrx tx-attenuation 

desto schwächer wird das Mobilfunksignal am Antennenanschluss der SDR-Hardware ausgegeben. Eine Abschwächung um 10 dB ist sinnvoll um eine Übersteuern zu vermeiden.    
https://de.wikipedia.org/wiki/%C3%9Cbersteuern_(Signalverarbeitung)

Je grösser der Wert vom Konfigurationsparameter:

     osmotrx rx-gain 

desto stärker wird das Mobilfunksignal von der SDR-Hardware empfangen. Der Wert vom Konfigurationsparameter rx-gain sollte nicht grösser als 40 dB sein, um ein Übersteuern zu vermeiden.
https://de.wikipedia.org/wiki/%C3%9Cbersteuern_(Signalverarbeitung)

In Gebäuden sollten der Wert vom Konfigurationsparameter rx-gain nicht grösser als 10 dB sein.

Mit diesen angepassten Zeilen wird die Signalverstärkung im Sende- und Empfangspfad der SDR-Hardware angepasst.

Allenfalls muss noch das zu verwendende Mobilfunkfrequenzband umkonfiguriert werden:

    band 900    => Band 8  ( 900 MHz FDD)
    band 1800   => Band 3  (1800 MHz FDD)
    band 850    => Band 5  ( 850 MHz FDD)
    band 1900   => Band 2  (1900 MHz FDD)

Zur Wahl des einzusetzenden Mobilfunkfrequenzband sind die Angaben im Kapitel "Funklizenz und funkregulatorische Anforderungen" zu beachten!

Aus Sicherheitsgründen sollte die Software von OsmoCOM ohne root-Rechte gestartet werden. Deshalb ist der Ersatz aller offiziellen Systemd-Dienste durch eigene, selber erstellte Systemd-Dienste erforderlich.

    $ touch /etc/systemd/system/osmo-bts_secure.service
    $ chown root:root /etc/systemd/system/osmo-bts_secure.service
    $ chmod u=rw,g=r,o=r /etc/systemd/system/osmo-bts_secure.service
    $ ls -alh /etc/systemd/system/osmo-bts_secure.service
    -rw-r--r-- ... root root ... /etc/systemd/system/osmo-bts_secure.service

Den eigenen, sicheren Systemd-Dienst editieren:

    $ nano /etc/systemd/system/osmo-bts_secure.service

    [Unit]
    Description=Osmocom osmo-bts for osmo-trx

    After=osmo-bsc_secure.service
    After=osmo-mgw_secure.service
    After=osmo-trx_secure.service

    Wants=osmo-bsc_secure.service
    Wants=osmo-mgw_secure.service
    Wants=osmo-trx_secure.service

    [Service]
    User=foo
    Group=foo
    Type=exec
    ExecStart=/usr/bin/osmo-bts-trx -s -c /etc/osmocom/osmo-bts.cfg
    Restart=always
    RestartSec=2
    OOMPolicy=stop

    # Let it process messages quickly enough
    CPUSchedulingPolicy=rr
    CPUSchedulingPriority=3
    CPUSchedulingResetOnFork=false
    LimitMEMLOCK=200M

    [Install]
    WantedBy=multi-user.target

Den offiziellen Systemd-Dienst von OsmoBTS beenden und für immer ausschalten:

    $ systemctl status osmo-bts.service
    $ systemctl stop osmo-bts.service
    $ systemctl disable osmo-bts.service
    $ systemctl mask osmo-bts.service
    $ systemctl status osmo-bts.service

Den eigenen, sicheren Systemd-Dienst einschalten und starten:

    $ systemctl daemon-reload
    $ systemctl status osmo-bts_secure.service
    $ systemctl enable osmo-bts_secure.service
    $ systemctl start osmo-bts_secure.service
    $ systemctl status osmo-bts_secure.service    

Die Ausgaben von OsmoBTS kontrollieren:

    $ journalctl -b -u osmo-bts_secure.service

#### Abschluss der Konfigurationsarbeiten
Rechner neustarten und kontrollieren, ob beim Rechnerstart automatisch die Programme von OsmoCOM gestartet werden.

### Diagnose
Fast alle Programme von OsmoCOM ermöglichen mit Telnet den unverschlüsselten Fernzugriff. Mit diesem Fernzugriff ermöglicht OsmoCOM die Diagnose und Fernwartung des eigenen 2G/GSM-Mobilfunknetzwerks.

Alle im Fernzugriff unterstützten Befehle auflisten:

    £ list

Der laufende Fernzugriff kann mit dem Befehl:

    £ exit

beendet werden.

#### OsmoHLR
Fernzugriff auf OsmoHLR starten (VTY):

    # telnet 127.0.0.1 4258

#### OsmoMGW
Fernzugriff auf OsmoMGW starten (VTY):

    # telnet 127.0.0.1 4243

Laufende RTP-Datenströme von laufenden Telefongespräche beobachten:

    £ show mgcp stats

#### OsmoSTP
Fernzugriff auf OsmoSTP starten (VTY):

    # telnet 127.0.0.1 4239

#### OsmoMSC
Fernzugriff auf OsmoMSC starten (VTY):

    # telnet 127.0.0.1 4254

Diverse Statistiken und Zähler:

    £ show rate-counters
    £ show statistics

Bestehende Verbindungen:

    £ show connection

Laufende Aktionen:

    £ show transaction

Aktuelle Länge der SMS-Versandwarteschlange:

    £ show sms-queue

Ein besonderes "Schmankerl" ist die Möglichkeit von stillen SMS und stillen Telefonanrufe. Stille SMS und stille Telefonanrufe können für die Diagnose eingesetzt werden.   
- https://nickvsnetworking.com/gsm-with-osmocom-silent-sms-silent-calls/   
- https://de.wikipedia.org/wiki/Schmankerl   

Stilles SMS an die Telefonnummer (MSISDN) 100 senden:

    $ subscriber msisdn 100 silent-sms sender msisdn 666 send lautlos

Stiller Anruf auf die Telefonnummer (MSISDN) 100 starten:

    $ subscriber msisdn 100 silent-call start any

Stille SMS werden gerne von Polizei und Geheimdiensten zur Personenverfolgung und Personenüberwachung eingesetzt.
https://de.wikipedia.org/wiki/Stille_SMS

#### OsmoBSC
Fernzugriff auf OsmoBSC starten (VTY):

    # telnet 127.0.0.1 4242

Diverse Statistiken und Zähler:

    £ show rate-counters
    £ show stats
    £ show statistics

Übersicht über die aktuelle Netzwerkverkehrslast:

    £ show network
    £ show paging    

Übersicht über den aktuellen Zustand der Mobilfunkantenne:

    £ show bts

Übersicht über die GSM-Funkkanäle:

    £ show trx
    £ show timeslot

Übersicht über alle laufenden Telefongespräche:

    £ show lchan summary
    £ assignment any
    $ show subscriber all

Details zu allen laufenden Telefongespräche:

    £ show conns

#### OsmoTRX
Fernzugriff auf OsmoTRX starten (VTY):

    # telnet 127.0.0.1 4237

Übersicht über die GSM-Funkkanäle:

    £ show trx

Diverse Statistiken und Zähler:

    £ show rate-counters
    £ show stats

#### OsmoBTS
Fernzugriff auf OsmoBTS starten (VTY):

    # telnet 127.0.0.1 4241

Übersicht über den aktuellen Zustand der Mobilfunkantenne:

    £ show bts 0
    £ show transceiver
    £ show phy

Übersicht über die GSM-Funkkanäle:

    £ show trx
    £ show timeslot
    £ show lchan summary
    £ show lchan

### Erster Testlauf
Beim ersten Testlauf sollten einige Aspekte kontrolliert werden.

#### Netzwerkverbindungen kontrollieren
Auf welchen IP-Adressen und Serverports horchen die OsmoCOM-Programme auf eingehende SCTP-, TCP- und UDP-Netzwerkverbindungen?  
- https://de.wikipedia.org/wiki/Stream_Control_Transmission_Protocol    
- https://de.wikipedia.org/wiki/Transmission_Control_Protocol    
- https://de.wikipedia.org/wiki/User_Datagram_Protocol     

Kontrolle mit root-Rechten:

    $ ss --sctp --udp --tcp --listening --numeric --process |grep -i osmo
    Netid 	State		Recv-Q		Send-Q		Local Adddress:Port		Peer Address:Port 		Process
    udp		UNCONN		...			...			127.0.0.2:2427			0.0.0.0:*				users:(("osmo-mgw", ...))
    udp		UNCONN		...			...			127.0.0.1:2427			0.0.0.0:*				users:(("osmo-mgw", ...))
    tcp		LISTEN		...			...			127.0.0.1:4249			0.0.0.0:*				users:(("osmo-bsc", ...))
    tcp		LISTEN		...			...			0.0.0.0:3002			0.0.0.0:*				users:(("osmo-bsc", ...))
    tcp		LISTEN		...			...			0.0.0.0:3003			0.0.0.0:*				users:(("osmo-bsc", ...))
    tcp		LISTEN		...			...			127.0.0.1:4222			0.0.0.0:*				users:(("osmo-hlr", ...))
    tcp		LISTEN		...			...			127.0.0.1:4254			0.0.0.0:*				users:(("osmo-msc", ...))
    tcp		LISTEN		...			...			127.0.0.1:4255			0.0.0.0:*				users:(("osmo-msc", ...))
    tcp		LISTEN		...			...			127.0.0.1:4258			0.0.0.0:*				users:(("osmo-hlr", ...))
    tcp		LISTEN		...			...			127.0.0.1:4259			0.0.0.0:*				users:(("osmo-hlr", ...))        
    tcp		LISTEN		...			...			127.0.0.1:4236			0.0.0.0:*				users:(("osmo-trx-lms", ...))
    tcp		LISTEN		...			...			127.0.0.1:4237			0.0.0.0:*				users:(("osmo-trx-lms", ...))    
    tcp		LISTEN		...			...			127.0.0.1:4238			0.0.0.0:*				users:(("osmo-bts-trx", ...))  
    tcp		LISTEN		...			...			127.0.0.1:4239			0.0.0.0:*				users:(("osmo-stp", ...))
    tcp		LISTEN		...			...			127.0.0.1:4241			0.0.0.0:*				users:(("osmo-bts-trx", ...))            
    tcp		LISTEN		...			...			127.0.0.1:4242			0.0.0.0:*				users:(("osmo-bsc", ...))  
    tcp		LISTEN		...			...			127.0.0.2:4243			0.0.0.0:*				users:(("osmo-mgw", ...))
    tcp		LISTEN		...			...			127.0.0.1:4243			0.0.0.0:*				users:(("osmo-mgw", ...))      
    tcp		LISTEN		...			...			0.0.0.0:2775			0.0.0.0:*				users:(("osmo-msc", ...))  
    sctp 	LISTEN		...			...		    127.0.0.1:2905		    0.0.0.0:*				users:(("osmo-stp", ...))

Kommen alle für den Betrieb des eigenen 2G/GSM-Mobilfunknetzwerks erforderlichen SCTP-Netzwerkverbindungen zustande?
  
    $ ss --sctp --numeric --process
    State		Recv-Q		Send-Q		Local Address:Port		Peer Address:Port		Process
    ESTAB		...			...			127.0.0.1:4302		    127.0.0.1:2905          users:(("osmo-bsc", ...)) 
    ...
    ESTAB	    0			0			127.0.0.1:4301		    127.0.0.1:2905		    users:(("osmo-msc", ...))
    ...

Kommen alle für den Betrieb des eigenen 2G/GSM-Mobilfunknetzwerks erforderlichen UDP-Netzwerkverbindungen zustande?

    $ ss --udp --numeric --process
    Recv-Q		Send-Q		Local Address:Port		Peer Address:Port		Process
    ...         ...         127.0.0.1:...           127.0.0.1:2427          users:(("osmo-msc", ...)) 
    ...         ...         127.0.0.1:5700          127.0.0.1:5800          users:(("osmo-trx-lms", ...))                      
    ...         ...         127.0.0.1:5701          127.0.0.1:5801          users:(("osmo-trx-lms", ...))  
    ...         ...         127.0.0.1:5702          127.0.0.1:5802          users:(("osmo-trx-lms", ...))
    ...         ...         127.0.0.1:...           127.0.0.2:2427          users:(("osmo-bsc", ...))
    ...         ...         127.0.0.1:5800          127.0.0.1:5700          users:(("osmo-bts-trx", ...))                      
    ...         ...         127.0.0.1:5801          127.0.0.1:5701          users:(("osmo-bts-trx", ...))  
    ...         ...         127.0.0.1:5802          127.0.0.1:5702          users:(("osmo-bts-trx", ...))    

Kommen alle für den Betrieb des eigenen 2G/GSM-Mobilfunknetzwerks erforderlichen TCP-Netzwerkverbindungen zustande?

    $ ss --tcp --numeric --process
    State		Recv-Q		Send-Q		Local Address:Port		Peer Address:Port		Process
    ESTAB       ...         ...         127.0.0.1:4222          127.0.0.1:...           users:(("osmo-hlr", ...))
    ESTAB       ...         ...         127.0.0.1:3002          127.0.0.1:...           users:(("osmo-bsc", ...))
    ESTAB       ...         ...         127.0.0.1:3003          127.0.0.1:...           users:(("osmo-bsc", ...))
    ESTAB       ...         ...         127.0.0.1:...           127.0.0.1:3002          users:(("osmo-bts-trx", ...))
    ESTAB       ...         ...         127.0.0.1:...           127.0.0.1:4222          users:(("osmo-msc", ...))
    ESTAB       ...         ...         127.0.0.1:...           127.0.0.1:3003          users:(("osmo-bts-trx", ...))    

Für die Kommunikation zwischen:

- BSC und MSC auf der Schnittstelle A 

wird SS7 mit dem Netzwerkprotokoll SCTP eingesetzt.    
https://de.wikipedia.org/wiki/Signalling_System_7   

Die Konfiguration von SS7 ist sehr komplex und sehr fehleranfällig. Deshalb müssen alle SS7-Verbindungen auf ihre korrekte Funktion geprüft werden. Eine SS7-Verbindungsaufbau wird an den Initiator der SS7-Verbindung mit der Meldung "RESET ACK" gemeldet. 

Wenn zum Beispiel das Programm OsmoBSC keine Meldung "RESET ACK" vom MSC erhält, ist die SS7-Verbindung zwischen OsmoBSC und OsmoMSC (A) nicht in einem funktionstüchtigen Zustand.

    $ journalctl -b -u osmo-bsc_secure.service |grep -i "RESET ACK from MSC"
    => ... osmo-bsc[...]: DMSC NOTICE ... RESET ACK from MSC: RI=SSN_PC,PC=0.23.1,SSN=BSSAP

Wenn eine SS7-Verbindung nicht funktioniert, muss die Konfiguration der beiden betroffenen OsmoCOM-Programme und von OsmoSTP kontrolliert werden. Fast alle SS7-Konfigurationsparametern befinden sich im Block "cs7". Folgende für die SS7-Konfiguration relevanten Konfigurationsparametern ausserhalb des Blocks "cs7" dürfen bei der Kontrolle nicht vergessen werden:

- OsmoBSC:  "msc-addr" im Block "msc 0"

Im Fehlerfall sollte mit Telnet auf der VTY-Schnittstelle der beiden betroffenen OsmoCOM-Programme die aktuelle SS7-Konfiguration und deren aktuellen Verbindungszustände geprüft werden:

Alle OsmoCOM-Programme mit Unterstützung für SS7-Verbindungen:

    £ show cs7 instance <Instanznummer> asp
    => ... ASP_ACTIVE ...

    £ show cs7 instance <Instanznummer> as m3ua
    => ... AS_ACTIVE ...

Nur OsmoSTP:

    $ show cs7 m3ua
    => xUA server for m3ua on 0.0.0.0:2905

    $ show cs7 instance <Instanznummer> route
    => ... as-rkm-3 ...
    => ... as-rkm-2 ...

#### Firewallregeln kontrollieren
Funktionieren die SCTP-Firewallregeln einwandfrei?

    $ conntrack -E |grep -i sctp
    ...
    [UPDATE] sctp ... ESTABLISHED src=127.0.0.1 dst=127.0.1.100 ... dport=36412 ... [ASURED]

#### Programmausgaben kontrollieren
Die Ausgaben aller OsmoCOM-Programme auf Fehlermeldungen kontrollieren:

    $ journalctl -b |grep -i osmo |grep -i FATAL
    => Keine Ausgabe

    $ journalctl -b |grep -i osmo |grep -i ERROR
    => Keine Ausgabe

    $ journalctl -b |grep -i osmo |grep -i NOTICE
    => Ausgaben untersuchen und allenfalls
       den Mangel mit einer Konfigurationsänderung
       beheben

#### Sende- und Empfangspfad kontrollieren       
In den Ausgaben vom Programm OsmoTRX ist zu kontrollieren, ob die korrekten Sende- und Empfangspfade vom LimeSDR Mini verwendet werden. Für Mobilfunkfrequenzbänder im Bereich von 2.0 GHz bis 3.5 GHz muss der:

    $ journalctl -u osmo-trx_secure.service |grep -i Antennas
    
    Empfangspfad:   RX1_H	=> OsmoTRX-Ausgabe: Rx antennas......... LNAH
    Sendepfad:      TX1_1	=> OsmoTRX-Ausgabe:	Tx antennas......... BAND1

verwendet werden. Mobilfunkfrequenzbänder > 3.5 GHz werden vom LimeSDR Mini nicht unterstützt. 

Für Mobilfunkfrequenzbänder im Bereich von 10 MHz bis 2 GHz muss der:

    Empfangspfad:   RX1_W	=> OsmoTRX-Ausgabe:  Rx antennas........ LNAW
    Sendepfad:      TX1_2	=> OsmoTRX-Ausgabe:	 Tx antennas........ BAND2

verwendet werden.

- https://wiki.myriadrf.org/LimeSDR-Mini_v1.2_hardware_description#RF_Frequency_Range  
- https://github.com/srsran/srsRAN_4G/issues/437#issuecomment-586745598

#### Kalibration vom SDR kontrollieren 
Häufig schlägt die Kalibration des Empfangspfad vom LimeSDR Mini mit der 
Fehlermeldung:

    $ journalctl -u osmo-trx_secure.service |grep -i calibrat

    ... Rx calibration: MCU error 5 (Loopback signal weak: not connected/insufficient gain?)

in den Ausgaben vom Programm OsmoTRX fehl. 

Gemäss dem Beitrag von Andrew Back am 11.11.2020 um 14:58 Uhr unter:    
https://discourse.myriadrf.org/t/srslte-and-limesdr-mini/6688/5  
ist die Ursache der fehlgeschlagenen Kalibrierung ein Programmierfehler in älteren Versionen der Lime Suite. 

Ab Version 20.10 der Lime Suite ist ein fehlerkorrigierter Kalibrieralgorithmus mit der CMake-Option "-DENABLE_NEW_GAIN_BEHAVIOUR=ON" erhältlich. Man beachte den ersten Hinweis (Notes 1.) in diesem Beitrag zur Veröffentlichung der Version 20.10 der Lime Suite:  
https://discourse.myriadrf.org/t/new-lime-suite-20-10-release/6660

TODO:
    
    TODO
    - Lime Suite mit der CMake-Option "-DENABLE_NEW_GAIN_BEHAVIOUR=ON" kompilieren und installieren

#### Mobilfunksignal mit SDR kontrollieren
Beim ersten Testlauf sollte kontrolliert werden, ob die funkregulatorischen Anforderungen erfüllt werden. Dazu muss mit einem zweiten SDR das vom LimeSDR Mini ausgestrahlte Mobilfunksignal geprüft werden. Eine kostengünstige Lösung für einen zweiten SDR ist der ADALM-PLUTO (PlutoSDR).    
- https://www.analog.com/en/resources/evaluation-hardware-and-software/evaluation-boards-kits/adalm-pluto.html#eb-overview
- https://wiki.analog.com/university/tools/pluto/users/firmware   
- https://wiki.analog.com/university/tools/pluto/drivers/windows#windows_drivers   
- https://www.rtl-sdr.com/plutosdr-quickstart-guide/   

Wird das Mobilfunksignal des eigenen 2G/GSM-Mobilfunksignals nur in einem Mobilfunkfrequenzband < 1000 MHz ausgestrahlt, kann als zweite SDR-Hardware der billigere RTL-SDR Blog V3 oder der RTL-SDR Blog V4 eingesetzt werden.   
- https://www.rtl-sdr.com/buy-rtl-sdr-dvb-t-dongles/   
- https://www.rtl-sdr.com/rtl-sdr-blog-v4-dongle-initial-release/   
- https://www.rtl-sdr.com/rtl-sdr-quick-start-guide/   
- https://www.rtl-sdr.com/manual-installation-of-sdr/   

Ich verbinde den zweiten SDR per USB-Kabel mit einem Laptop. Auf dem Laptop läuft das Betriebssystem MS Windows 10 und die SDR-Software "SDR Console":    
- https://www.sdr-radio.com/Console   
- https://www.sdr-radio.com/pluto   
- https://www.sdr-radio.com/rtl-dongles   

Befindet sich das vom LimeSDR Mini ausgestrahlte Mobilfunksignal im gewünschten Frequenzbereich? Ist die Signalspitze von jedem GSM-Funkkanal genau bei der konfigurierten Mittenfrequenz?

#### Teilnehmerdatenbank befüllen
Damit ein Mobiltelefon sich in das eigene 2G/GSM-Mobilfunknetzwerk einbuchen kann, muss die SIM-Kartennummer (IMSI) der im Mobiltelefon eingelegten SIM-Karte in der Teilnehmerdatenbank (HLR) vom eigenen 2G/GSM-Mobilfunknetzwerk hinterlegt sein.   
https://de.wikipedia.org/wiki/International_Mobile_Subscriber_Identity 

Leider unterstützt die installierte Version von OsmoHLR den Konfigurationsparameter "subscriber-create-on-demand" nicht. Dieser OsmoHLR-Konfigurationsparameter erlaubt das Einschalten der Funktion "Subscribers Create on Demand Feature". Mit dieser Funktion werden alle fremden SIM-Karten, mit welchen ein Mobiltelefon versucht, sich in das eigene 2G/GSM-Mobilfunknetzwerk einzubuchen, automatisch in der Teilnehmerdatenbank (HLR) gespeichert. 

Die Konfigurationskniffe, welche das Einbuchen von fremden SIM-Karten in das eigene 2G/GSM-Mobilfunknetzwerk erlauben, sind ausführlich im Kapitel 6.3 - "Configuring the Subscribers Create on Demand Feature" der OsmoHLR-Bedienungsanleitung beschrieben.

Ohne die Funktion "Subscribers Create on Demand Feature" müssen alle im eigenen 2G/GSM-Mobilfunknetzwerk zugelassenen SIM-Karten händisch in der Teilnehmerdatenbank eingetragen werden. 

Bevor die SIM-Karte in der Teilnehmerdatenbank registriert werden kann, muss deren SIM-Kartennummer (IMSI) festgestellt werden. Das geht am einfachsten über die Programmausgaben von OsmoHLR:

    $ journalctl -b -u osmo-hlr |grep -i "No such subscriber"
    => ... Cannot read subscriber from db: IMSI='228991234567890': No such subscriber

SIM-Kartennummer notieren. Als nächstes wird diese neue SIM-Karte in der Teilnehmerdatenbank (HLR) eingetragen. In der Teilnehmerdatenbank wird die SIM-Kartennummer (IMSI) eingetragen. Für die Sprachtelefonie sollte jeder in der Teilnehmerdatenbank eingetragenen SIM-Karte eine Telefonnummer (MSISDN) zugewiesen werden.   
https://de.wikipedia.org/wiki/Mobile_Subscriber_ISDN_Number   

Für die Pflege der Teilnehmerdatenbank (HLR) die VTY-Schnittstelle von OsmoHLR starten:

    # telnet 127.0.0.1 4258

Bearbeitungsmodus einschalten:

    £ enable

Neue SIM-Karte in der Teilnehmerdatenbank eintragen:

    £ subscriber imsi 228991234567890 create

Telefonnummer (MSISDN) der neuen SIM-Karte zuweisen. Im Beispiel wird der neuen SIM-Karte die Telefonnummer "100" zugewiesen:

    £ subscriber imsi 228991234567890 update msisdn 100

Achtung: 

    Achtung: 

    Eine gültige Telefonnummer (MSISDN) besteht aus mindestens 3 Ziffern!

Neuer Eintrag in der Teilnehmerdatenbank kontrollieren:

    £ subscriber imsi 228991234567890 show

Telnet-Sitzung zu OsmoHLR schliessen:

    £ exit

Mobiltelefon neustarten! Eventuell muss auch der Rechner neugestartet werden!

#### Einbuchen ins eigene Mobilfunknetzwerk testen
In den Einstellungen vom Mobiltelefon den "Nur 2G/GSM"-Modus einschalten. Mit dieser Einstellung wird das Mobiltelefon zum Einbuchen in ein 2G/GSM-Mobilfunknetzwerk gezwungen. Diese Einstellung befindet sich bei Smartphones mit dem Betriebssystem Android unter:

Einstellungen (Zahnrad) > Verbindungen > Mobile Netzwerke > Netzmodus 

In Ländern, wo mindestens ein öffentlicher Mobilfunkanbieter noch ein 2G/GSM-Mobilfunknetzwerk betreibt, muss mit der manuellen Netzwerkwahl das Mobiltelefon zum Einbuchen in das eigene 2G/GSM-Mobilfunknetzwerk forciert werden. Die manuelle Netzwerkwahl befindet sich auf Android-Smartphones unter:

- Einstellungen (Zahnrad) > Verbindungen > Mobile Netzwerke > Netzbetreiber
- Regler "Automatisch wählen" nach links schieben
- Unter "Verfügbare Netzwerke" das eigene 2G/GSM-Mobilfunknetzwerk auswählen

Mit dem USSD-Code:

    *#100#

die Telefonnummer (MSISDN) der SIM-Karte im eigenen 2G/GSM-Mobilfunknetzwerk abfragen.   
https://de.wikipedia.org/wiki/USSD-Codes

#### SMS-Versand testen
Mit OsmoMSC eine SMS an die neue SIM-Karte senden:

    # telnet 127.0.0.1 4254

    £ subscriber msisdn 100 sms sender msisdn 666 send Willkommen im eigenen 2G/GSM-Mobilfunknetzwerk!

Wenn sich das Mobiltelefon erfolgreich im eigenen 2G/GSM-Mobilfunknetzwerk anmelden kann, erhält es automatisch diese SMS vom OsmoMSC.

Zweites Mobiltelefon in der Teilnehmerdatenbank mit der Telefonnummer 200 registrieren.

SMS zwischen den beiden Mobiltelefone versenden. Zieladresse ist die Telefonnummer 100 respektive 200.

## Fehlende Schritte
In dieser Anleitung fehlen (noch) folgende Anweisungen:

TODO:

    TODO:
    - Sprachtelefonie zwischen zweier Mobiltelefone.

    - Ein eigener Benutzer ohne root-Rechte für jeden einzelnen Systemd-Dienste
      erstellen und alle selber erstellten Systemd-Dienste mit diesen neuen 
      Benutzern starten. Siehe dazu:

      https://superuser.com/questions/77617/how-can-i-create-a-non-login-user

    - Absichern der Systemd-Dienste mit einem Sandkasten (Sandbox). Siehe dazu:  

      https://www.redhat.com/sysadmin/mastering-systemd    

    - Möglichst viele horchende Serverports schliessen oder deren Zugriff mit
      Passwort absichern (VTY + Control interface). Diese unnötige horchende
      Serverports stellen ein Sicherheitsproblem dar.

      Änderung der Konfiguration der OsmoCOM-Programme während der Laufzeit
      verhindern.

    - Überwachung der CPU-Auslastung mit "htop". Siehe dazu:
    
      https://docs.srsran.com/projects/4g/en/latest/app_notes/source/pi4/source/index.html#running-the-pi4-enodeb

    - Damit die SDR-Hardware regelmässig kalibriert wird, sollte in der Nacht
      der Rechner neugestartet werden. Ein Neustart eines oder mehreren 
      Systemd-Dienste genügt nicht, da deren Zusammenspiel fragil ist.

      Regelmässige, automatische Rechnerneustarts (reboot) können mit einem
      Systemd-Timer und dem Befehl:

      $ shutdown -r 03:00 "Rechner-Neustart wird um 03:00 Uhr durchgeführt"

      ausgelöst werden.

    - Konfiguration und Installation des paketorientierten Datendienstes (PSD)
      mit EDGE.

    - Optimierung des eigenen 2G/GSM-Mobilfunknetzwerks mit den Angaben in den
      Bedienungsanleitungen aller OsmoCOM-Programmen.

    - IP-Routerkonfiguration für die Weiterleitung der Datenpakete aus dem 
      eigenen Mobilfunknetzwerk ins Internet. 
    
    - Firewallregeln für die Weiterleitung der Datenpakete aus dem eigenen 
      Mobilfunknetzwerk ins Internet.
    
    - "Traffic Shaper" für die Weiterleitung der Datenpakete aus dem eigenen
      Mobilfunknetzwerk ins Internet.

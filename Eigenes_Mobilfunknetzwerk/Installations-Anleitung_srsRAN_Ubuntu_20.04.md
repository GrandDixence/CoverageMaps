**Installations-Anleitung srsRAN - Ubuntu Server 20.04**
---

# Einleitung
Diese Anleitung beschreibt die Installation und Konfiguration von srsRAN für den Betrieb eines eigenen Mobilfunknetzwerks (Campus-Netzwerk). 

Wichtig:

    Achtung:

    Leider ist diese Anleitung unvollständig! Fehlende Anweisungen sind an der Kennzeichnung: "TODO" ersichtlich!

SrsRAN ermöglicht den Betrieb eines eigenen 4G/LTE-Mobilfunknetzwerks auf einem Raspberry Pi 4 mit einem LimeSDR Mini. Der Raspberry Pi 4 kann mit einer USB-Powerbank ab "Batterie" betrieben werden.

Diese Anleitung gilt für Ubuntu Server 20.04.

Eine gute Übersicht über die Struktur eines 4G/LTE-Mobilfunknetzwerks bietet:
https://open5gs.org/open5gs/assets/images/Open5GS_CUPS-01.pdf

## Lesehinweise
\#  Befehlseingabe im Terminal (Bash) mit normalen Benutzerrechten (Benutzer: foo). Keine root-/Administratorrechte!

\$  Befehlseingabe im Terminal (Bash) als Benutzer root. Mit Administratorrechten!
    
... Platzhalter für diverse Angaben

:=  Zuweisung des Werts von der rechten Texthälfte an die Variable in der linken Texthälfte.
		
z.B. 	zum Beispiel

## Referenzen
Diese Anleitung basiert auf den Angaben von folgenden Webseiten:

- Offizielle srsRAN-Installationsanleitung  
https://docs.srsran.com/projects/4g/en/latest/general/source/1_installation.html

- srsRAN 4G on Raspberry Pi 4  
https://docs.srsran.com/projects/4g/en/latest/app_notes/source/pi4/source/index.html

- cyberlogint - Build a LTE Network with srsLTE and Program Your Own USIM Card  
https://cyberloginit.com/2018/05/03/build-a-lte-network-with-srslte-and-program-your-own-usim-card.html

# Hardwarevoraussetzungen
Für den Betrieb eines 4G/LTE-Mobilfunknetzwerks mit einem SDR muss die eingesetzte SDR-Hardware das gleichzeitige Senden und Empfangen von Funksignalen (Full Duplex) unterstützten. Weiter muss die SDR-Hardware den Funkbetrieb mit Zeitstempeln (timestamping) unterstützen. 

Aktuell die billigste SDR-Hardware welche den Funkbetrieb mit "Full Duplex" und "Timestamping" unterstützt, ist der LimeSDR Mini. 

In naher Zukunft könnte der ADALM-PLUTO (PlutoSDR) den LimeSDR Mini als die billigste SDR-Hardware mit "Full Duplex"- und Timestamping-Unterstützung ablösen. Aktuell fehlt aber dem PlutoSDR noch die Timestamping-Unterstützung im offiziellen Quellcode der Software (SoapySDR)! Siehe dazu:  
https://www.quantulum.co.uk/blog/private-lte-with-analog-adalm-pluto/

Je mehr Rechenleistung der eingesetzte Rechner aufweist, desto mehr Bandbreite kann der SDR für den Betrieb des eigenen 4G/LTE-Mobilfunknetzwerks einsetzen.

Die maximal erreichbare Datenübertragungsrate mit der konfigurierten Bandbreite des Mobilfunksignals kann mit der Webseite:
https://www.cellmapper.net/4G-speed  
berechnet werden.

- Bandbreite:  		      3 MHz 		(15 physikalische Ressourcenblöcke => PRB)
- Downlink Modulation:  64 QAM 		(bei gutem Empfang realistisch)
- Uplink Modulation: 	  16 QA   	(bei gutem Empfang realistisch)
- MIMO: 				        1x1 SiSO 	(nur 1x Sendeantenne und 1x Empfangsantenne)

Die Anzahl physikalische Ressourcenblöcke (PRB) können mit der Webseite:  
https://www.rfwireless-world.com/calculators/LTE-Resource-Block-Calculator.html  
berechnet werden.

Leider ist die Rechenleistung vom Raspberry Pi 4 sehr limitiert. Deshalb gilt zu beachten:

    Achtung:
    
    Gemäss den Angaben unter:
    https://docs.srsran.com/projects/4g/en/latest/app_notes/source/hw_packs/source/index.html?#package-1
    
    ist das mit dem Raspberry Pi 4 betriebene, eigene 4G/LTE-Mobilfunknetzwerk auf eine Bandbreite von 5 MHz beschränkt!

Als Mobilfunkantennen reichen im Testbetrieb die im Lieferumfang des LimeSDR-Mini enthaltenen zwei "duck antennas". 

Beim gleichzeitigen Betrieb von zweien Mobilfunkantennen, ist darauf zu achten, dass die Ausrichtung der Sendeantenne in einem Winkel von 90° verschoben zur Ausrichtung der Empfangsantenne erfolgt. Damit wird eine Kreuzpolarisation erzeugt, welche die Störeinflüsse der Sendeantenne auf die Empfangsantenne minimiert.

Für den Dauerbetrieb sollte eine einzige, professionelle Mobilfunkantenne verwendet werden. Mobilfunkantennen für den professionellen Einsatz sind zum Beispiel beim Lieferanten FTH Henning:  
https://www.fts-hennig.de/Antennen/  
erhältlich.

## Langzeitbetrieb
Soll die SDR-Hardware im Langzeitbetrieb eingesetzt werden, müssen weitere Hardwarevoraussetzungen erfüllt werden. Unter Langzeitbetrieb wird der unterbruchsfreie Einsatz über eine Zeitdauer von mehreren Tagen (24h/7 Tage-Betrieb) verstanden.

USB-Verbindungen sind nicht für den Langzeitbetrieb geeignet. Deshalb sollte zwischen der Rechnerhardware (z.B. Raspberry Pi) und der SDR-Hardware eine langzeitstabile Kommunikationsverbindung realisiert werden. 

Spezialrechnerhardware bieten Bussysteme an, über welche die Kommunikation zwischen SDR und Rechner langzeitstabil abgewickelt werden kann. Soll kein Spezialrechner eingesetzt werden, muss mit Gigabit-Ethernet-Netzwerkkabel eine langzeitstabile Netzwerkverbindung zwischen SDR und Rechner realisiert werden.

Der Einsatz eines Ethernet-Netzwerkkabels hat den Vorteil, dass für die Rechnerhardware kostengünstige COTS-Hardware eingesetzt werden kann. Und die Rechnerhardware relativ leicht durch eine neuere, leistungsfähigere Rechnerhardware ersetzt werden kann.

Für den Betrieb an einem SDR geeignete Spezialrechnerhardware ist vom Hersteller Ettus als USRP E313 erhältlich:  
https://www.ettus.com/product-categories/usrp-embedded-series/  
https://www.ettus.com/wp-content/uploads/2019/01/USRP_E313_Datasheet-1.pdf

## Anforderungen an die Frequenz- und Phasensynchronisation der SDR-Hardware
Mobilfunknetzwerke stellen hohe Anforderungen an die Frequenz- und Phasensynchronisation des im Downlink ausgestrahlten Funksignals. Üblich sind zeitliche Genauigkeiten unter 10 μs. 

Um diese Anforderungen an die Synchronisation des ausgestrahlten Funksignals zu erfüllen, muss der SDR mit einer sehr genauen Zeitbasis, wie einer Atomuhr synchronisiert sein. 

Fehlt die Takt- und Frequenzsynchronisation vom SDR zu einer Atomuhr, führt dies zu massiven Performanceeinbussen. Diese Performanceeinbussen sind in Form von erheblich reduzierten erreichbaren Datenübertragungsraten im eigenen Mobilfunknetzwerk spürbar und messbar.

Hochwertige SDR-Hardware verfügt über einen Anschluss zum Einspeisen eines externen Taktsignals über eine mechanisch robuste N- oder SMA-Steckverbindung. Dieser Anschluss wird häufig mit "EXT CLOCK" bezeichnet.

Leider ist der LimeSDR-Mini kein hochwertiger SDR. Der LimeSDR-Mini verfügt zwar über einen mit einer mechanisch ungenügend robusten U.FL-Steckverbindung 
ausgerüsteten "EXT CLOCK"-Anschluss auf der Leiterplatte. Aber zur Nutzung von diesem "EXT CLOCK"-Anschluss müssen auf der Leiterplatte vom LimeSDR-Mini einige Nullohmwiderstände umgelöstet werden!  
https://wiki.myriadrf.org/LimeSDR-Mini_v1.1_hardware_description  
https://discourse.myriadrf.org/t/enabling-external-clock-on-limesdr-mini-1-2/3066

Auch der ADALM-PLUTO (PlutoSDR) ist kein hochwertiger SDR. Neuere Varianten vom PlutoSDR verfügen über einen "EXT CLOCK"-Anschluss über eine mechanisch 
ungenügend robuste U.FL-Steckverbindung. Jedoch ist diese U.FL-Steckerverbindung nur innerhalb des PlutoSDR-Gehäuse erreichbar. Deshalb muss das Gehäuse vom 
PlutoSDR geöffnet oder modifiziert werden, damit ein externes Taktsignal in den PlutoSDR eingespiesen werden kann.  
https://wiki.amsat-dl.org/doku.php?id=de:tricks:pluto_extclk&do=export_pdf  
https://wiki.amsat-dl.org/doku.php?id=en:tricks:pluto_extclk&do=export_pdf  
https://www.youtube.com/watch?v=o2IeXpsOQig

Die Synchronisation des SDR erfolgt in der Regel über einen GPSDO. Die im Aussenbereich montierte GNSS-Empfangsantenne leitet das vom GNSS-Satelliten empfangene, hochgenaue Atomuhr-Zeitsignal über ein langes Antennenkabel zum im Innenbereich montierten GPSDO.

Der GPSDO wandelt das GPS-Signal in ein SDR-kompatibles Taktsignal um. Einige SDR-Hardware erwartet ein externes Taktsignal als Rechtecksignal mit einer Taktfrequenz von 10 MHz. Andere SDR-Hardware erwartet ein externes Taktsignal als Rechtecksignal mit einer deutlich höheren Taktfrequenz. Vom GPSDO wird das externe Taktsignal über ein Antennenkabel über den "EXT CLOCK"-Anschluss dem SDR zugeführt.

In der Regel wird im GPSDO mit dem Zeitsignal vom GNSS-Satelliten ein lokaler Quarzoszillator synchronisiert. Dieses Synchronisationsverfahren wird GPSDO genannt. Beim Ausfall des GNSS-Zeitsignals läuft dieser Quarzoszillator frei und ist alleinig für die Frequenz- und Phasensynchronisation des Funksenders zuständig.

Ob der Quarzoszillator als temperaturstabilisierter Quarzoszillator (TCXO), mit einem Quarzofen (OCXO) oder als Rubidium-Oszillator realisiert wird, hängt von den Anforderungen ab, wie lange das Mobilfunknetzwerk ohne externe Synchronisation betrieben werden soll. Rubidium-Oszillatoren sind langfristig stabiler und genauer als Quarzoszillatoren.

Der Hersteller Leo Bodnar bietet einige SDR-kompatible GPSDO an:  
https://www.leobodnar.com/shop/index.php?main_page=index&cPath=107

Einige vom Hersteller Leo Bodnar angebotene SDR-kompatible GPSDO können über ein USB-Kabel von einer USB-Powerbank mit Strom versorgt werden. 

Dieses informative Youtube-Video zeigt auf, wie ein PlutoSDR mit dem GPSDO "Mini Precision GPS Reference Clock" vom Hersteller Leo Bodnar über GPS-Satellitenmit dem Zeitsignal einer Atomuhr synchronisiert wird:  
https://www.youtube.com/watch?v=o2IeXpsOQig

Auch für andere SDR-Hardware ist dieses Youtube-Video informativ. In diesem Video wird die Konfiguration der Frequenz des vom GPSDO ausgegebenen Taktsignal aufgezeigt.

## Funklizenz und funkregulatorische Anforderungen
Zum Betrieb eines eigenen Mobilfunknetzwerk ist eine entsprechende Funklizenz von der nationalen Funkaufsichtbehörde erforderlich. In der Schweiz ist dafür das BAKOM zuständig. Das BAKOM gestattet gegen eine ordentliche jährliche Gebühr die Nutzung des Mobilfunkfrequenzband 78 (3.5 GHz) für private Campus-Netze.  
https://www.bakom.admin.ch/bakom/de/home/das-bakom/medieninformationen/bakom-infomailing/infomailing-61/realisierung-privater-lokaler-mobilfunknetze-in-der-schweiz-ab-1-januar-2024-moglich.html  
https://www.bakom.admin.ch/bakom/de/home/frequenzen-antennen/frequenznutzung-mit-oder-ohne-konzessionen/privatelokalemobilfunknetze.html

Eine Übersicht über alle in der Schweiz für Mobilfunknetzwerke nutzbaren Mobilfunkfrequenzbändern bietet die Webseite:  
https://de.wikipedia.org/wiki/Mobilfunkfrequenzen_in_der_Schweiz

Mit der Funklizenz verknüpft sind funkregulatorische Anforderungen. Insbesondere darf der Funksender keine anderen Funkanwendungen stören. Deshalb ist für jeden eingesetzten SDR im Sendepfad zwischen dem Leistungsverstärker (Power Amplifier -> kurz: PA) und der Mobilfunkantennne ein sehr gut filternder Bandpass zwingend erforderlich. Die funkregulatorischen Anforderungen definieren die genauen Anforderungen an diesen Bandpass.  
https://de.wikipedia.org/wiki/Bandpass

Da die SDR-Hardwaree über ihre Sendeeinheit(en) üblicherweise nur ein sehr schwaches Funksignal im ein- oder zweistelligen Milliwatt-Bereich liefert, muss dieses (Mobil-)Funksignal mit einem Leistungsverstärker (PA) verstärkt werden.

Wie stark das Mobilfunksignal mit dem Leistungsverstärker (PA) verstärkt werden darf, ist der Funklizenz zu entnehmen.

Abhängig vom eingesetzten Mobilfunkband muss im Funksender der Sende- und Empfangspfad mit einem Diplexer oder einem Duplexer zwischen Mobilfunkantenne und SDR getrennt werden. Jeder SDR verfügt über einen Eingang für das empfangene Funksignal und über einen Ausgang für das zu versendende Funksignal. 

In einigen wenigen SDR erfolgt die Trennung von Sende- und Empfangspfad erst auf der Leiterplatine des SDR.  
https://de.wikipedia.org/wiki/Duplex_(Nachrichtentechnik)

Teurere SDR-Hardware kann gleichzeitig über mehrere Eingänge und Ausgänge Funksignale empfangen und senden. Eine solcher SDR ist für den MIMO-Betrieb erforderlich.  
https://de.wikipedia.org/wiki/MIMO_(Nachrichtentechnik)

Ein Überblick über die einzelnen Kompoonenten einer mit SDR-Hardware realisierten Mobilfunkantenne bietet Seite 7 von diesem Foliensatz:  
https://osmocom.org/attachments/download/2616/SDR%20based%20BTS.pdf

Wenn Zeitduplex (TDD) eingesetzt wird, erfolgt die Trennung von Sende- und Empfangspfad mit einem Duplexer.   
https://de.wikipedia.org/wiki/Duplexer

Wenn Frequenzduplex (FDD) eingesetzt wird, erfolgt die Trennung von Sende- und Empfangspfad mit einem Diplexer. Beim Einsatz eines Diplexer wird der aus funkregulatorischen Anforderungen erforderliche Bandpass üblicherweise im Diplexer realisiert.  
https://de.wikipedia.org/wiki/Diplexer

Mit SDR-Hardware sind eigene Mobilfunknetzwerke mit Frequenzduplex (FDD) deutlich einfacher zu realisieren als mit Zeitduplex (TDD). Deshalb sollte mit SDR-Hardware nur Campus-Netze in FDD-Mobilfunkfrequenzbändern betrieben werden.

Hier eine Übersicht über alle 4G/LTE-Mobilfunkfrequenzbändern weltweit:  
https://en.wikipedia.org/wiki/LTE_frequency_bands  
https://www.sqimway.com/lte_band.php

Hinweis:  Im englischen Sprachraum ist keine Unterscheidung zwischen Diplexer und Duplexer üblich. Beide werden schlicht: "Duplexer" genannt.

Diplexer sind zum Beispiel beim deutschen Hersteller Sysmocom erhältlich.  
https://shop.sysmocom.de/RF/Duplexers/

Diplexer werden üblicherweise "massgeschneidert" für jede Mobilfunkantenne gefertigt. Damit der Hersteller den für diese Mobilfunkantenne bestellten 
Diplexer auf diese Mobilfunkantenne massgeschneidert liefern kann, muss der Besteller bei der Diplexer-Bestellung die beiden gewünschten Grenzfrequenzen 
angeben. Der Besteller entnimmt die Angaben der beiden Grenzfrequenzen seiner Funklizenz.  
https://de.wikipedia.org/wiki/Grenzfrequenz


# Hinweise zur IT-Security
    Sicherheitswarnung:
     
    Für die zu installierenden Softwarepakete:
    
    liblimesuite20.01-1
    libsoapysdr0.7
    soapysdr0.7-module-lms7
    soapysdr-module-lms7
    soapysdr-tools
    libsoapysdr-dev
    limesuite
    python3-soapysdr 
    libwxgtk3.0-gtk3-0v5
    libwxbase3.0-0v5
    libglew2.1
    libfltk1.3
    limesuite-udev
    libmbedtls-dev
    libmbedcrypto3
    libmbedtls12
    libmbedx509-0
    libconfig++-dev
    libconfig9
    libconfig-dev
    libconfig++9v5
    
    gewährleistet der Hersteller Canonical keine Sicherheitsupdates!

Für diese Softwarepakete muss regelmässig (1x pro Woche) auf der Webseite:  
https://ubuntu.com/security/cves  
kontrolliert werden, welche bekannte Sicherheitslücken diese Softwarepakete enthalten. Allenfalls müssen diese Softwarepakete aus Sicherheitsgründen deinstalliert werden!

    Sicherheitswarnung:
    
    Für die aus dem Quellcode übersetzte Software
    
    srsENB (Teil von srsRAN_4G)
    srsEPC (Teil von srsRAN_4G)
    
    stellt der Hersteller Canonical keine Sicherheitsupdates zur Verfügung!

Für diese Software muss regelmässig (1x pro Woche) auf der Webseite:  
https://github.com/srsran/srsRAN_4G/security  
kontrolliert werden, ob diese Software bekannte Sicherheitslücken enthält. Wenn ja, muss die von der Sicherheitslücke betroffene Software aus Sicherheitsgründen ausser Betrieb genommen werden oder aktualisiert werden!

# Umkonfiguraton des Betriebssystems
Hinweise, Tricks und Tipps zum Einsatz eines Raspberry Pi 4 bieten die Beiträge unter:  
https://www.lancom-forum.de/fragen-zur-lancom-systems-routern-und-gateways-f41/vdsl-umzug-glasfaser-neuer-router-t17926.html#p101750

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
    $ apt install bsdutils
    $ apt install findutils
    $ apt install grep
    $ apt install conntrack

Anzahl der nicht mit Sicherheitsupdates versorgten Softwarepakete feststellen:

    $ pro security-status
    548 packages installed:
      539 packages from Ubuntu Main/Restricted repository
      9 packages from Ubuntu Universe/Multiverse repository
	
Nicht mit Sicherheitsupdates versorgte Softwarepakete entstammen dem Paketdepot (Repository) Universe oder Multiverse!

Damit srsRAN korrekt funktioniert, muss die automatische Drosselung der CPU-Taktfrequenz ausgeschaltet werden. Dadurch steigt der Stromverbrauch des Raspberry Pi massiv an!

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

Die Firewallkonfiguration ist so anzupassen, dass auf der Loopback-Netzwerkschnittstelle Netzwerkverbindungen mit dem Netzwerkprotokoll SCTP gestattet sind.  
https://de.wikipedia.org/wiki/Stream_Control_Transmission_Protocol  
https://www.ionos.de/digitalguide/server/knowhow/sctp-stream-control-transmission-protocol/

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

Überflüssige Echtzeitanwendungen ausschalten:

    $ systemctl status multipathd.service
    $ systemctl stop multipathd.service
    $ systemctl disable multipathd.service
    $ systemctl mask multipathd.service

TODO:

    TODO:

    - Vermeidung der mlockall-Fehlermeldung beim Start von srsENB. Siehe dazu:
    https://github.com/srsran/srsRAN_4G/issues/881#issuecomment-1247971384

# Softwareinstallation und Konfiguration

## Installation von SoapySDR
SoapySDR ermöglicht Programmen die herstellerunabhängige Ansteuerung der SDR-Hardware.  
https://github.com/pothosware/SoapySDR/wiki

SrsRAN nutzt SoapySDR für die Ansteuerung des LimeSDR Mini.

SoapySDR installieren:

    $ apt install soapysdr-module-lms7
    $ apt install soapysdr-tools
    $ apt install libsoapysdr-dev


## Installation von LimeSuite
Die LimeSuite ist eine Softwaresammlung für die Ansteuerung des LimeSDR Mini. LimeSuite ermöglicht das Einspielen von Firmware-Updates in den LimeSDR Mini.  
https://wiki.myriadrf.org/Lime_Suite

Bei der vorgängigen Installation vom Softwarepaket soapysdr-module-lms7 wurden als Abhängigkeiten bereits Softwarepakete mit Bestandteilen der LimeSuite installiert.

Komplette LimeSuite installieren:

    $ apt install limesuite

udev-Regel für den LimeSDR Mini installieren:

    $ apt install limesuite-udev
    $ reboot

Installation der LimeSuite kontrollieren:

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

Die Ansteuerung des LimeSDR Mini kontrollieren:

    # SoapySDRUtil --info
    Module found: .../libLMS7Support.so (...)
    Available factories... lime


    # SoapySDRUtil --find
      driver = lime
      label = LimeSDR Mini [USB 3.0] ...
      media = USB 3.0
      module = FT601
      name = LimeSDR Mini


    # SoapySDRUtil --make
    Make device
    [INFO] Make connection: 'LimeSDR Mini [USB 3.0] ...'
    [INFO] Reference clock: 40.00 MHz
    [INFO] Device name: LimeSDR-Mini
    [INFO] Reference: 40 MHz
    [INFO] LMS7002M register cache: Disabled
      driver=FT601
      hardware=LimeSDR-Mini


    # SoapySDRUtil --probe |more
      -- Peripheral summary
      Timestamps: YES
      
      -- RX Channel 0
      Full-duplex: YES
      
      -- TX Channel 0
      Full-duplex: YES

## srsRAN

### Installation von srsRAN
SrsRAN ermöglicht den Betrieb eines eigenen 4G/LTE-Mobilfunknetzwerks mit Ansteuerung der SDR-Hardware über SoapySDR.

Es sind gemäss der offiziellen Installationsanleitung von srsRAN:  
https://docs.srsran.com/projects/4g/en/latest/general/source/1_installation.html#package-installation  

fixfertige Softwarepakete für Ubuntu Server 20.04 vom Hersteller "Software Radio Systems" erhältlich.  
https://srs.io/ 

Jedoch sollte aus Sicherheitsgründen srsRAN immer aus dem Quellcode kompiliert werden! 

    $ apt install build-essential
    $ apt install git
    $ apt install cmake
    $ apt install pkg-config

    $ apt install libfftw3-dev
    $ apt install libmbedtls-dev
    $ apt install libboost-program-options-dev
    $ apt install libsctp-dev
    $ apt install libconfig++-dev

Achtung: Weitere Schritte ohne root-Rechte!

    # cd ~
    # git clone https://github.com/srsran/srsRAN_4G.git
    # cd srsRAN_4G
    # git checkout tags/release_23_11
    # mkdir build 
    # cd build/
    # cmake ../
    # make -j$(nproc)
    => Einige Stunden warten! Der Kompiliervorgang dauert mehrere Stunden!

Wenn der Kompiliervorgang abstürzt, einfach den Kompiliervorgang erneut starten. Für den Raspberry Pi 4 wird unter Ubuntu 20.04 nicht der offizielle Linux-Kernel verwendet.
Dies führt dank dem "tainted" Linux-Kernel zu Stabilitätsproblemen.  
https://www.kernel.org/doc/html/latest/admin-guide/tainted-kernels.html

TODO:

    TODO:

    - Kompilierung von srsRAN mit der Option "-DUSE_LTE_RATES" zur Vermeidung der srsRAN-Meldung:
    
    "Detected LimeSDR. Consider using LTE rates for better RF performance.  
    Either compile with '-DUSE_LTE_RATES=True' or start srsENB/srsUE with '--expert.lte_sample_rates=true'"


Wurde das für den Betrieb der Mobilfunkantenne (eNodeB; kurz eNB) des eigenen Mobilfunknetzwerks erfoderliche Programm srsENB erfolgreich kompiliert?

    # ls -alh ~/srsRAN_4G/build/srsenb/srs/srsenb
    -rwx------- 1 foo foo 20M ... /home/foo/srsRAN_4G/build/srsenb/src/srsenb


    # ~/srsRAN_4G/build/srsenb/srs/srsenb --version
    Active RF plugins:  libsrsran_rf_soapy.so
    Inactive RF plugins:
    --- Software Radio Systems LTE eNodeB ---

    Version 23.4.0

Wurde das für den Betrieb des Kernnetzwerks (Evolved Packet Core; kurz EPC) vom eigenen Mobilfunknetzwerk erfoderliche Programm srsEPC erfolgreich kompiliert?

    # ls -alh ~/srsRAN_4G/build/srsepc/srs/srsepc
    -rwx------- 1 foo foo 3.4M ... /home/foo/srsRAN_4G/build/srsepc/src/srsepc


    # ~/srsRAN_4G/build/srsepc/srs/srsepc --version

    Built in Release mode using commit eea87b1d8 on branch HEAD.


    --- Software Radio Systems EPC ---

    Couldn't open ...

Die Beispiele der Konfigurationsdateien suchen:

    # find ~/srsRAN -type f |grep -i conf.example
    # find ~/srsRAN -type f |grep -i csv.example

Die Beispiele der Konfigurationsdatei kopieren:

    # mkdir ~/srsRAN_4G/config

    # cp ~/srsRAN_4G/srsenb/enb.conf.example ~/srsRAN_4G/config/
    # cp ~/srsRAN_4G/srsenb/sib.conf.example ~/srsRAN_4G/config/
    # cp ~/srsRAN_4G/srsenb/rr.conf.example ~/srsRAN_4G/config/
    # cp ~/srsRAN_4G/srsenb/rb.conf.example ~/srsRAN_4G/config/

    # cp ~/srsRAN_4G/srsepc/epc.conf.example ~/srsRAN_4G/config/
    # cp ~/srsRAN_4G/srsepc/user_db.csv.example ~/srsRAN_4G/config/

    # cd ~/srsRAN_4G/config/

    # cp ./enb.conf.example enb.conf
    # cp ./sib.conf.example sib.conf
    # cp ./rr.conf.example rr.conf
    # cp ./rb.conf.example rb.conf

    # cp ./epc.conf.example epc.conf
    # cp ./user_db.csv.example user_db.csv

Kontrollieren, ob alle Konfigurationsparametern in der Beispielskonfigurationsdatei enb.conf.example mit den Angaben von:

    # ~/srsRAN_4G/build/srsenb/srs/srsenb --help |more

vollständig übereinstimmen.

Kontrollieren, ob alle Konfigurationsparametern in der Beispielskonfigurationsdatei epc.conf.example mit den Angaben von:

    # ~/srsRAN_4G/build/srsepc/srs/srsepc --help |more

vollständig übereinstimmen.

### eNodeB konfigurieren
--------------------------------------------------------------------------------
Konfigurationsdatei der Mobilfunkantenne (eNodeB; kurz: eNB) vom eigenen Mobilfunknetzwerk anpassen:  
https://en.wikipedia.org/wiki/ENodeB  
https://en.wikipedia.org/wiki/E-UTRA  
https://docs.srsran.com/projects/4g/en/latest/usermanuals/source/srsenb/source/1_enb_intro.html#enodeb-architecture

    # cd ~/srsRAN_4G/config/
    # nano enb.conf

Die eNodeB-Konfigurationsdatei (enb.conf) wird vom Programm srsENB eingelesen und verarbeitet. 

    Achtung:	
    
    Die hier vorgestellte eNodeB-Konfiguration behandelt keinerlei Handover-Konfigurationsparametern. Es wird davon ausgegangen, dass eine einzige Mobilfunkantenne (eNodeB) im eigenen Mobilfunknetzwerk betrieben wird!
          
    Handover ist der Vorgang, wenn das Endgerät die Mobilfunkantenne wechselt. Eine Mobilfunkantenne versorgt den Bereich innerhalb ihrer Funkzelle mit Mobilfunk.  
          
    Alle Handover-Konfigurationsparametern werden auf den Standardwerten belassen. Beim Betrieb von mehreren Funkzellen im eigenen Mobilfunknetzwerk müssen alle Handover-Konfigurationsparametern geprüft und allenfalls angepasst werden!
			
https://de.wikipedia.org/wiki/Handover  		
https://de.wikipedia.org/wiki/Funkzelle

Alle nachfolgend nicht genannten Blöcke und Konfigurationsparametern der eNodeB-Konfigurationsdatei (enb.conf) sind auf den Standardwerten zu belassen!

Im Block [enb] sind folgende Anpassungen erforderlich:

Die von der Funkaufsichtsbehörde vergebene Mobilfunknetzkennzahl eingeben. Die Mobilfunknetzkennzahl besteht aus dem Ländercode (MCC) und der Netzkennung (MNC).  
https://de.wikipedia.org/wiki/Mobilfunknetzkennzahl

Zum Beispiel für die Mobilfunknetzkennzahl 228-99:

    [enb]
    mcc = 228
    mnc = 99

Dann muss die von dieser Mobilfunkantenne zu verwendenden Anzahl der physikalischen Ressourcenblöcke (PRB) definiert werden. Wie in einem vorgängigen Kapitel beschrieben, wird mit der Anzahl der physikalischen Ressourcenblöcke (PRB) die Bandbreite des von der Mobilfunkantenne ausgestrahlten Mobilfunksignals bestimmt. 

Wegen der geringen Rechenleistung vom Raspberry Pi 4 darf die Bandbreite im Downlink höchstens 5 MHz betragen. Mit dem vorgängig vorgestellten Rechner kann die Umrechnung von Bandbreite zu physikalischen Ressourcenblöcke (PRB) erfolgen. Für 5 MHz Bandbreite sind 25 PRB zu konfigurieren.

    [enb]
    n_prb = 25

Achtung: 	

    Achtung:
    
    Zur Einhaltung der funkregulatorischen Vorgaben muss bei der Konfiguration der physikalischen Ressourcenblöcke zwingend die Angaben des zugewiesenen Mobilfunkfrequenzbands in der Funklizenz beachtet und eingehalten werden! 
			
Achtung:

    Achtung:
    
    Nicht in allen Mobilfunkfrequenzbändern ist der Einsatz aller möglichen Bandbreiten zulässig. Vor der Wahl der Anzahl physikalischer Ressourcenblöcke muss die Webseite:
    
    https://www.sqimway.com/lte_band.php
    
    konsultiert werden, ob in diesem Mobilfunkfrequenzband die gewünschte Bandbreite respektive die gewünschte Anzahl physikalischer Ressourcenblöcke zulässig ist. 
    
    Auf dieser Webseite findet man in der Tabelle die Spalte "Channel bandwidth (MHz)" mit der Angabe der zulässigen Bandbreite für jedes 4G/LTE-Mobilfunkfrequenzband.

Mit nur einer Sendeantenne (nof_ports = 1) muss der Übertragungsmodus TM1 verwendet werden. TM1 bedeutet "Single-antenna port, port 0". Siehe dazu:  
https://www.sharetechnote.com/html/Handbook_LTE_TransmissionMode.html

    [enb]
    tm = 1
    nof_ports = 1

Beim Einsatz von mehreren Sendeantennen oder mehrern Empfangsantennen kann der Übertragungsmodus (transmission mode) angepasst werden. Damit der Mobilfunkempfang von den Vorteilen der Antennendiversität profitiert.  
https://de.wikipedia.org/wiki/Antennendiversit%C3%A4t  
https://docs.srsran.com/projects/4g/en/latest/usermanuals/source/srsenb/source/4_enb_advanced.html#mimo

Die IP-Adressangaben belassen wir generell auf den Standardwerten. Aus Sicherheitsgründen wird nur das rechnerinterne Loopback-Netzwerk verwendet.  
https://de.wikipedia.org/wiki/Loopback

Kommen wir zur Umkonfiguration vom [rf]-Block:

Gemäss den Angaben in der offiziellen Dokumentation von srsRAN sollten die Signalverstärkungswerte wie folgt für den LimeSDR Mini justiert werden:  
https://docs.srsran.com/projects/4g/en/latest/app_notes/source/pi4/source/index.html

    [rf]
    tx_gain = 60
    rx_gain = 40

Als nächstes ist der Zugriff auf die SDR-Hardware zu konfigurieren. Der Zugriff von srsRAN auf die SDR-Hardware soll über SoapySDR (device_name = soapy) erfolgen.

    [rf]
    device_name = soapy
    device_args = auto

Weiter geht es mit den Umkonfigurationen im [log]-Block:

In der Logdatei /tmp/enb.log sollen vom Programm srsENB auch informative Mitteilungen ausgegeben werden:

    [log]
    all_level = info

Als nächstes muss die Konfigurationsdatei rr.conf angepasst werden.

    # cd ~/srsRAN_4G/config/
    # nano rr.conf

Zum Block "cell_list = " wechseln.

Mit dem Konfigurationsparameter dl_earfcn wird die EARFCN des im Downlink von der Mobilfunkantenne ausgestrahlten Mobilfunksignals konfiguriert. Die EARFCN-Angabe erfolgt mit der gewünschten Mittenfrequenz des Downlink-Mobilfunksignals.  
https://de.wikipedia.org/wiki/Mittenfrequenz 

Die EARFCN kann mit dem "EARFCN calculator" auf der Webseite:  
https://www.sqimway.com/lte_band.php  
berechnet werden.

Achtung:

    Achtung:

    Zur Einhaltung der funkregulatorischen Vorgaben muss bei der Konfiguration des EARFCN zwingend die Angaben des zugewiesenen Mobilfunkfrequenzbands in der Funklizenz beachtet und eingehalten werden! 
			
Zum Beispiel für den Downlink im Mobilfunkfrequenzband 7 im Bereich von 2685 MHz bis 2690 MHz:

Mittenfrequenz:  2687.5 MHz   => EARFCN:=3425

    cell_list = 
    (
      {
        ...
        dl_earfcn = 3425

Alle Änderungen in der eNodeB-Konfigurationsdateien kontrollieren:

    # diff --color=always enb.conf.example enb.conf
    # diff --color=always rb.conf.example rb.conf
    # diff --color=always rr.conf.example rr.conf
    # diff --color=always sib.conf.example sib.conf        


### EPC konfigurieren
Konfigurationsdatei des Kernnetzwerks (Evolved Packet Core; kurz: EPC) vom eigenen Mobilfunknetzwerk anpassen:  
https://en.wikipedia.org/wiki/Evolved_Packet_Core  
https://docs.srsran.com/projects/4g/en/latest/usermanuals/source/srsepc/source/1_epc_intro.html

    # cd ~/srsRAN_4G/config/
    # nano epc.conf

Die EPC-Konfigurationsdatei (epc.conf) wird vom Programm srsEPC eingelesen und verarbeitet.

Alle nachfolgend nicht genannten Blöcke und Konfigurationsparametern der EPC-Konfigurationsdatei (epc.conf) sind auf den Standardwerten zu belassen!

Im Block [mme] sind folgende Anpassungen erforderlich:

Die von der Funkaufsichtsbehörde vergebene Mobilfunknetzkennzahl eingeben. Die Mobilfunknetzkennzahl besteht aus dem Ländercode (MCC) und der Netzkennung (MNC).  
https://de.wikipedia.org/wiki/Mobilfunknetzkennzahl

Zum Beispiel für die Mobilfunknetzkennzahl 228-99:

    [mme]
    mcc = 228
    mnc = 99

Den Lang- und Kurznamen des eigenen Mobilfunknetzwerks angeben:

    [mme]
    full_net_name = test
    short_net_name = test

Die IP-Adressangaben belassen wir generell auf den Standardwerten. Aus Sicherheitsgründen wird nur das rechnerinterne Loopback-Netzwerk verwendet.  
https://de.wikipedia.org/wiki/Loopback

Einzig die Angabe des für die Namensauflösung (DNS) zu verwendenen DNS-Forwarder zeigt auf eine öffentliche oder private IPv4-Adresse.  
https://de.wikipedia.org/wiki/Domain_Name_System

Als Beispiel wird auf dem Raspberry Pi "Unbound" als DNS-Forwarder betrieben. Unbound ist im Beispiel auf der Loopback-Adresse (IP: 127.0.0.1) auf Serverport TCP 53 und UDP 53 erreichbar:  
https://de.wikipedia.org/wiki/Unbound

    $ ss --listening --tcp --udp --processes --numeric
    Netid 	State		Recv-Q		Send-Q		Local Adddress:Port		Peer Address:Port 		Process
    udp		UNCONN		0			0			127.0.0.1:53			0.0.0.0:*				users:(("unbound", ...))
    udp		UNCONN		0			0			127.0.0.1:323			0.0.0.0:*				users:(("chronyd", ...))
    udp		UNCONN		0			0			[::1]:53				[::]:*					users:(("unbound", ...))
    udp		UNCONN		0			0			[::1]:323				[::]:*					users:(("chronyd", ...))
    tcp		LISTEN		0			0			127.0.0.1:53			0.0.0.0:*				users:(("unbound", ...))
    tcp		LISTEN		0			0			[::1]:53				[::]:*					users:(("unbound", ...))

Für die Prozessangaben muss der Befehl "ss" mit root-Rechten aufgerufen werden!
			
    [mme]
    dns_addr = 127.0.0.1

Kommen wir zu den sicherheitsrelevanten Konfigurationsparametern. Mit dem Konfigurationsparameter "encyrption_algo" wird als Verschlüsselungsmethode AES-128-CTR gewählt (128-EEA2). Und mit dem Konfigurationsparameter "integrity_algo" wird das Datenintegritätsschutzverfahren AES-128-CMAC gewählt (128-EIA2). 

Mit "request_imeisv = true" fordert das Kernnetzwerk die Lieferung der IMEISV vom Endgerät an das Kernnetzwerk in verschlüsselter Form.  
https://en.wikipedia.org/wiki/International_Mobile_Equipment_Identity 

    [mme]
    encryption_algo = EEA2
    integrity_algo = EIA2
    request_imeisv = true

Warnung: 

    Warnung:  	
    
    Für ein sicheres, eigenes Mobilfunknetzwerk ist der Einsatz von selbst ausgestellten SIM-Karten erforderlich!

			 
Zum Thema "Sicherheit" im 4G/LTE-Mobilfunknetzwerk sollten diese Webseiten
konsultiert werden:  
https://www.ericsson.com/en/blog/2021/6/evolution-of-cryptographic-algorithms  
https://www.3glteinfo.com/lte-security-architecture/  
https://www.sharetechnote.com/html/Handbook_LTE_EEA.html  
https://www.sharetechnote.com/html/Handbook_LTE_EIA.html   
https://www.etsi.org/security-algorithms-and-codes/security-algorithms  
https://www.telecomtrainer.com/what-is-the-function-of-the-security-mode-command-smc-in-lte-communication/  
https://howltestuffworks.blogspot.com/2011/11/nas-security-mode-complete.html

Weiter geht es mit den Umkonfigurationen im [log]-Block:

In der Logdatei /tmp/epc.log sollen vom Programm srsEPC auch informative Mitteilungen ausgegeben werden:

    [log]
    all_level = info

Als nächstes muss die SIM-Karten-Datenbank gepflegt werden. Die SIM-Karten-Datenbank befindet sich in der Konfigurationsdatei "user_db.csv". Diese Konfigurationsdatei mit einem Texteditor zur Bearbeitung öffnen:

    # cd ~/srsRAN_4G/config/
    # nano user_db.csv
	 
Die bereits bestehenden SIM-Karteneinträge mit dem Zeichen "#" am Zeilenanfang
auskommentieren. Auskommentierte Zeilen erscheinen im Texteditor NANO in blauer
Schriftfarbe.

    #ue1,xor, ...
    #ue2,mil, ...	 
	 
Alle Änderungen in den EPC-Konfigurationsdateien kontrollieren:

    # diff --color=always epc.conf.example epc.conf
    # diff --color=always user_db.csv.example user_db.csv

### Testlauf
--------------------------------------------------------------------------------
Die Spannung steigt, es steht der erste Testlauf mit der neuen Konfiguraton an. Für ein funktionstüchtiges, eigenes Mobilfunknetzwerk muss gleichzeitig das Kernnetzwerk (EPC) und die Mobilfunkantenne (eNodeB) in Betrieb stehen.

Kernnetzwerk (EPC) starten:

    $ cd /home/foo/srsRAN_4G/config
    $ /home/foo/srsRAN_4G/build/srsepc/srs/srsepc ./epc.conf

Damit das Programm srsepc erfolgreich startet, muss dieses Programm mit root-Rechten gestartet werden!
			
Die Angabe vom Benutzer "foo" ist durch den entsprechenden Benutzername zu ersetzen!			
			
Logdatei vom Kernnetzwerk (EPC) beobachten:

    $ tail -f /tmp/epc.log

Mobilfunkantenne (eNodeB) starten:

    # cd ~/srsRAN_4G/config
    # ~/srsRAN_4G/build/srsenb/srs/srsenb ./enb.conf 

Achtung: 

    Achtung:
    
    Aus Sicherheitsgründen sollte das Programm srsenb immer ohne Root-Rechte gestartet werden!
			
Direkt nach dem Start von srsenb diese Anwendung zur Echtzeitanwendung heraufzustufen. Dazu sind root-Rechte erforderlich!
  
    $ renice --priority -11 -prgrp $(pidof srsenb)

Logdatei von der Mobilfunkantenne (eNodeB) beobachten:

    # tail -f /tmp/enb.log

Auf welchen IP-Adressen und Serverports horcht das Kernnetzwerk (EPC) auf eingehende SCTP- und UDP-Netzwerkverbindungen?  
https://de.wikipedia.org/wiki/Stream_Control_Transmission_Protocol

    $ ss --sctp --udp --tcp --listening --numeric --process
    Netid 	State		Recv-Q		Send-Q		Local Adddress:Port		Peer Address:Port 		Process
    udp		UNCONN		0			0			127.0.0.1:53			0.0.0.0:*				users:(("unbound", ...))
    udp 	UNCONN		0			0			127.0.1.1:2152			0.0.0.0:*				users:(("srsenb",  ...))
    udp 	UNCONN		0			0			127.0.1.100:2152		0.0.0.0:*				users:(("srsepc",  ...))
    udp		UNCONN		0			0			127.0.0.1:323			0.0.0.0:*				users:(("chronyd", ...))
    udp		UNCONN		0			0			[::1]:53				[::]:*					users:(("unbound", ...))
    udp		UNCONN		0			0			[::1]:323				[::]:*					users:(("chronyd", ...))
    tcp		LISTEN		0			256			127.0.0.1:53			0.0.0.0:*				users:(("unbound", ...))
    tcp		LISTEN		0			256			[::1]:53				[::]:*					users:(("unbound", ...))
    sctp 	LISTEN		0			4096		127.0.1.100:36412		0.0.0.0:*				users:(("srsepc",  ...))

Kommen die SCTP-Netzwerkverbindungen zwischen Mobilfunkantenne (eNodeB) und Kernnetzwerk (EPC) zustande?
  
    # ss --sctp --numeric
    State		Recv-Q		Send-Q		Local Address:Port		Peer Address:Port		Process
    LISTEN		0			0			127.0.1.100:36412		0.0.0.0:*
    ...
    UNCONN		...
    `- ESTAB	0			0			127.0.0.1%lo:49937		127.0.0.1.100:36412		

Funktionieren die SCTP-Firewallregeln einwandfrei?

    $ conntrack -E |grep -i sctp
    ...
    [UPDATE] sctp ... ESTABLISHED src=127.0.0.1 dst=127.0.1.100 ... dport=36412 ... [ASURED]

Ausgaben der Programme srsEPC und srsENB kontrollieren:

Ist die Mobilfunkantenne (eNodeB) mit dem Kernnetzwerk (EPC) verbunden, steht in den Ausgaben vom Programm srsEPC die Zeile:

    Sending S1 Setup Response

In den Ausgaben vom Programm srsENB ist zu kontrollieren, ob die korrekten Sende- und Empfangspfade vom LimeSDR Mini verwendet werden. Für Mobilfunkfrequenzbänder im Bereich von 2.0 GHz bis 3.5 GHz muss der:

    Empfangspfad: 	RX1_H	=> srsENB-Ausgabe:  Rx antenna set to LNAH
    Sendepfad:		TX1_1	=> srsENB-Ausgabe:	Tx antenna set to BAND1

verwendet werden. Mobilfunkfrequenzbänder > 3.5 GHz werden vom LimeSDR Mini nicht unterstützt. 

Für Mobilfunkfrequenzbänder im Bereich von 10 MHz bis 2 GHz muss der:

    Empfangspfad: 	RX1_W	=> srsENB-Ausgabe:  Rx antenna set to LNAW
    Sendepfad:		TX1_2	=> srsENB-Ausgabe:	Tx antenna set to BAND2

verwendet werden.

https://wiki.myriadrf.org/LimeSDR-Mini_v1.2_hardware_description#RF_Frequency_Range  
https://github.com/srsran/srsRAN_4G/issues/437#issuecomment-586745598

Häufig schlägt die Kalibration des Empfangspfad vom LimeSDR Mini mit der 
Fehlermeldung:

    [ERROR] Rx calibration: MCU error 5 (Loopback signal weak: not connected/insufficient gain?)

in den Ausgaben vom Programm srsENB fehl. Offenbar schlägt die Kalibration des LimeSDR (Mini) fehl, wenn die Kühlung der SDR-Hardware ungenügend ist oder die Synchronisation mit einer Atomuhr fehlt (GPSDO).    
https://discourse.myriadrf.org/t/error-rx-calibration-mcu-error-5-loopback-signal-weak-not-connected-insufficient-gain/7637

Die Kalibration des LimeSDR (Mini) schlägt häufig fehl, wenn der hochfrequente Empfangspfad (RX1_H, LNAH) kalibriert werden soll. 

Die Kalibration des Sendepfads und Empfangspfad wird bei jedem Programmstart von srsENB automatisch durchgeführt. Ein SDR sollte nie ohne vorgängige, erfolgreiche Kalibration im Sendepfad und Empfangspfad für den Funksendebetrieb eingesetzt werden!

Beim ersten Testlauf sollte kontrolliert werden, ob die funkregulatorischen Anforderungen erfüllt werden. Dazu muss mit einem zweiten SDR das vom LimeSDR Mini ausgestrahlte Mobilfunksignal geprüft werden. Eine kostengünstige Lösung für einen zweiten SDR ist der ADALM-PLUTO (PlutoSDR). Ich verwende dazu einen Laptop mit dem Betriebssystem MS Windows 10 und der SDR-Software "SDR Console":  
https://www.sdr-radio.com/Console

Befindet sich das vom LimeSDR Mini ausgestrahlte Mobilfunksignal im Bereich von 2685 bis 2690 MHz? Ist die Signalspitze des 4G/LTE-Mobilfunksignals genau bei der Mittenfrequenz (2687.5 MHz)?

# Fehlende Schritte
In dieser Anleitung fehlen (noch) folgende Anweisungen:

TODO:

    TODO:
    
    - Erstellen eines Softwarepakets (DEB) von srsRAN_4G für die professionelle Softwareinstallation.

    - Behandlung der srsRAN-Installation mit den Befehlen: "make install" und "ldconfig"?
    
    - Erstellen von Systemd-Units zum Starten und Beenden der Serverdienste srsENB und srsEPC.
    
    - Konfiguration der Systemd-Units für den Start von srsENB als Echtzeitanwendung (höhere Priorität im Task-Scheduling).
    
    - Konfiguration der Systemd-Units mit einem "Systemd-Watchdog" für den zuverlässigen, kontinuerlichen Betrieb der Serverdienste srsENB und srsEPC.
    
    - Überwachung der CPU-Auslastung mit "htop".
    
    - Aus Sicherheitsgründen den Start von srsEPC ohne root-Rechte ermöglichen.
    
    - Quellcode-Änderungen für den Netzwerkzugriff mit fremden SIM-Karten.
    
    - IP-Routerkonfiguration für die Weiterleitung der Datenpakete aus dem eigenen Mobilfunknetzwerk ins Internet.
    
    - Firewallregeln für die Weiterleitung der Datenpakete aus dem eigenen Mobilfunknetzwerk ins Internet.
    
    - "Traffic Shaper" für die Weiterleitung der Datenpakete aus dem eigenen Mobilfunknetzwerk ins Internet.
    
    - Beschreib der Netzwerkanmeldung mit einem modernen, Android-Smartphone.


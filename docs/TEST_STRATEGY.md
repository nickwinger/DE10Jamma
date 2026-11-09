# V3-Teststrategie: bestehendes DE10Jamma gezielt erweitern

**Status: dokumentierte Testplanung, noch kein Nachweis ausgeführter Simulationen oder einer Fertigungsfreigabe.** Stand: 2026-11-09. Verbindliche Anforderungen stehen in [AGENTS.md](../AGENTS.md); Umsetzung und Freigabe werden in der [Roadmap](ROADMAP.md) verfolgt.

## 1. Ziel und Änderungsumfang

Wir verwenden DE10Jamma V2 weiter, statt eine neue JAMMA-Plattform zu entwickeln. Referenz ist `smdjeff/DE10Jamma` am Commit `fd7a9569fb19a7e95b04638bb1bc645c9801b142`. Mechanik, JAMMA-Steckkante, RGB-DAC und Audioschaltung bleiben soweit für V3 möglich erhalten. Änderungen an übernommenen Blöcken brauchen einen konkreten technischen Grund; bloße Modernisierung ist kein Arbeitsauftrag.

Pflichtumfang: USB-HID, Villena-kompatibles DB15-Schieberegister und direkte parallele FPGA-Eingänge bleiben gleichzeitig angeschlossen, ohne Hardware-Umschalter. Die drei DB15-Pins bleiben fest; die bisher dort liegenden direkten Eingänge werden umgelegt. Die Registerbeschaltung und der USB-HID-Anschluss gehören ebenfalls zum Änderungsumfang, soweit sie in V2 noch nicht vorhanden sind. Ein USB-Hub allein ist kein HID-Encoder.

SNAC ist nur eine Option nach dem Pflichtumfang. Es wird nur ergänzt, wenn Pinbelegung, Pegel und Portbelegung nachvollziehbar geprüft sind und die drei Pflichtwege unverändert funktionieren. Ein freier Steckerplatz allein ist kein Kompatibilitätsnachweis. Fehlender optionaler SNAC-Support verhindert die Freigabe des Pflichtumfangs nicht.

Es wird **genau eine PCB** geplant. Keine Test-PCB-Serie, keine eingeplante zweite Fertigung, keine automatisch genehmigten Messgeräte- oder Werkzeugkäufe. Vorab mögliche Prüfungen erfolgen vor der Bestellung. Die Inbetriebnahme erfolgt anschließend am dauerhaft vorgesehenen Einzelstück.

## 2. Bestehende Basis prüfen, Änderungen gezielt absichern

Zunächst V2-Netzliste, Layout und relevante RTL-Pinzuweisungen als Referenz erfassen. Für jede V3-Änderung wird notiert: altes Netz, neues Netz, Grund, betroffene Bauteile/Pins und zugehörige Test-ID. Ein elektrischer Netzvergleich ist aussagekräftiger als ein bloßer Textvergleich der KiCad-Dateien.

Unveränderte Video-/Audio-/Versorgungsblöcke erhalten einen Regressionsvergleich: Werte, Netze, Footprints, Anschlussrichtung und Lastannahmen dürfen nicht unbeabsichtigt verändert sein. Eine neue detaillierte Simulation konzentriert sich auf Änderungen und deren Auswirkungen, nicht auf eine vollständige Neuentwicklung aller V2-Funktionen. Relevante übernommene Annahmen werden dennoch gegen Primärunterlagen geprüft; Internetdokumentation ist Referenzmaterial, kein automatischer Hardwaretestnachweis.

## 3. Werkzeuge und Nachweise

| Prüfbereich | Geplantes Werkzeug | Ergebnis |
|---|---|---|
| Netzliste und Pinplan | KiCad-Netzlistenexport, Python/pytest | Vergleich von Sollbelegung, Schaltplan, PCB und FPGA-Constraints |
| Schaltplan und Layout | KiCad ERC/DRC, visuelle Kontrolle | Prüfberichte mit einzeln begründeten Ausnahmen |
| Digitale Eingabelogik | Verilator und cocotb | Automatisierte Tests des tatsächlichen RTL-Codes, Ergebnisdateien und bei Fehlern Signalverläufe |
| Elektrische Teilfunktionen | Datenblattberechnungen; ngspice für relevante modellierbare Schaltungen | Pegel-, Strom-, Last- und Toleranzprüfung mit dokumentierten Annahmen |
| FPGA-Implementierung | Quartus mit Cyclone-V-Unterstützung | Fitter-Pinbelegung, Ressourcen- und Timing-Berichte der eigenen Builds |
| Mechanik und Ausgabe | KiCad-Ansichten, Maßzeichnungen, 1:1-Ausdruck, Gerber-Viewer | Stecker-, Bohrungs-, Bauraum- und Fertigungsdatenkontrolle |

Die tatsächlich verwendeten Versionen, Befehle, Modelle und Quellstände werden mit jedem Prüfbericht gespeichert. Diese Tabelle bedeutet nicht, dass die Werkzeuge bereits installiert oder die Tests bereits implementiert sind. Versionskompatibilität wird bei der Einrichtung festgelegt; keine unkontrollierten `latest`-Abhängigkeiten im reproduzierbaren Testlauf.

## 4. Netz- und Pinprüfungen vor der Bestellung

Die unabhängige Sollbelegung folgt dem Format in [PINPLAN.md](PINPLAN.md). Quellen sind die tatsächlichen DE10-Nano-Unterlagen, die V2-Schaltung und die Constraints der vorgesehenen Core-Builds. Stecker-Pinnummer, GPIO-Signalname und FPGA-Package-Pin werden getrennt erfasst.

| ID | Prüfung | Bestehenskriterium |
|---|---|---|
| PIN-01 | Alle 24 Spielereingaben sowie separat geplante Test-/Service-/Menüsignale verfolgen | Jede Funktion besitzt eine vollständige und eindeutige Verbindungskette vom Cabinet-Kontakt bis zum Ziel |
| PIN-02 | DB15-Reservierung prüfen | LOAD=`USER_IO[0]`/AG11, CLK=`USER_IO[1]`/AH9, DATA=`USER_IO[5]`/AF15; keine direkte Taste teilt diese Netze |
| PIN-03 | Sollplan gegen Schaltplan, PCB-Pads und eigene FPGA-Constraints vergleichen | Physische Verbindungen und logische Funktionen stimmen in allen vier Darstellungen überein |
| PIN-04 | SDRAM, RGB, Sync, Audio und Versorgung gegen V2 vergleichen | Keine unbeabsichtigten Netz-, Wert- oder Pinänderungen; beabsichtigte Änderungen einzeln erklärt |
| PIN-05 | Verhalten mit Standard-, DB15- und Direct-Builds erfassen | Treiberrichtung, Pull-ups, Reset-/Konfigurationszustand und mögliche Nebenfunktionen jedes angeschlossenen Pins bewertet |
| PIN-06 | Prüfskript mit absichtlich fehlerhaften Testdaten ausführen | Vertauschter Headerpin, doppelt belegter Eingang und falscher DB15-Pin werden zuverlässig erkannt |

Die Sollbelegung wird unabhängig mit den Herstellerunterlagen abgeglichen. Dieselbe falsche Tabelle in Schaltplan und RTL zu übernehmen darf nicht als bestandene Prüfung gelten. Für jeden genutzten externen Build werden Version/Commit und zugehörige Constraints festgehalten; ungetestete Builds erhalten keine pauschale Freigabe.

## 5. RTL-Simulation der Eingabewege

Prüfling ist der tatsächlich verwendete DB15-Leser beziehungsweise unser Direct-/Quellenauswahlmodul, nicht nur eine neu geschriebene Ersatzimplementierung. Als Gegenstelle dient ein Modell der konkret vorgesehenen Registerbeschaltung mit ihrer Bitreihenfolge, Polarität, LOAD-Funktion und Taktflanke. Modell und Platinenverdrahtung werden anhand der Bauteildaten und der vorhandenen Villena-/MiSTer-DB9-Implementierung abgeglichen.

USB wird in diesen Modultests durch Zustände an der vorhandenen Core-Schnittstelle dargestellt. Das simuliert die Quellenwahl, nicht den gesamten USB-Bus, Linux oder den HID-Encoder.

| ID | Testfall | Bestehenskriterium |
|---|---|---|
| RTL-01 | Jede der 24 Spielereingaben einzeln drücken und loslassen | Nur die richtige Funktion beim richtigen Spieler reagiert; Rückkehr zum Ruhezustand |
| RTL-02 | Alle 276 Zweierkombinationen, alle Tasten gleichzeitig und reproduzierbare Zufallsmuster | Kein Vertauschen oder Übersprechen; erwartete Ausgabe aus unabhängigem Sollmodell |
| RTL-03 | Einzelbitmuster über die vollständige Registerkette | Richtige Anzahl/Zuordnung der Nutzbits, Spielerreihenfolge, Polarität und Abtastflanken |
| RTL-04 | Schalterzustand vor, während und nach LOAD beziehungsweise während des Schiebens ändern | Auswertung entspricht dem spezifizierten Übernahmeverhalten; kein unbegründetes Mischen zweier Scans |
| RTL-05 | USB-Zustände absichtlich später als Direct-/DB15-Zustände liefern | In unseren eigenen Builds beeinflusst die nicht ausgewählte Spielquelle weder Drücken noch Loslassen |
| RTL-06 | Eingabequelle bei gehaltenen Tasten wechseln | Dokumentiertes Verhalten ohne hängende, doppelte oder künstlich verlängerte Aktionen |
| RTL-07 | Reset im Leerlauf und mitten im Scan; Neustart mit gehaltenen Tasten | Definierte Initialisierung und Wiederanlauf, keine Phantomaktionen durch alte Registerinhalte |
| RTL-08 | Asynchrone Änderungen, Prellmuster und kurze Pulse | Verhalten entspricht der gewählten Synchronisations-/Filterstrategie; erforderliche Mindestimpulsdauer dokumentiert |

Spielereingaben sind zunächst unveränderte Schalterzustände. Gegensätzliche Richtungen und Mehrfachbetätigung werden nicht allein für einen grünen Test künstlich bereinigt. Eine bewusst gewählte Eingaberegel wird separat spezifiziert.

Prüfberichte nennen Testanzahl, Seed, Simulationsversion und Quellcommit. Erwartete Werte dürfen nicht allein aus der zu prüfenden Implementierung abgeleitet werden. Ein negatives Testbeispiel mit vertauschten Registerbits muss scheitern.

Digitale Simulation ersetzt keine elektrische Pegelprüfung. Metastabilität, reale Signalintegrität und externe Zeitbedingungen sind nicht durch einen bestandenen Zwei-Zustands-RTL-Test bewiesen. Bei X/Z-, Tri-State- oder herstellerspezifischen Modellen wird die Eignung des Simulators gesondert geprüft; gegebenenfalls wird dafür ein geeigneter zusätzlicher Simulator eingesetzt. Eine vollständige Simulation aller Spiele-Cores oder des gesamten MiSTer-Systems ist nicht Voraussetzung für diese Modultests.

## 6. Elektrische Prüfung der Änderungen

| ID | Prüfung | Nachweis |
|---|---|---|
| ELEC-01 | Ein Schalter versorgt gleichzeitig Direct-, Register- und HID-Eingang | Gemeinsamer Spannungsbereich, HIGH/LOW-Grenzen, Pull-ups, Eingangslasten und Kontaktstrom aus tatsächlichen Datenblättern |
| ELEC-02 | LOAD, CLOCK und DATA | Signalrichtung, Spannungen, Setup/Hold, Laufzeiten und Flanken der konkreten Registerkette erfüllen die Anforderungen des Lesers |
| ELEC-03 | Reset, unkonfiguriertes FPGA und unterschiedlich versorgte Zweige | Keine unzulässige Rückspeisung oder gegeneinander arbeitenden Ausgangstreiber; Schutz und Ruhezustände nachvollziehbar |
| ELEC-04 | Versorgung aus JAMMA, DE10-Nano und USB | Dokumentierte Speisepfade, Strombedarf, gemeinsame Masse und Rückspeiseschutz; keine versehentlich verbundenen Versorgungsquellen |
| ELEC-05 | Auswirkungen auf bestehende Video-/Audioausgänge | V2-Werte und Netze erhalten beziehungsweise jede Änderung unter der vorgesehenen Last geprüft |

Analoge Modelle verwenden die tatsächlichen Bauteile oder klar dokumentierte Näherungen. Versorgungs- und Bauteiltoleranzen werden anhand der Datenblätter festgelegt. Wenn kein geeignetes SPICE-Modell existiert, wird kein Scheinnachweis erzeugt: dann Datenblatt-/Worst-Case-Berechnung und konkreten Hardware-Prüfpunkt dokumentieren.

Für die Hardware-Abnahme werden Spannungs-, Strom- und Timinggrenzen vorab aus den ausgewählten Bauteilen abgeleitet. Offene Grenzwerte dürfen nicht erst nach einer Messung passend gewählt werden.

## 7. FPGA-Build, Layout und Mechanik

| ID | Prüfung | Bestehenskriterium |
|---|---|---|
| BUILD-01 | Test-Core und ein Direct-Referenzspiel vollständig übersetzen | Synthese und Fitter für den realen FPGA erfolgreich; finale Pinliste stimmt mit PIN-03 überein |
| BUILD-02 | Timing und I/O-Einstellungen | Takte und relevante Ein-/Ausgabezeiten beschrieben; keine ungeklärten Timingfehler; asynchrone Pfade gezielt behandelt statt global ausgeblendet |
| CAD-01 | ERC/DRC samt Schaltplan-PCB-Abgleich | Keine ungeklärten Fehler, keine unverbundenen Pflichtnetze; Warnungen und Ausnahmen einzeln nachvollziehbar |
| CAD-02 | Footprints und Steckerorientierung | Datenblatt-Pinout, Pin 1, Ober-/Unterseite und tatsächliche Bestellteile abgeglichen |
| MECH-01 | Mechanische Passform | Abstände, Bohrungen, Headerhöhe, SDRAM-/Kühlkörperfreiraum, JAMMA-Kante und Anschlusszugang geprüft; 1:1-Ausdruck mit Skalierungsreferenz an realer Hardware kontrolliert |
| FAB-01 | Exportierte Fertigungsdaten separat ansehen | Gerber, Bohrungen, Lagen, Kontur, BOM und Bestückungsdaten stammen aus demselben freizugebenden Quellstand und stimmen mit ihm überein |

Für unveränderte Fremd-Bitstreams werden deren konkrete Pin-/Betriebsannahmen geprüft; unser eigener erfolgreicher Build beweist nicht automatisch deren gesamte Kompatibilität.

## 8. Optionaler SNAC-Port

SNAC bekommt einen eigenen Testumfang nur dann, wenn er tatsächlich eingebaut wird: vollständige Pin-/Pegelbelegung, vorgesehene Adapter/Core-Kombinationen, Verhalten bei inaktivem Port und mögliche gemeinsame USER_IO-Leitungen dokumentieren. Die beiden Nativeingabewege und USB dürfen dadurch nicht beeinträchtigt werden. Zusätzliche Ausgangstreiber dürfen nicht ungeprüft auf bereits belegte Leitungen geschaltet werden. Keine pauschale Zusage für sämtliche SNAC-Adapter.

Kann diese Option für die einzige Bestellung nicht ausreichend geprüft werden, bleibt sie aus dem freigegebenen Funktionsumfang; USB, DB15 und Direct GPIO bleiben vollständig erhalten. Das ist keine Aufforderung, die Pflichtarchitektur neu zu entwerfen.

## 9. Automatisierung und Ergebnisablage

Geplant sind versionierte Python-/cocotb-Tests, Registermodelle und elektrische Testschaltungen. Ein automatischer Lauf soll Netz-/Pinprüfungen, RTL-Tests und ERC/DRC ausführen; analoge Batchtests kommen für die betroffenen Teilblöcke hinzu. Quartus kann wegen Installation und Laufzeit separat lokal ausgeführt werden. Keine kostenpflichtige CI-Infrastruktur als Voraussetzung; CI und ausführbare Testskripte sind noch zu implementieren.

Pro Ergebnis werden mindestens festgehalten:

`Test-ID | Anforderung | Quellcommit | Referenz-/Modellversion | Werkzeugversion | Aufruf/Seed | Sollwert/Grenze | Ist-Ergebnis | Status | Bericht`

Erlaubte Zustände: **geplant**, **implementiert, nicht ausgeführt**, **bestanden**, **fehlgeschlagen**, **nicht anwendbar mit Begründung**. Fehlende Werkzeuge oder Modelle ergeben keinen bestandenen Test. Die Beschränkung eines Tests und begründete Ausnahmen sind sichtbar, nicht in einem pauschalen grünen Status versteckt.

Nach jeder Änderung werden betroffene Prüfungen wiederholt; vor der Bestellung wird der gesamte Pflichtumfang für einen eindeutig bezeichneten Quellstand geprüft. Eine dokumentierte Strategie ist nicht mit einer implementierten Testsuite gleichzusetzen.

## 10. Freigabe und Inbetriebnahme

Die Bestellfreigabe folgt der Liste in [ROADMAP.md](ROADMAP.md). Vorher müssen die vorab möglichen Pflichtprüfungen samt Berichten abgeschlossen sein. Nicht anwendbare Prüfungen brauchen einen technischen Grund; nicht ausgeführte relevante Prüfungen dürfen nicht als erledigt abgehakt werden. Der Eigentümer entscheidet über die einzige Bestellung anhand des konkreten Prüfnachweises, des Angebots und der verbleibenden Hardwaretestpunkte.

Nach Fertigung werden zuerst Orientierung, Kurzschlüsse und Versorgung geprüft, danach alle Tasten, drei Eingabewege, Core-Wechsel, Menü, CRT-Ausgabe und Audio am Einzelstück. Ein vorbereiteter Test-Core hilft bei der eindeutigen Zuordnung. DB15 und Direct werden zusätzlich bei getrennter HID-Datenverbindung geprüft, um ihre Unabhängigkeit von USB zu zeigen. Dies ist die Inbetriebnahme des vorgesehenen Boards, keine eingeplante weitere Prototypenrunde.

Vorabprüfungen reduzieren das Fehlerrisiko. Sie ersetzen keine Prüfung realer Montage, Steckkontakte, Cabinet-Verkabelung und analoger Signale. Diese Grenze wird konkret dokumentiert, ohne reguläre TODOs als grundsätzliche Architekturhindernisse zu behandeln.

## Quellen

- [V2-Referenzstand](https://github.com/smdjeff/DE10Jamma/tree/fd7a9569fb19a7e95b04638bb1bc645c9801b142)
- [MiSTer-DB9-Pinreferenz](https://github.com/MiSTer-DB9/Documentation/blob/main/user-guide/pinout-reference.md) und [DB15-Beschreibung](https://github.com/MiSTer-DB9/Documentation/blob/main/user-guide/controllers/neogeo-supergun.md); konkrete RTL-Revision bei Testimplementierung festhalten.
- [KiCad CLI: ERC, DRC und Exporte](https://docs.kicad.org/9.0/en/cli/cli.html); Dokumentationsversion 9.0 ist Referenz, nicht Behauptung einer installierten Projektversion.
- [KiCad/ngspice](https://www.kicad.org/discover/spice/)
- [Verilator-Übersicht](https://verilator.org/guide/latest/overview.html)
- [cocotb: Simulatorunterstützung und Einschränkungen](https://docs.cocotb.org/en/stable/simulator_support.html)

Herstellerdatenblätter, DE10-Nano-Pinunterlagen und Quartus-Projektversion werden mit der konkreten Schaltungs-/Testimplementierung versionsgenau ergänzt. Quellen ersetzen keine abgelegten projektspezifischen Testergebnisse.

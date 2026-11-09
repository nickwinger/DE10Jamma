# V3-Architektur

Status: vereinbartes Entwicklungsziel. Die Umsetzung und ihre Prüfergebnisse stehen in der [Roadmap](ROADMAP.md). Die aktuellen Schaltungsdateien sind noch V2.

## 1. Festgelegter Aufbau

V3 ist eine Weiterentwicklung von `smdjeff/DE10Jamma` V2 als direkt an die Header des Terasic DE10-Nano angeschlossenes JAMMA-Board. Das SDRAM-Modul und der USB-OTG-Anschluss bleiben nutzbar. Mechanische Stapelhöhe, Abstandshalter und Zugang zu SD-Karte, Ethernet, HDMI und USB werden im Layout berücksichtigt.

Die drei Pflicht-Eingabewege sind:

1. **USB-HID:** Cabinet-Schalter zu einem HID-Encoder, von dort über USB zum MiSTer. Dieser Weg bedient Standard-Cores und das Menü und bleibt als Fallback vorhanden.
2. **DB15-Schieberegister:** Dieselben Schalter an eine Villena-kompatible serielle Eingabeschaltung. Das FPGA steuert LOAD und CLOCK und liest DATA. Kein USB-/Linux-Inputpfad zwischen Schalter und Spiel.
3. **Direkte GPIOs:** Dieselben Schalter über geeignete Eingangsaufbereitung an jeweils eigene FPGA-Eingänge. Keine serielle Controllerabfrage und kein USB im direkten Spielpfad.

Diese Wege sind physisch gleichzeitig vorhanden. **Es gibt keinen Hardware-Umschalter zwischen den drei Wegen.** Die direkte GPIO-Belegung wird gegenüber V2 geändert; die DB15-Schnittstelle behält ihre erwarteten drei Pins. Die endgültige Zuordnung ist eine Implementierungsaufgabe, keine offene Architekturentscheidung.

SNAC ist eine nachrangige Erweiterung, soweit geeignete GPIOs übrig bleiben. Der Name MiSTer-DB9 bezeichnet hier das Projekt; für unseren Pflichtumfang wird dessen **DB15-Modus** verwendet. Unterstützung sämtlicher DB9-/Saturn-/SNAC-Adapter ist damit nicht automatisch zugesagt.

## 2. Bedienumfang und Verzweigung

Grundumfang pro Spieler: vier Richtungen, sechs Aktionstasten, Start und Coin. Das ergibt zwölf digitale Signale pro Spieler beziehungsweise 24 für zwei Spieler. Tasten außerhalb des normalen JAMMA-Anschlussumfangs werden über einen passenden Kick-Harness geführt. Test, Service und Menübedienung erhalten einen gesondert dokumentierten Anschluss-/Mappingplan; sie werden nicht stillschweigend zusätzlichen DB15-Spielerbits zugeordnet.

Die JAMMA-Schalterzustände werden auf die drei Eingangsschaltungen verteilt. Dabei dürfen deren Pull-ups und Versorgungen keine unzulässigen Spannungen oder Rückspeisungen verursachen. Der Schieberegister-Ausgang DATA ist **nicht** mit einem der parallelen Schalteingänge zu verbinden: Er bekommt seinen eigenen FPGA-Pin.

Der USB-Zweig braucht einen tatsächlichen HID-Encoder, nicht bloß einen Hub. Ein integrierter Mikrocontroller oder ein angebundenes Encoder-Modul sind mögliche Umsetzungen. Auswahl und Anschluss werden im Schaltplan festgelegt. Der Mikrocontroller darf die beiden direkten FPGA-Wege weder takten noch als notwendige Zwischenstation bedienen.

## 3. Eingabeauswahl im Core

Physisch parallele Signalwege bedeuten nicht, dass dieselbe Aktion aus allen drei Quellen zusammengeodert werden muss. Bei unseren angepassten Cores soll der Spieleingang eindeutig ausgewählt werden: USB, DB15 oder Direct GPIO. Der Menüpfad darf unabhängig davon USB verwenden.

Das vermeidet beispielsweise, dass ein direkt erkannter Tastendruck durch einen später eintreffenden USB-Zustand künstlich verlängert wird. Die Auswahl findet in der Core-Logik beziehungsweise deren Konfiguration statt, nicht durch Neuverdrahten der Platine.

Für fremde unveränderte Cores wird ihr vorhandenes Eingabeverhalten getestet. Die V3-Hardware soll keine Änderung der Standard-USB-Cores voraussetzen. Für Direct GPIO sind dagegen angepasste RTL-Eingänge und Pin-Constraints erforderlich; ein alter DE10Jamma-Build mit alter Pinbelegung ist kein V3-Build.

Asynchrone Schalter, Synchronisation, eventuelle Entprellung und Übergänge beim Core-Wechsel sind Teil der Implementierung. Zusätzliche Zeitanteile werden dokumentiert und gemessen, nicht pauschal als absolut null bezeichnet. Im direkten Spielpfad ist keine zusätzliche USB-Abfrage oder Softwareverarbeitung vorgesehen.

## 4. Softwareebenen

| Ebene | Aufgabe |
|---|---|
| Linux auf dem ARM/HPS | Basisbetriebssystem |
| ARM-Programm `MiSTer` | Cores laden, Dateien und Einstellungen verwalten, Menü-/Eingabeintegration |
| `menu.rbf` | FPGA-Konfiguration für das Hauptmenü |
| Spiele-Core `.rbf` | FPGA-Konfiguration für das gewählte System samt unterstützter Eingabelogik |

Beim Laden eines Spiele-Cores wird die vorherige FPGA-Konfiguration ersetzt. Standard-, DB9- und V3-Direct-Core-Dateien können nebeneinander gespeichert werden, werden jedoch nacheinander ausgeführt.

Die aktuelle [MiSTer-DB9-Installation](https://github.com/MiSTer-DB9/Documentation/blob/main/user-guide/installation.md) verwendet eigene Fassungen von `MiSTer`, `menu.rbf` und den unterstützten Spiele-Cores. Die Beschreibung einer angepassten Main-Anwendung bedeutet nicht, dass die DB15-Spielereingabe durch Linux geleitet werden muss: Das Schieberegister wird im FPGA gelesen.

Für V3 wird zunächst diese bestehende Kombination als gemeinsame Basis getestet. Zusätzlich bleibt eine unveränderte Standard-Installation als unabhängige Referenz sinnvoll. Eine spätere reine Core-Integration des DB15-Lesers mit Standard-Main ist möglich, aber keine Voraussetzung für die erste Hardware.

Beim Ablegen mehrerer Varianten müssen Dateinamen, Arcade-MRA-Verweise, Core-Konfigurationsnamen und Update-Ziele eindeutig sein. Eigene Builds dürfen nicht durch einen regulären Updater überschrieben werden. Nur mehrere Ordner anzulegen garantiert noch keine getrennten Konfigurationsdateien.

## 5. Video und Audio

V3 übernimmt die analoge Ausgabearchitektur von V2 als Ausgangsbasis:

```text
FPGA: digitale RGB-Werte --> Widerstands-DAC --> analoges JAMMA-RGB
FPGA: Composite-Sync -------------------------> JAMMA-Sync
FPGA: digitales Audio/PWM --> Tiefpass --> Verstärker --> Lautsprecher
```

V2 beschreibt drei 6-Bit-RGB-Widerstands-DACs und eine Audiofilter-/Verstärkerschaltung. Die FPGA-Systemlogik erzeugt Composite-Sync; die konkrete Ausgabe muss zur gewählten Core-Konfiguration passen. JAMMA benötigt kein zweites Videosystem neben dem normalen MiSTer-Bildgenerator.

Die elektrische Ausführung wird anhand des tatsächlichen V2-Schaltplans und der Bauteildaten geprüft. Analogpegel, Lasten, Sync-Polarität, native Bildfrequenzen und Lautsprecheranschluss gehören in den Testplan. Insbesondere werden Lautsprecher-Ausgänge nicht ohne Prüfung der Verstärkertopologie mit Masse verbunden.

Die unveränderten Standard-Video-/Audiosignale sollen nutzbar bleiben. HDMI bleibt eine Entwicklungs-/Diagnoseoption, ist aber nicht der vorgesehene Weg zum JAMMA-CRT. Ein zusätzliches klassisches Analog-I/O-Board ist für den fertigen V3-Aufbau nicht vorgesehen.

## 6. Elektrische und mechanische Umsetzung

Die Umsetzung dokumentiert je Signal Richtung, Spannungsdomäne, Pull-up/-down, Schutz und Verhalten in jedem Core-Modus sowie während Reset und FPGA-Neukonfiguration. Geeignete Eingangsbuffer oder Schutzschaltungen widersprechen dem Parallelbetrieb nicht; ein manueller Eingabeweg-Umschalter ist nicht Teil des Designs.

Standard-Bitstreams können andere Funktionen auf denselben Headern aktivieren. Daher wird die Sicherheit der permanent angeschlossenen V3-Eingangsschaltungen mit den tatsächlich vorgesehenen Bitstreams geprüft, nicht nur mit dem eigenen Direct-Core. FPGA-seitig unbenutzte Pins und ihre Syntheseeinstellungen werden ebenfalls dokumentiert.

Stromversorgung aus JAMMA und externer Versorgung, USB-Rückspeisung, gemeinsame Masse und die Versorgung des HID-Encoders werden explizit festgelegt. Vor der Fertigung werden Schaltplan und PCB gemeinsam geändert und geprüft; Gerber-Dateien werden anschließend neu erzeugt, nicht als primäre Schaltungsquelle editiert.

## 7. Was nicht Teil der ersten Revision ist

- Gleichzeitiger Betrieb mehrerer unabhängiger Spiele-Cores auf einem FPGA.
- Garantierte Unterstützung aller SNAC-Protokolle oder beliebiger externer Controlleradapter.
- Dual-SDRAM oder zusätzliche Arcade-Eingabearten wie Trackballs ohne eigene Spezifikation.
- Hardware-Umschalter als Voraussetzung zum Wechsel zwischen USB, DB15 und Direct GPIO.

Referenzen: [V2-Ausgangsstand](https://github.com/smdjeff/DE10Jamma/tree/fd7a9569fb19a7e95b04638bb1bc645c9801b142), [DE10Jamma-RTL-Beispiel](https://github.com/MisterJamma/Arcade-GnG_MiSTer/blob/master/sys/sys_top.v), [DB15-Protokoll](https://github.com/MiSTer-DB9/Documentation/blob/main/user-guide/controllers/neogeo-supergun.md). Externe Projektinformationen wurden für diesen Dokumentationsstand am 2026-11-09 abgeglichen.

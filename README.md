# DE10Jamma V3

**Ein JAMMA-Erweiterungsboard für den Terasic DE10-Nano mit drei parallel angeschlossenen Eingabewegen: USB-HID, DB15-Schieberegister und direkte FPGA-GPIOs. Ohne Hardware-Umschalter.**

Dieses Projekt entwickelt [DE10Jamma V2 von smdjeff](https://github.com/smdjeff/DE10Jamma) weiter. Es soll Arcade-Cabinets mit normalen MiSTer-Cores, MiSTer-DB9-Cores und eigenen Direct-GPIO-Cores betreiben können, ohne die Steuerung umzustecken. USB bleibt für das Menü und als Fallback erhalten; im Spiel können die Eingaben unmittelbar im FPGA verarbeitet werden.

## Entwicklungsstand

**V3 ist in Entwicklung, noch keine fertige Hardware-Revision.** Die Architektur und die nächsten Umsetzungsschritte sind dokumentiert. Die KiCad-Bibliothekspfade wurden berichtigt. Schaltplan, Leiterbahnen, PDF, Fotos und vorhandene Fertigungsdaten stammen weiterhin aus V2; sie enthalten noch nicht die V3-Erweiterungen.

Die Dateien `fabrication.zip` und `production_files/*` sind **keine V3-Fertigungsfreigabe**. Details: [Fertigungsdaten](production_files/README.md).

## Was V3 können soll

| Funktion | V3-Ziel |
|---|---|
| Mechanik | Direktes Erweiterungsboard für die DE10-Nano-Header mit JAMMA-Steckkante |
| USB-HID | Cabinet-Steuerung über einen HID-Encoder an USB; Standard-Cores, Menü und Fallback |
| DB15 | Villena-kompatibles Schieberegister für zwei Spieler, direkt vom FPGA gelesen |
| Direct GPIO | Eigene FPGA-Eingänge für die einzelnen JAMMA-Schalter, ohne USB oder Schieberegister im Spielpfad |
| Parallelbetrieb | Alle drei Wege dauerhaft angeschlossen; keine Hardware-Umschaltung und kein Umstecken zwischen ihnen |
| Bild | Analoge JAMMA-RGB-Ausgabe mit Composite-Sync; V2-Schaltung als Ausgangsbasis |
| Ton | Analoge Audioaufbereitung und Cabinet-Lautsprecherverstärker auf dem Board |
| Speicher | Ein 128-MB-MiSTer-SDRAM-Modul; dessen Anschluss bleibt reserviert |
| SNAC | Optional, wenn nach den drei Pflicht-Eingabewegen genügend geeignete GPIOs verfügbar sind |

SNAC ist keine Voraussetzung für V3. Ebenso ist ein zweites SDRAM-Modul kein Bestandteil der ersten Revision.

## Signalwege

```text
JAMMA / Kick-Harness: Joysticks, Buttons, Start, Coin
                         |
                  Eingangsaufbereitung
                         |
          +--------------+-------------------+
          |              |                   |
      USB-HID-       DB15-kompatible      Direkte parallele
      Encoder        Schieberegister     FPGA-Eingänge
          |              |                   |
      USB-OTG        LOAD / CLK / DATA    Eigene GPIOs
          |              |                   |
   Standard-Cores    MiSTer-DB9-Cores     V3-Direct-Cores
   Menü / Fallback        |                   |
                          +-------- FPGA ------+
```

Die Verzweigung zeigt die Zielarchitektur, keine fertige Verdrahtungsanleitung. Jeder Schalter soll alle drei Eingabewege erreichen können. Im Spiel wird der gewünschte Weg logisch ausgewählt, damit USB-Eingaben nicht zusätzlich dieselbe Spielaktion verzögert auslösen.

**Ein USB-Hub ist kein HID-Encoder.** Zusätzlich zum Hub braucht der USB-Zweig Elektronik, die die Schalter als USB-Eingabegerät meldet. Deren Integration beziehungsweise Modulanschluss ist Teil der V3-Arbeiten; die beiden FPGA-Eingabewege bleiben davon unabhängig.

## Cores und MiSTer-Software

Mehrere Core-Varianten können auf derselben SD-Karte liegen; jeweils **ein** FPGA-Core ist aktiv. Es laufen nicht drei MiSTer-Systeme gleichzeitig.

| Eingabeweg | Geplante Softwareverwendung |
|---|---|
| USB | Unveränderte Standard-Cores als Referenz und Fallback |
| DB15 | Vorhandene MiSTer-DB9-Cores mit deren dokumentierter Main-/Menü-Version |
| Direct GPIO | Angepasste Cores, die die neue V3-Pinbelegung lesen |

MiSTer-DB9 umfasst derzeit auch die ARM-Anwendung `MiSTer` und `menu.rbf`, nicht nur Spiele-Cores. Ein Schieberegister benötigt technisch kein eigenes Linux: Eine nur im Core implementierte DB15-Anbindung ist ebenfalls möglich. Für die erste Integration verwenden wir die vorhandene Implementierung; die gemeinsame Softwarebasis und das Laden der Core-Varianten werden getestet. Siehe [Architektur und Software](docs/ARCHITECTURE.md).

Die Menübedienung darf vollständig über USB erfolgen. Native Menübedienung ist keine Voraussetzung für direkte Eingaben im Spiel.

## Video und Audio

Die MiSTer-Core-/Systemlogik erzeugt Bilddaten, Sync und Audiodaten. Für analoge Ausgänge benötigt die Zusatzplatine weiterhin die elektrische Wandlung: RGB-DAC, Audiofilter und Lautsprecherverstärker. V2 bringt hierfür Schaltungen mit; sie werden als Basis übernommen und für die vorgesehenen Core-Varianten geprüft.

Ein externer HDMI-zu-JAMMA-Konverter ist für diesen Aufbau nicht vorgesehen. Analogausgabe und ihre FPGA-Pinbelegung gehören ebenso zum Kompatibilitätstest wie die Eingaben.

## Dokumentation

- [Architektur und feste Designentscheidungen](docs/ARCHITECTURE.md)
- [Pinplanung und reservierte DB15-Signale](docs/PINPLAN.md)
- [Roadmap und Abnahmetests](docs/ROADMAP.md)
- [Status der Fertigungsdaten](production_files/README.md)
- [Änderungsprotokoll](CHANGELOG.md)
- [Arbeitsregeln für Beiträge und Coding-Agenten](AGENTS.md)

## Herkunft und Referenzstand

Ausgangsbasis ist `smdjeff/DE10Jamma` V2, Commit [`fd7a9569fb19a7e95b04638bb1bc645c9801b142`](https://github.com/smdjeff/DE10Jamma/commit/fd7a9569fb19a7e95b04638bb1bc645c9801b142). V2 modernisierte Bestückung, Audioverstärker und Widerstandsbauteile. Die [historische V2-Anleitung](https://github.com/smdjeff/DE10Jamma/blob/fd7a9569fb19a7e95b04638bb1bc645c9801b142/README.md) bleibt über diesen Stand erhalten.

Weitere Grundlagen sind das [ursprüngliche DE10Jamma](https://github.com/MisterJamma/DE10Jamma), dessen [Ghosts-'n-Goblins-Referenzcore](https://github.com/MisterJamma/Arcade-GnG_MiSTer) und die [MiSTer-DB9-Dokumentation](https://github.com/MiSTer-DB9/Documentation). Danke an die ursprünglichen Entwickler. Vorhandene Herkunfts- und Lizenzhinweise bleiben erhalten; dieser Dokumentationsstand erklärt keine neue Lizenz für fremde Dateien.

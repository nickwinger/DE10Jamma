# V3-Pinplan und Schnittstellen

**Status:** Die DB15-Schnittstelle ist festgelegt. Die physische Zuordnung der parallelen V3-Eingänge wird als nächster Schaltplanschritt ergänzt. Diese Datei beschreibt noch keinen fertig gerouteten V3-Pinout.

## Feste DB15-Reservierung

Für Kompatibilität mit dem vorhandenen MiSTer-DB9-DB15-Leser bleiben diese drei Leitungen an ihren bisherigen FPGA-Pins:

| Signal | MiSTer-Port | DE10-Nano-Signalname | FPGA-Package-Pin | Richtung aus FPGA-Sicht |
|---|---|---|---|---|
| `JOY_LOAD` | `USER_IO[0]` | `Arduino_IO15` | `AG11` | Ausgang zum Schieberegister |
| `JOY_CLK` | `USER_IO[1]` | `Arduino_IO14` | `AH9` | Ausgang zum Schieberegister |
| `JOY_DATA` | `USER_IO[5]` | `Arduino_IO10` | `AF15` | Eingang vom Schieberegister |

Quelle: [MiSTer-DB9-Pinreferenz](https://github.com/MiSTer-DB9/Documentation/blob/main/user-guide/pinout-reference.md) und [DB15-Beschreibung](https://github.com/MiSTer-DB9/Documentation/blob/main/user-guide/controllers/neogeo-supergun.md), geprüft am 2026-11-09. Package-Pins und Arduino-Signalnamen sind **keine Stecker-Pinnummern**. Die Header-Pinnummern werden beim Abgleich mit DE10-Nano-Schaltplan, KiCad-Symbol und Constraints ergänzt.

Diese drei Pins gehören im V3-Schaltplan ausschließlich zur DB15-Schnittstelle, nicht gleichzeitig zu einzelnen parallelen JAMMA-Schaltern. Masse und Versorgung kommen zusätzlich zu den drei Signalen hinzu; Pegelwandlung und Registerversorgung werden ausdrücklich festgelegt.

## Welche Zuordnung aus dem alten Direct-Core geändert wird

Der historische DE10Jamma-Ghosts-'n-Goblins-Wrapper verwendet dieselben Leitungen bislang so:

| Historischer Direct-Eingang | Alter FPGA-Port | V3-Ziel |
|---|---|---|
| Spieler 1: Down | `USER_IO[0]` | Separater paralleler GPIO, physische Zuordnung folgt |
| Spieler 1: Left | `USER_IO[1]` | Separater paralleler GPIO, physische Zuordnung folgt |
| Spieler 1: Button 3 | `USER_IO[5]` | Separater paralleler GPIO, physische Zuordnung folgt |

Quelle: [historische `sys_top.v`](https://github.com/MisterJamma/Arcade-GnG_MiSTer/blob/master/sys/sys_top.v). Diese Tabelle beschreibt die RTL-Referenz; sie ersetzt nicht den vollständigen Netzabgleich des V2-KiCad-Projekts.

Die Eingänge werden verschoben, nicht über einen Hardware-Multiplexer mit DB15 geteilt. Andere parallele Leitungen werden ebenfalls in die vollständige V3-Zuordnung aufgenommen, auch wenn sie ihre bisherige Position behalten.

## DB15-Datenbelegung

Die Referenzimplementierung liest beide Spieler über einen gemeinsamen seriellen Datenpfad. Die dokumentierten logischen Spielerbits lauten:

| Bit | Funktion |
|---|---|
| 0 | Right |
| 1 | Left |
| 2 | Down |
| 3 | Up |
| 4–9 | Aktionstasten A–F |
| 10 | Start |
| 11 | Select; für Arcade als Coin zuordnen |
| 12–15 | In dieser Darstellung unbenutzt |

Das ist die logische `joydb`-Belegung, **nicht automatisch die Reihenfolge der Registeranschlüsse auf der Platine**. Die DB9-Dokumentation nennt 26 Takte für einen vollständigen Scan. Die Registerkette wird anhand der tatsächlichen Abtastflanken, Polarität und Bitreihenfolge des Lesers aufgebaut und mit Einzelbitmustern geprüft. Aus 26 Takten werden nicht 26 frei verfügbare Spielertasten abgeleitet.

## Weitere Reservierungen

| Bereich | Planungsvorgabe |
|---|---|
| SDRAM | Anschluss und Signale des ersten MiSTer-SDRAM-Moduls beibehalten |
| RGB / Sync / Audio | V2-Ausgabeschaltungen als Basis; Zuordnung zu den vorgesehenen Standard-Bitstreams dokumentieren |
| Direct GPIO | Eigene Eingänge für 24 Spielersignale; weitere Cabinet-Funktionen separat planen |
| USB | HID-Encoder an USB-OTG/Hub, kein Verbrauch der drei DB15-Signale |
| SNAC | Nur nach Abschluss des Pflicht-Pinplans ergänzen; keine pauschale Reservierung aller USER_IO-Leitungen vorab |

Nicht jeder physisch herausgeführte Pin ist in jedem Bitstream unbenutzt. Bezeichnungen wie `VGA_EN`, `BTN_RESET` oder `IO_SDA` werden daher nicht allein aufgrund eines alten Kommentars als freigegebene Eingänge eingetragen. Die Zuordnung wird gegen die tatsächlichen verwendeten Pin-Constraints und Funktionen abgeglichen.

## Verbindliches Format der vollständigen Zuordnung

Für jede Verbindung wird beim nächsten Schaltplanschritt eine Zeile mit folgenden Feldern angelegt:

`JAMMA/Kick-Kontakt | Funktion | V2-Netz | V3-Netz | Header + Pinnummer | FPGA-Package-Pin | Richtung | Pegel | Pull-up/-down | Verhalten Standard/DB15/Direct | RTL-Port`

Dazu gehören Versorgung, Masse und alle weiterhin genutzten Video-/Audiosignale. Unzugeordnete Felder werden als TODO gekennzeichnet und nicht durch vermutete Pinnummern ausgefüllt.

## Prüfung beim Eintragen

1. Jede parallele Taste besitzt einen eigenen eindeutig zugeordneten Eingang.
2. Die DB15-Pins bleiben exakt auf den drei oben reservierten Package-Pins.
3. Schaltplan, PCB-Netze und FPGA-Constraints stimmen überein.
4. Alle drei Eingabewege können dauerhaft angeschlossen bleiben, auch bei Reset und Core-Wechsel.
5. USB-, DB15- und Direct-Testfälle funktionieren ohne physisches Umschalten.
6. Ein optionaler SNAC-Anschluss kollidiert nicht mit den Pflichtfunktionen; gemeinsamer Portbesitz und angeschlossene Ausgangstreiber sind ausdrücklich zu klären.

Das sind die Umsetzungsschritte für die festgelegte Architektur, keine Aufforderung, wieder auf einen Hardware-Umschalter zurückzugehen.

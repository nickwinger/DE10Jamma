# V3-Roadmap und Abnahme

Die Architektur ist festgelegt. Offene Punkte sind konkrete Arbeitsaufträge. Ein Häkchen bedeutet ein vorhandenes Ergebnis, nicht bloß eine Absicht.

## Bereits erledigt

- [x] V3-Fork auf Basis von `smdjeff/DE10Jamma` V2, Commit `fd7a9569fb19a7e95b04638bb1bc645c9801b142`.
- [x] USB-HID, DB15 und Direct GPIO als drei dauerhaft angeschlossene Pflicht-Eingabewege dokumentiert.
- [x] Hardware-Umschalter ausgeschlossen; USB-Menübedienung als ausreichend festgelegt.
- [x] SNAC als nachrangige Option dokumentiert.
- [x] DB15-Referenzsignale und historische Pinüberschneidungen dokumentiert.
- [x] Veraltete projektlokale KiCad-Bibliothekspfade auf `libs/` korrigiert.
- [x] Übernommene V2-Fertigungsdaten klar vom V3-Ziel getrennt.

## 1. Schaltplan und vollständiger Pinplan

- [ ] V2-Netze, DE10-Nano-Header und FPGA-Package-Pins vollständig erfassen.
- [ ] Die parallelen JAMMA-Signale auf getrennte V3-GPIOs zuordnen; DB15 auf `USER_IO[0]`, `[1]`, `[5]` belassen.
- [ ] Zwei Spieler mit je vier Richtungen, sechs Aktionstasten, Start und Coin abdecken.
- [ ] Test, Service und Menüfunktionen samt Kick-Harness-/Zusatzanschlüssen festlegen.
- [ ] Villena-kompatible Registerkette samt Bitreihenfolge, LOAD-/CLOCK-Flanken, Versorgung und Pegeln ergänzen.
- [ ] USB-HID-Encoder oder Encoder-Modulanschluss auswählen und die Schalterverzweigung ergänzen.
- [ ] Eingangsaufbereitung, Schutz, Pull-ups, Versorgung und USB-Rückspeiseschutz auslegen.
- [ ] Standard-Bitstreams sowie Reset-/Core-Wechsel-Verhalten in die Pinprüfung aufnehmen.
- [ ] Verbleibende geeignete Pins für optionales SNAC bewerten, ohne den Pflichtumfang zu ändern.

**Ergebnis:** vollständiger Netz-/Pinplan und konsistenter V3-Schaltplan, nicht nur ein geänderter Silkscreen.

## 2. PCB und Fertigung

- [ ] PCB aus dem V3-Schaltplan aktualisieren und alle geänderten Netze routen.
- [ ] Abstandshalter, Bauhöhe, SDRAM-Freiraum und Anschlusszugänglichkeit prüfen.
- [ ] Video-/Audioausgänge, Stecker und Versorgungen eindeutig beschriften.
- [ ] ERC und DRC ausführen; verwendete KiCad-Version, Berichte und begründete Ausnahmen ablegen.
- [ ] Schaltplan/PCB visuell gegenprüfen; Fertigungsparameter für Steckkante, Leiterplatte und Bestückung festlegen.
- [ ] Neue Gerber-, Bohr-, BOM- und Bestückungsdateien aus demselben geprüften Commit erzeugen.
- [ ] V3-Ausgaben getrennt von den vorhandenen V2-Ausgaben versionieren und kennzeichnen.

**Ergebnis:** nachvollziehbares V3-Fertigungspaket. ERC/DRC sind Entwicklungsprüfungen, kein Ersatz für den Hardwaretest.

## 3. RTL und Software

- [ ] Einen kleinen Test-Core für alle parallelen Schalter und den DB15-Leser bereitstellen.
- [ ] Ein Referenzspiel auf die V3-Direct-Pinbelegung portieren.
- [ ] Spieleingabequelle eindeutig auswählen; doppelte USB-/Direct-Aktionen vermeiden.
- [ ] Passende MiSTer-DB9-Main-/Menü-/Core-Kombination als Referenz festhalten.
- [ ] Unveränderte Standard-Cores über USB sowie V3-Direct-Cores auf der gemeinsamen Installation testen.
- [ ] Getrennte Core-/MRA-Namen, Konfigurationen und updategeschützte eigene Builds einrichten.
- [ ] Native RGB-/Sync-/Audioausgabe in allen drei Core-Familien prüfen.

## 4. Hardware-Abnahme

Alle folgenden Tests sind noch offen. Für jeden Test werden Platinenrevision, FPGA-Core, Main-Version, Einstellungen und Ergebnis notiert.

| Test | Erwartetes Ergebnis |
|---|---|
| Jede Taste / jeder Spieler | Richtige Zuordnung, keine Übersprechungen oder Phantomtasten |
| Standard-Core | Spiel über USB-HID bedienbar |
| MiSTer-DB9-Core, DB15-Modus | Spiel über Registereingabe bedienbar |
| V3-Direct-Core | Spiel über einzelne parallele FPGA-Eingänge bedienbar |
| Native Eingaben bei getrennter HID-Datenverbindung | DB15-/Direct-Spielpfad arbeitet unabhängig vom USB-Encoder |
| Menü / Fallback | USB-Menübedienung und Rückkehr zur USB-Spielsteuerung möglich |
| Alle Wege angeschlossen | Wechsel zwischen den Test-Cores ohne Umstecken oder Hardware-Umschalter |
| Taste gehalten beim Core-Wechsel | Keine unerwarteten Rücksetz-, Menü- oder Dauereingaben |
| Tastendruck und Loslassen | Keine durch doppelte Quellen verlängerten oder wiederholten Aktionen |
| Reset, Boot, Neukonfiguration | Sichere Pegel und definierte Zustände |
| CRT-Video | Passende RGB-Pegel, Sync-Polarität und native Bildfrequenzen |
| Audio | Richtige Lautsprecheranschlüsse, Pegel, Lautstärke und thermisches Verhalten |
| Versorgung | Keine unerlaubte Rückspeisung; Spannung und Strom unter Last im Soll |

Latenz und Jitter werden getrennt für USB, DB15 und Direct GPIO gemessen. Es werden keine pauschalen Null-Latenzwerte aus dem Projektnamen abgeleitet.

## Nächster Arbeitsschritt

Den vollständigen V2-Netz-/Headerplan in das Format aus [PINPLAN.md](PINPLAN.md) übertragen und daraus die neue parallele V3-Belegung im Schaltplan umsetzen. Die drei DB15-Leitungen und die Architektur werden dabei nicht erneut zur Entscheidung gestellt.

# V3-Roadmap und Abnahme

Die Architektur ist festgelegt. Offene Punkte sind konkrete Arbeitsaufträge. Ein Häkchen bedeutet ein vorhandenes Ergebnis, nicht bloß eine Absicht.

## Kostenrahmen: eine einzige PCB

Dies ist ein privates Einzelstückprojekt. Geplant ist **genau eine V3-PCB**, keine Prototypenserie und keine eingeplante PCB-Nachbestellung. Ziel ist, dass der erste gefertigte Aufbau die festgelegten Funktionen erfüllt. USB-HID, DB15 und Direct GPIO bleiben Pflichtumfang; an der Architektur ändert sich nichts.

Daraus folgt: Die vorab möglichen Schaltungs-, Simulations-, Synthese-, Layout- und Mechanikprüfungen werden vor die Bestellung gelegt. Die Bestellung folgt erst auf die dokumentierte Fertigungsfreigabe, nicht bereits auf das erste vollständige Layout. Die einzige Platine wird anschließend in Betrieb genommen und geprüft; dies ist keine geplante Wegwerf-Prototypenrunde.

Eine fehlerfreie Erstfunktion ist das Entwicklungsziel, keine vor dem Hardwaretest bewiesene Eigenschaft. Vor der Bestellung werden die tatsächlich ausgeführten Prüfungen und verbleibende, nur am realen Board prüfbare Eigenschaften offengelegt. Zusätzliche Fertigungen oder Ausgaben sind nicht Teil des Plans und dürfen nicht ohne neue ausdrückliche Entscheidung vorausgesetzt werden.

## Bereits erledigt

- [x] V3-Fork auf Basis von `smdjeff/DE10Jamma` V2, Commit `fd7a9569fb19a7e95b04638bb1bc645c9801b142`.
- [x] USB-HID, DB15 und Direct GPIO als drei dauerhaft angeschlossene Pflicht-Eingabewege dokumentiert.
- [x] Hardware-Umschalter ausgeschlossen; USB-Menübedienung als ausreichend festgelegt.
- [x] SNAC als nachrangige Option dokumentiert.
- [x] DB15-Referenzsignale und historische Pinüberschneidungen dokumentiert.
- [x] Veraltete projektlokale KiCad-Bibliothekspfade auf `libs/` korrigiert.
- [x] Übernommene V2-Fertigungsdaten klar vom V3-Ziel getrennt.
- [x] Einzelstückbudget und Prüfung vor der einzigen PCB-Bestellung verbindlich dokumentiert.

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

## 2. PCB und Fertigungsvorbereitung

- [ ] PCB aus dem V3-Schaltplan aktualisieren und alle geänderten Netze routen.
- [ ] Abstandshalter, Bauhöhe, SDRAM-Freiraum und Anschlusszugänglichkeit prüfen.
- [ ] Headerorientierung, Pin 1, Steckverbinderabstände, Bohrungen und JAMMA-Steckkante anhand der Maßzeichnungen und eines maßstäblichen 1:1-Ausdrucks mit der vorhandenen DE10-Nano-/SDRAM-/Kühlkörper-Hardware abgleichen.
- [ ] Bauteil-Pinouts und PCB-Footprints gegen die Datenblätter der tatsächlich vorgesehenen Bestellteile prüfen.
- [ ] Video-/Audioausgänge, Stecker und Versorgungen eindeutig beschriften.
- [ ] ERC und DRC ausführen; verwendete KiCad-Version, Berichte und begründete Ausnahmen ablegen.
- [ ] Schaltplan/PCB visuell gegenprüfen; Fertigungsparameter für Steckkante, Leiterplatte und Bestückung festlegen.
- [ ] Neue Gerber-, Bohr-, BOM- und Bestückungsdateien aus demselben geprüften Commit erzeugen.
- [ ] V3-Ausgaben getrennt von den vorhandenen V2-Ausgaben versionieren und kennzeichnen.

**Ergebnis:** nachvollziehbares V3-Fertigungspaket zur abschließenden Freigabe. Noch keine Bestellung. ERC/DRC sind Entwicklungsprüfungen, kein Ersatz für den Hardwaretest.

## 3. RTL und Software

- [ ] Einen kleinen Test-Core für alle parallelen Schalter und den DB15-Leser bereitstellen.
- [ ] Ein Referenzspiel auf die V3-Direct-Pinbelegung portieren.
- [ ] Spieleingabequelle eindeutig auswählen; doppelte USB-/Direct-Aktionen vermeiden.
- [ ] Vor der Fertigung RTL-Simulationen für einzelne und gleichzeitige Tasten, beide Spieler, Loslassen, DB15-Bitreihenfolge/-Flanken, Reset und Quellenwahl ausführen und Ergebnisse ablegen.
- [ ] Test-Core und Direct-Referenz-Core vor der Fertigung synthetisieren; Pin-Constraints, I/O-Standards, Ressourcen und Timing-Berichte gegen den Schaltplan prüfen. Nicht modellierte Hardwareeigenschaften ausdrücklich abgrenzen.
- [ ] Passende MiSTer-DB9-Main-/Menü-/Core-Kombination als Referenz festhalten.
- [ ] Unveränderte Standard-Cores über USB sowie V3-Direct-Cores auf der gemeinsamen Installation testen; Tests der fertigen V3-Platine gehören zur Hardware-Abnahme nach der Fertigung.
- [ ] Getrennte Core-/MRA-Namen, Konfigurationen und updategeschützte eigene Builds einrichten.
- [ ] Native RGB-/Sync-/Audioausgabe in allen drei Core-Familien prüfen; Messungen am JAMMA-Ausgang gehören zur Hardware-Abnahme nach der Fertigung.

## Freigabe vor der einzigen PCB-Bestellung

Diese Liste ist vor einer Bestellempfehlung abzuarbeiten. Es werden keine Häkchen allein aufgrund einer Planungsbeschreibung gesetzt.

- [ ] Jede Pflichtverbindung ist vom JAMMA-/Kick-Kontakt über den Schaltplan und das PCB-Netz bis zum DE10-Header und FPGA-Package-Pin abgeglichen.
- [ ] Versorgung, Signalpegel, Pull-ups, Ausgangstreiber und dauerhaft angeschlossene Eingabewege sind für die vorgesehenen Standard-, DB15- und Direct-Bitstreams sowie Boot/Reset geprüft.
- [ ] Bauteile und Footprints sind geprüft; Bauteilverfügbarkeit, Montageverfahren und Bestückungsumfang sind geklärt.
- [ ] RTL-Simulation und Synthese/Timing der eigenen Referenz-Builds sind dokumentiert; die vollständige Signalzuordnung stimmt mit der Hardware überein.
- [ ] ERC/DRC und visuelle Schaltplan-/Layoutkontrolle sind abgeschlossen, einschließlich begründeter Ausnahmen.
- [ ] Mechanischer Maßabgleich und 1:1-Passkontrolle sind abgeschlossen.
- [ ] Gerber und Bohrdaten sind separat kontrolliert und stammen zusammen mit BOM und Bestückungsdaten aus dem freizugebenden Quellstand.
- [ ] Fertigerangebot, Stückzahl, Bestückung und Gesamtkosten passen zur Einzelstückvorgabe. Mindestmengen oder weitere Ausgaben werden nicht stillschweigend als genehmigt behandelt.
- [ ] Verbleibende Hardwaretestpunkte und Grenzen der Vorabprüfung sind konkret dokumentiert. Der Eigentümer entscheidet auf dieser Grundlage über die einzige Bestellung.

## 4. Inbetriebnahme und Hardware-Abnahme des Einzelstücks

Alle folgenden Tests sind noch offen. Für jeden Test werden Platinenrevision, FPGA-Core, Main-Version, Einstellungen und Ergebnis notiert. Diese Prüfung erfolgt an der einzigen vorgesehenen Platine, nicht in einer eingeplanten Serie von Test-PCBs.

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

Den vollständigen V2-Netz-/Headerplan in das Format aus [PINPLAN.md](PINPLAN.md) übertragen und daraus die neue parallele V3-Belegung im Schaltplan umsetzen. Die drei DB15-Leitungen und die Architektur werden dabei nicht erneut zur Entscheidung gestellt. Eine Fertigungsbestellung erfolgt erst nach der oben dokumentierten Freigabe.

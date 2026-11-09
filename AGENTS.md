# Arbeitsregeln für DE10Jamma V3

Diese Regeln gelten für Beiträge und Coding-Agenten in diesem Repository. Fachliche Grundlage: [README](README.md), [Architektur](docs/ARCHITECTURE.md), [Pinplan](docs/PINPLAN.md) und [Roadmap](docs/ROADMAP.md).

## Bereits entschiedene Anforderungen

- Basis ist der V2-Fork von `smdjeff/DE10Jamma`, nicht ein Neustart vom alten Originalprojekt.
- Zielhardware ist der Terasic DE10-Nano mit einem MiSTer-SDRAM-Modul und einer direkt angeschlossenen JAMMA-Erweiterungsplatine.
- Drei Pflicht-Eingabewege bleiben gleichzeitig physisch angeschlossen: USB-HID, Villena-kompatibles DB15-Schieberegister und direkte parallele FPGA-GPIOs.
- Kein Hardware-Umschalter und kein Umstecken zwischen diesen drei Wegen. Direct-Eingänge auf getrennte Pins legen; DB15 behält seine drei Referenzpins.
- USB darf das Menü vollständig bedienen. Native Menübedienung ist keine Voraussetzung für native Spieleingaben.
- Ein USB-Hub ersetzt keinen HID-Encoder. Die HID-Elektronik gehört in den Schaltplan beziehungsweise an einen dokumentierten Modulanschluss.
- SNAC ist optional, nur soweit passende GPIOs nach dem Pflichtumfang verbleiben. Nicht vorsorglich alle USER_IO-Pins reservieren oder den Pflichtumfang dafür reduzieren.
- Standard-Cores bleiben als USB-Fallback ein Entwicklungsziel; eigene Direct-Cores verwenden die V3-Pinbelegung.

## Verbindlicher Kosten- und Fertigungsrahmen

- Privates Einzelstückprojekt: geplant ist genau eine V3-PCB für den eigenen Aufbau. Keine Prototypenserie und keine eingeplanten kostenpflichtigen PCB-Nachbestellungen.
- Ziel ist ein funktionierender Erstaufbau. Die Entwicklung verlagert die möglichen Prüfungen vor die Bestellung: Netz-/Pinabgleich, Bauteil- und Footprintkontrolle, RTL-Simulation, Synthese/Timing, ERC/DRC und mechanische Kontrolle.
- Die drei Pflicht-Eingabewege bleiben unverändert. Die Budgetvorgabe ist kein Anlass, die Architektur erneut zur Diskussion zu stellen.
- V2-Schaltungsteile soweit sinnvoll übernehmen und nachvollziehbar prüfen; bloße Übernahme ist kein Nachweis, dass V2 bereits vollständig hardwaregetestet ist.
- Fertigungsunterlagen erst nach der dokumentierten Freigabe in der Roadmap zur Bestellung empfehlen. Fertiger-Stückzahl, Bestückungsumfang und Gesamtkosten müssen zur Einzelstückvorgabe passen; keine zusätzlichen Ausgaben oder Mindestmengen stillschweigend als akzeptiert behandeln.
- Keine hundertprozentige Erstfunktion oder Hardwarevalidierung allein aus Simulation, Synthese oder ERC/DRC behaupten. Verbleibende, erst am gefertigten Board prüfbare Eigenschaften vor der Bestellung konkret benennen.
- Die einzige gefertigte Platine wird bei der Inbetriebnahme getestet. Diese Abnahme ist keine geplante Wegwerf-Prototypenrunde. Notwendige zusätzliche Ausgaben bedürfen einer neuen ausdrücklichen Entscheidung des Eigentümers.

## Umsetzung

- Offene Pinzuordnung, Routing und Tests sind TODOs. Architekturentscheidungen nicht ohne konkrete neue Evidenz erneut zur Diskussion stellen.
- GPIOs, Steckerpins und FPGA-Package-Pins unterscheiden. Keine ungeprüften Ersatzpins als fertige Zuordnung ausgeben.
- Hardwarequellen sind `.kicad_sch` und `.kicad_pcb`; Gerber sind generierte Ergebnisse. Schaltplan und PCB konsistent ändern.
- Originaldateien, Herkunft und Lizenzhinweise erhalten. Keine neue Lizenz für übernommene Dateien erfinden.
- V2-Fertigungsdaten nicht als V3 umbenennen oder freigeben. V3-Ausgaben aus einem dokumentierten Quellstand neu erzeugen.
- Drei physische Eingabewege sind nicht drei gleichzeitig laufende FPGA-Cores. Dokumentation muss Hardware, RTL, Main-Anwendung und Linux auseinanderhalten.
- Bestehende fremde Core-Builds nicht ohne Test als vollständig kompatibel kennzeichnen. Bei eigenen Builds verzögerte Doppeleingaben vermeiden.

## Nach jeder Änderung

Änderungsumfang und tatsächlich ausgeführte Prüfungen dokumentieren. Statische Dateiprüfung, KiCad-ERC/DRC, Synthese und Hardwaretests sind unterschiedliche Nachweise. Geplante Funktionen nicht als implementiert oder getestet ausgeben. Die Roadmap entsprechend fortschreiben.

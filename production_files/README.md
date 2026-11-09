# Fertigungsdaten: derzeit V2

Die bereits vorhandenen Dateien in diesem Verzeichnis stammen aus dem übernommenen V2-Stand von `smdjeff/DE10Jamma`, Commit `fd7a9569fb19a7e95b04638bb1bc645c9801b142`:

- `BOM-DE10Jamma.csv`
- `CPL-DE10Jamma.csv`
- `GERBER-DE10Jamma.zip`

Auch `../fabrication.zip` und `../DE10Jamma.pdf` gehören zum übernommenen V2-Stand.

**Diese Dateien enthalten keine V3-Schieberegister-/HID-Erweiterung und keine neue V3-GPIO-Belegung. Sie sind keine V3-Fertigungsfreigabe.** Sie bleiben als Referenz erhalten.

## Ablauf für ein zukünftiges V3-Fertigungspaket

1. V3-Schaltplan und PCB aus demselben Git-Commit verwenden.
2. ERC-/DRC-Berichte, KiCad-Version, visuelle Kontrolle und begründete Ausnahmen dokumentieren.
3. Gerber, Bohrdaten, BOM und Bestückungsdaten neu aus diesem Stand exportieren.
4. Dateien getrennt unter einer eindeutigen V3-Revisionsbezeichnung ablegen.
5. Eine Paketbeschreibung mit Quellcommit, Fertigungsparametern und Prüfstatus hinzufügen. Hardwaretests getrennt ausweisen.

Die Gerber-Dateien werden nicht direkt als Ersatz für Änderungen an den KiCad-Quellen bearbeitet. Aktueller Arbeitsplan: [V3-Roadmap](../docs/ROADMAP.md).

# Änderungsprotokoll

## V3 – Entwicklung, 2026-11-09

### Hinzugefügt

- V3-Zielbeschreibung für ein DE10-Nano-JAMMA-Board mit USB-HID, DB15-Schieberegister und Direct GPIO.
- Verbindliche Architektur: alle drei Wege dauerhaft angeschlossen, kein Hardware-Umschalter; SNAC optional.
- Dokumentation der Softwareebenen, Core-Varianten, Video-/Audioausgabe und USB-HID-Elektronik.
- Reservierung der drei DB15-Referenzsignale und Vorlage für den vollständigen V3-Pinplan.
- Roadmap, Abnahmematrix, Arbeitsregeln und Kennzeichnung der vorhandenen Fertigungsdaten als V2.

### Korrigiert

- Projektlokaler Footprintpfad `Jamma.pretty` auf `libs/Jamma.pretty`.
- Projektlokale Symbolpfade `Jamma.lib` und `DE10Jamma-rescue.lib` auf ihre vorhandenen Dateien unter `libs/`.

### Unverändert

- V2-Schaltplan, PCB-Leiterbahnen, PDF, Fotos, BOM/CPL und Gerber-/Fertigungsarchive.
- Noch keine V3-RTL-Implementierung, Synthese, ERC-/DRC-Freigabe oder Hardware-Abnahme.

## Übernommene Basis

`smdjeff/DE10Jamma` V2, Commit `fd7a9569fb19a7e95b04638bb1bc645c9801b142`. Die ursprüngliche Versionsgeschichte bleibt in Git erhalten.

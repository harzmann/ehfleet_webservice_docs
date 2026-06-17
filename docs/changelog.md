# Änderungsprotokoll

Alle wichtigen Änderungen an diesem Projekt werden in dieser Datei dokumentiert.

Das Format orientiert sich an Keep a Changelog. Die Versionierung folgt der vorhandenen Assembly-Version aus dem Visual-Studio-Projekt.

## [Unveröffentlicht]

### Hinzugefügt

- Automatische Erstellung eines installierbaren Kundenpakets mit Web-Deploy-Paket, IIS-Installationsskript, Kurzinstallationsanleitung und SHA256-Prüfsumme.
- Automatische Aktualisierung des öffentlichen MkDocs-Repositories `harzmann/ehfleet_webservice_docs` bei Push auf `master`.
- Öffentliche Download-, Installations- und Änderungsseiten für GitHub Pages.

### Geändert

- README und Änderungsprotokoll wurden auf deutsche Sprache mit Umlauten umgestellt.
- Der Deployment-Workflow veröffentlicht die Kundendokumentation zusätzlich zum bestehenden IIS-Deployment.

## [1.3.10.0] - 2026-06-12

### Hinzugefügt

- GitHub-Migrationsdokumentation für den ASMX-Webservice.
- GitHub-Actions-Workflow für Build und Deployment per MSDeploy.
- IIS-Installationsskript für Kundeninstallationen mit Web-Deploy-Paket.
- `web.config.template` mit Platzhaltern für die Datenbankverbindung.
- Lokale Debug-Übersteuerung der Verbindungszeichenfolge über `EHFLEET_CONNECTION_STRING`.
- `WorkshopItems.GetStockInventoryList` für Lagerbestandsdaten aus `abfr_lagerbestand`.
- `WorkshopItems.DeleteReceiptPosition` zum Löschen von Lieferanten-Lagerzugängen anhand der Bewegungs-ID.
- `WorkshopItems.GetLabelPrinters` zur Ermittlung von MDE-Etikettendruckern.
- `WorkshopItems.PrintReceiptLabel` für serverseitigen Etikettendruck aus MDE-Wareneingängen.
- Idempotentes MDE-Schema-Skript für Etikettendruck mit Druckern, Vorlagen, Gerätezuordnungen und Druckprotokoll.

### Geändert

- `web.config` enthält jetzt einen Platzhalter für `EHFleetConnectionString` statt einer kundenspezifischen Standardverbindung.
- Das GitHub-Actions-Deployment baut das Web-Deploy-Paket auf einem Entwicklungsrunner und installiert es remote auf `192.168.100.124` per MSDeploy.
- `CreateReceiptPosition` verwendet den Berechtigungspfad `frmLagerzugang` nun konsistent über die Klassenbibliothek.
- `GetStockInventoryList` akzeptiert einen leeren Parameter `ArtikelID` von ASMX-Clients und behandelt ihn als `0`.
- Webservice-Endpunkte lösen die Datenbankverbindung über einen gemeinsamen Helfer auf, bevor sie auf `web.config` zurückfallen.
- Local-IIS-Debugging kann die Testdatenbankverbindung aus `LocalDebuggerEnvironment` lesen, ohne `web.config` zu ändern.
- Web-Deploy-Pakete schließen `.vbproj.user` aus, damit lokale Debug-Zugangsdaten nicht deployt werden.
- `web.config.template` enthält Binding Redirects für die JSON-Laufzeitabhängigkeiten der Etikettenvorlagen.


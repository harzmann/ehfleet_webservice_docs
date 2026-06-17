# Installation des EHFleet Webservice

Diese Anleitung richtet sich an Administratoren, die den Webservice auf einem Windows-IIS-Server installieren oder aktualisieren.

Der Installer unterstützt ausschließlich SQL-Authentifizierung für die Datenbankverbindung.

## Voraussetzungen

- Windows Server mit administrativem Zugriff.
- IIS mit ASP.NET 4.x.
- .NET Framework 4.8.
- Microsoft Web Deploy 3.6.
- Zugriff auf die EHFleet-Datenbank.
- SQL-Zugangsdaten für die Datenbank.
- Optional WinGet, damit das Installationsskript Web Deploy automatisch nachinstallieren kann.

## Download und Prüfung

1. Installationspaket herunterladen: [EHFleet_Webservice_1.3.10.0_f62500cf_Installation.zip](assets/downloads/EHFleet_Webservice_1.3.10.0_f62500cf_Installation.zip)
2. Optional Prüfsumme herunterladen: [EHFleet_Webservice_1.3.10.0_f62500cf_Installation.zip.sha256](assets/downloads/EHFleet_Webservice_1.3.10.0_f62500cf_Installation.zip.sha256)
3. SHA256 vergleichen:

```powershell
Get-FileHash -Algorithm SHA256 ".\EHFleet_Webservice_1.3.10.0_f62500cf_Installation.zip"
```

Erwartete SHA256:

```text
c2be324cf170a70ebaebd62ad101785ed2abe358b3c943746478d49e6a35af47
```

## Neuinstallation Schritt für Schritt

1. Wartungsfenster abstimmen und sicherstellen, dass kein alter EHFleet-Webservice unter demselben IIS-Anwendungsnamen produktiv verwendet wird.
2. Installationspaket auf den IIS-Server kopieren, zum Beispiel nach `C:\Install\EHFleetWebservice`.
3. ZIP-Datei vollständig entpacken.
4. Prüfsumme der heruntergeladenen ZIP-Datei mit der oben angegebenen SHA256-Prüfsumme vergleichen.
5. PowerShell als Administrator starten.
6. In den entpackten Paketordner wechseln.
7. Datenbankserver, Datenbankname, SQL-Benutzer und SQL-Passwort bereitlegen.
8. Installation mit SQL-Authentifizierung ausführen.
9. Nach Abschluss die angezeigte Smoke-Test-URL prüfen.
10. In IIS kontrollieren, dass Website, Anwendung und App-Pool vorhanden sind und der App-Pool gestartet ist.
11. Aus einem Clientnetz die ASMX-Testseite öffnen und eine fachliche Anmeldung beziehungsweise MDE-/Werkstattfunktion testen.

## Neuinstallation mit SQL-Authentifizierung

```powershell
.\Install-EHFleetWebservice.ps1 `
  -SiteName "Default Web Site" `
  -AppName "ehfleet_webservice" `
  -AppPoolName "ehfleet_webservice" `
  -Port 8080 `
  -PhysicalPath "C:\inetpub\wwwroot\ehfleet_webservice" `
  -PackagePath ".\ehfleet_webservice.zip" `
  -SqlServer ".\SQL2019" `
  -Database "EHFleet" `
  -SqlUser "ehfleet" `
  -SqlPassword "<passwort>"
```

## Was das Skript einrichtet

- IIS-Rollen und ASP.NET-Features, soweit sie fehlen.
- IIS-App-Pool mit .NET CLR v4.0 und integriertem Pipeline-Modus.
- IIS-Anwendung unter dem angegebenen Site- und Anwendungsnamen.
- NTFS-Leserechte für die App-Pool-Identität.
- Web-Deploy-Synchronisierung des Pakets.
- `EHFleetConnectionString` in der Zielkonfiguration.
- Smoke-Test auf `WorkshopItems.asmx`.

## Funktionstest

Nach der Installation sollte diese URL erreichbar sein:

```text
http://<server>:8080/ehfleet_webservice/WorkshopItems.asmx
```

Zusätzlich sollten Anmeldung und ausgewählte MDE-/Werkstattfunktionen gegen die Produktivdatenbank geprüft werden.

## Update Schritt für Schritt

1. Aktuelle Version, Installationspfad, IIS-Site, IIS-Anwendungsname, App-Pool und Datenbankverbindung dokumentieren.
2. Wartungsfenster ankündigen und aktive Benutzer abmelden lassen.
3. Sicherung des bestehenden Zielverzeichnisses erstellen, zum Beispiel `C:\Backup\EHFleetWebservice\<datum>`.
4. Aktuelle `web.config` sichern, damit die bisherige Verbindungszeichenfolge nachvollziehbar bleibt.
5. Neues Installationspaket herunterladen und auf den IIS-Server kopieren.
6. SHA256-Prüfsumme mit der Downloadseite vergleichen.
7. ZIP-Datei in einen neuen temporären Ordner entpacken.
8. PowerShell als Administrator starten und in den entpackten Paketordner wechseln.
9. Installationsskript mit denselben Parametern wie bei der bestehenden Installation ausführen.
10. Das Skript aktualisiert die vorhandene IIS-Anwendung und verwendet App-Pool, Site und Zielpfad weiter.
11. Smoke-Test auf `WorkshopItems.asmx` prüfen.
12. Anwendung im Browser oder MDE-Client fachlich testen.
13. Bei Problemen die Sicherung des Zielverzeichnisses zurückkopieren und das vorherige Paket erneut mit denselben Parametern installieren.

Beispiel für ein Update mit SQL-Authentifizierung:

```powershell
.\Install-EHFleetWebservice.ps1 `
  -SiteName "Default Web Site" `
  -AppName "ehfleet_webservice" `
  -AppPoolName "ehfleet_webservice" `
  -Port 8080 `
  -PhysicalPath "C:\inetpub\wwwroot\ehfleet_webservice" `
  -PackagePath ".\ehfleet_webservice.zip" `
  -SqlServer ".\SQL2019" `
  -Database "EHFleet" `
  -SqlUser "ehfleet" `
  -SqlPassword "<passwort>"
```

## Fehlerdiagnose

- PowerShell muss erhöht gestartet sein.
- Web Deploy muss auf dem Zielserver installiert sein.
- Der IIS-App-Pool benötigt Lesezugriff auf das Zielverzeichnis.
- Die Datenbankverbindung muss vom IIS-Server erreichbar sein.
- Bei einem fehlgeschlagenen Smoke-Test zuerst `web.config`, Ereignisanzeige und IIS-Protokolle prüfen.

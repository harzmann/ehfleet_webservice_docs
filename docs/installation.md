# Installation des EHFleet Webservice

Diese Anleitung richtet sich an Administratoren, die den Webservice auf einem Windows-IIS-Server installieren oder aktualisieren.

## Voraussetzungen

- Windows Server mit administrativem Zugriff.
- IIS mit ASP.NET 4.x.
- .NET Framework 4.8.
- Microsoft Web Deploy 3.6.
- Zugriff auf die EHFleet-Datenbank.
- SQL-Zugangsdaten oder Windows-Authentifizierung für die Datenbank.
- Optional WinGet, damit das Installationsskript Web Deploy automatisch nachinstallieren kann.

## Download und Prüfung

1. Installationspaket herunterladen: [EHFleet_Webservice_1.3.10.0_22d05f2f_Installation.zip](assets/downloads/EHFleet_Webservice_1.3.10.0_22d05f2f_Installation.zip)
2. Optional Prüfsumme herunterladen: [EHFleet_Webservice_1.3.10.0_22d05f2f_Installation.zip.sha256](assets/downloads/EHFleet_Webservice_1.3.10.0_22d05f2f_Installation.zip.sha256)
3. SHA256 vergleichen:

```powershell
Get-FileHash -Algorithm SHA256 ".\EHFleet_Webservice_1.3.10.0_22d05f2f_Installation.zip"
```

Erwartete SHA256:

```text
b04a5e43e7160a11e3f8dde8912e94aba2222a10241b78e2b5b01c1b2af972e8
```

## Installation mit SQL-Authentifizierung

1. ZIP-Datei entpacken, zum Beispiel nach `C:\Install\EHFleetWebservice`.
2. PowerShell als Administrator starten.
3. Installation ausführen:

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

## Installation mit Windows-Authentifizierung

```powershell
.\Install-EHFleetWebservice.ps1 `
  -PackagePath ".\ehfleet_webservice.zip" `
  -SqlServer ".\SQL2019" `
  -Database "EHFleet" `
  -UseWindowsAuth
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

## Aktualisierung

Für ein Update das neue Paket herunterladen, entpacken und das Installationsskript mit denselben Parametern erneut ausführen. Die IIS-Anwendung und der App-Pool werden wiederverwendet.

## Fehlerdiagnose

- PowerShell muss erhöht gestartet sein.
- Web Deploy muss auf dem Zielserver installiert sein.
- Der IIS-App-Pool benötigt Lesezugriff auf das Zielverzeichnis.
- Die Datenbankverbindung muss vom IIS-Server erreichbar sein.
- Bei einem fehlgeschlagenen Smoke-Test zuerst `web.config`, Ereignisanzeige und IIS-Protokolle prüfen.

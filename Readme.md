# VisualsACC für Windows

Eigenständige Windows-Version von VisualsACC für **umsatzsteuerpflichtige** Unternehmen (Regelbesteuerung)
mit **E-Rechnung**. Sie hat einen eigenen Programmstand in `src/` und ist komplett getrennt von der Mac-Version
(`../VisualsACC`, Kleinunternehmer). Änderungen an der einen Version wirken sich **nicht** auf die andere aus.

## Umfang

- **Übersicht:** Umsatz netto, Ausgaben netto, Ergebnis, USt-Zahllast (Umsatzsteuer minus Vorsteuer) mit Kennzahlen
  für die Umsatzsteuer-Voranmeldung, offene Forderungen und offene Ausgaben
- **Kunden und Lieferanten**
- **Angebote und Rechnungen** mit Zahlungen und Storno
  - Steuersatz je Position: 19 %, 7 %, 0 % (Nullsatz), steuerfrei, Reverse Charge (§ 13b),
    innergemeinschaftliche Lieferung, Ausfuhr. Die Pflichthinweise kommen automatisch auf die Rechnung.
  - Netto, USt je Steuersatz und Brutto im Editor, in der Ansicht und auf der PDF
- **E-Rechnung:** Knöpfe „E-Rechnung (PDF)“ (ZUGFeRD / Factur-X, Profil EN 16931) und „XRechnung (XML)“ (XRechnung 3.0)
  bei jeder abgeschlossenen Rechnung. Beim Mailversand wird die E-Rechnung automatisch in die PDF eingebettet;
  auf Wunsch wird zusätzlich die XRechnung-XML angehängt (Einstellungen → E-Mail-Vorlagen).
- **Leistungen & Artikel** mit Standard-Steuersatz
- **Ausgaben** mit Belegerkennung (liest auch Vorsteuer und Netto), als bezahlt markieren
- **E-Mail-Versand:** Beim ersten Mal wird nach dem Mail-Programm gefragt
- **Einstellungen:** Standard-Steuersatz, Soll-/Ist-Versteuerung, Mail-Programm

Nicht enthalten (anders als am Mac): Mahnungen, sonstige Einnahmen, wiederkehrende Ausgaben, Anlagen & AfA,
EÜR, Onlineshops, Protokoll-Ansicht, PLZ→Ort-Abfrage.

### Für E-Rechnungen nötig

In den Einstellungen: Firmenname, Anschrift, E-Mail, Telefon, Steuernummer oder USt-IdNr. und IBAN.
Beim Kunden: Name, Anschrift und E-Mail. Für Behörden die **Leitweg-ID** beim Kunden eintragen.
Bei Reverse Charge und innergemeinschaftlicher Lieferung braucht der Kunde eine USt-IdNr.
Fehlt etwas, sagt VisualsACC genau, was.

**Prüfen:** Die E-Rechnungen wurden nach EN 16931 / XRechnung 3.0 erstellt und intern auf Rechenregeln geprüft,
aber nicht mit dem offiziellen KoSIT-Prüfprogramm validiert. Vor dem ersten echten Einsatz eine Testrechnung
mit einem Online-Validator prüfen (z. B. dem E-Rechnungs-Validator deiner Steuerberatung oder des Landes).
Die ZUGFeRD-PDF enthält die XML und die Factur-X-Metadaten, ist aber kein zertifiziertes PDF/A-3.
Rechtlich maßgeblich ist der XML-Teil.

## Installation auf dem Windows-PC

1. `dist/VisualsACC-Setup-1.0.exe` auf den PC kopieren (USB-Stick, Cloud, E-Mail).
2. Doppelklick auf das Setup.
   - Erscheint **„Der Computer wurde durch Windows geschützt“**: auf **„Weitere Informationen“** → **„Trotzdem ausführen“**
     klicken. Die Warnung kommt, weil das Setup nicht kostenpflichtig signiert ist.
3. Dem Assistenten folgen. Administratorrechte sind nicht nötig.
   Installiert wird nach `%LOCALAPPDATA%\Programs\VisualsACC`. Startmenü- und Desktop-Verknüpfung werden angelegt.
4. VisualsACC startet in einem eigenen Fenster (Microsoft Edge im App-Modus).

**Deinstallieren:** Windows-Einstellungen → Apps → Installierte Apps → VisualsACC → Deinstallieren.
Deine Daten bleiben dabei erhalten.

**Update:** Ein neueres Setup einfach darüber installieren. Die Daten bleiben erhalten.

## Daten

`%APPDATA%\VisualsACC` (in den Einstellungen gibt es den Knopf „Datenordner öffnen“).
Versendete PDFs und E-Rechnungen liegen im Unterordner `versand`.

Daten vom Mac zu übernehmen ist **nicht vorgesehen**: Die Mac-Version rechnet als Kleinunternehmer ohne Umsatzsteuer,
die Windows-Version mit Umsatzsteuer. Starte unter Windows mit einem leeren Datenbestand.

## Windows-Besonderheiten

| Funktion | Windows |
|---|---|
| App-Fenster | Microsoft Edge im App-Modus (auf Windows 10/11 vorinstalliert) |
| Belegerkennung | Windows-Texterkennung (Windows.Media.Ocr); die Sprache Deutsch muss installiert sein (auf deutschem Windows Standard). E-Rechnungen (XML/ZUGFeRD) werden direkt ausgelesen. |
| Drucken / PDF | Edge-Druckdialog → Drucker „Als PDF speichern“; E-Rechnung-PDF über den Knopf |
| Per E-Mail senden | Outlook (klassisch) und Thunderbird bekommen einen Entwurf mit Anhängen. Bei „Anderes Programm / Webmail“ öffnet sich der Entwurf ohne Anhang, und die Datei wird im Explorer markiert. |

**Hinweis:** Die Windows-Version wurde auf dem Mac gebaut und getestet (Programmlogik, Rechnungen, E-Rechnung, PDF-Einbettung).
Die Windows-spezifischen Teile (Edge-Fenster, Texterkennung, Outlook/Thunderbird) sind noch nicht auf einem echten
Windows-PC getestet.

## Neu bauen (auf dem Mac)

```bash
cd ~/Projekte/VisualsACC-Windows
./build.sh
```

Ergebnis: `dist/VisualsACC-Setup-<Version>.exe`. Die Version steht oben in `build.sh`.

Benötigt werden:
- `cache/`: Windows-Python 3.13.12 (embeddable, von python.org; die SHA-256-Prüfsumme wird bei jedem Bau geprüft)
  und NSIS 3.12 (von nsis.sourceforge.io)
- `werkzeug/nsis/`: der NSIS-Installer-Baukasten (offizielles Windows-Paket plus selbst übersetzter `Bin/makensis` für macOS)

## Dateien

- `src/server.py`: Programmlogik und Datenbank (mit Umsatzsteuer)
- `src/erechnung.py`: E-Rechnung (CII-XML für XRechnung / ZUGFeRD, Einbetten in die PDF)
- `src/belegerkennung.py`: Belegerkennung inkl. Umsatzsteuer
- `src/launcher.pyw`: startet Programmlogik und App-Fenster und beendet beim Schließen alles
- `src/windows_hilfen.py`: Texterkennung, PDF über Edge, Mail-Entwurf, Ordner öffnen
- `src/belegleser.ps1`: Windows-Texterkennung (PowerShell)
- `src/web/`: Oberfläche (`windows.js` enthält die Windows-Ergänzungen)
- `windows/start.nsi`, `windows/setup.nsi`: VisualsACC.exe und Setup (NSIS); `windows/ico.py`, `windows/icon.swift`: Programm-Icon
- `werkzeug/`: einmalige Umbau-Skripte (bereits angewendet) und NSIS

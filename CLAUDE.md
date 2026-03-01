# Bad Peggy - Projektnotizen

## Übersicht

Bad Peggy ist ein Fork des Original-Projekts von coderslagoon. Das Original-Repository existiert nicht mehr auf GitHub.

- **GitHub:** https://github.com/HJS-cpu/BadPeggy
- **GitLab:** https://gitlab.com/HJS-cpu/BadPeggy
- **Website:** https://hjs.page.gd/badpeggy/
- **Aktuelle Version:** 2.4.2
- **Java Version:** 17 (OpenJDK Temurin)
- **Build System:** Maven
- **GUI Framework:** SWT (Eclipse)
- **Plattform:** Nur Windows (x64)
- **Copyright:** coderslagoon und HJS, GPLv3
- **Sprachen:** Deutsch, Englisch

## Lokale Pfade

- **Quellcode:** `C:\Users\HJS\Claude.ai\BadPeggy`
- **Installierte Version:** `C:\Tools\Multimedia Tools\Bad Peggy`

## Implementierte Features (in diesem Fork)

### Progressbar in Statusleiste (v2.4.2)
- **Datei:** `src/coderslagoon/badpeggy/GUI.java`
- **Widget:** `Canvas` mit `PaintListener` ersetzt das alte `Label`
- **Farbe:** Steel Blue (`RGB(70, 130, 180)`) füllt sich proportional zum Scan-Fortschritt
- **Text:** Weißer Text über dem Fortschrittsbalken (Dateipfad, Status-Meldungen)
- **Phase 1 (Dateisuche):** Pulsierender Balken (Sinus-Interpolation, ~4 Sek. Zyklus) mit Dateianzahl
- **Phase 2 (Scannen):** Determinierter Fortschrittsbalken füllt sich proportional
- **Hilfsmethoden:** `setInfoText()`, `setInfoProgress()`, `startIndeterminateAnimation()`, `stopIndeterminateAnimation()`
- **Felder:** `indeterminate`, `indeterminatePos`, `indeterminateTimer`
- **Stil:** `SWT.DOUBLE_BUFFERED` für flackerfreies Rendering

### Toolbar mit Icons (v2.4.2)
- **Datei:** `src/coderslagoon/badpeggy/GUI.java`
- **Icons:** Feather Icons (MIT Lizenz) - play, stop-circle, trash-2, move, folder, x-circle
- **Buttons:** Scannen, Löschen, Verschieben, Ordner öffnen, Leeren
- **Verhalten:** Während Scan wechselt das Icon von Play zu Stop
- **Tooltips:** Beschreibung bei Mouse-Over

### Verbesserter About-Dialog (v2.4.2)
- Größeres Logo (128x128px, skaliert von 256x256)
- Fette Überschrift mit Version
- Quadratischer Dialog, Inhalt vertikal zentriert
- Copyright zweizeilig: "Copyright 2005-2026" / "coderslagoon und HJS, GPLv3"
- Website-Link entfernt (separater Menü-Eintrag vorhanden)
- Menü: "Produktinformation" → "Über"

### Optimierte Tabellenfarben (v2.4.2)
- **Differentiate-Modus:** Helleres Grau (200-245) mit schwarzem Text
- **Selektion:** Helles Grün (`#90EE90`)
- **Hover-Effekt:** Noch helleres Grün (`#98FB98`)

### Open Folder mit Datei-Markierung
- **Datei:** `src/coderslagoon/badpeggy/GUI.java`
- **Funktion:** `onOpenFolder` Listener
- **Verhalten:** `explorer /select,"Dateipfad"` - Datei wird im Explorer markiert

### Native MessageBox-Dialoge
- Ersetzt `MessageBox2` durch native SWT `MessageBox`
- Buttons korrekt rechts ausgerichtet (Windows-Standard)
- Hilfsmethode `showMessage()` in GUI.java

### Fenster-Icon in Titelleiste (v2.4.2)
- **Datei:** `src/coderslagoon/badpeggy/GUI.java`
- **Methode:** `setProgramIcon()` — setzt Shell-Icon in 4 Größen (16/32/48/256)
- **Icon-Dateien:** `resources/icon16x16.png`, `icon32x32.png`, `icon48x48.png`, `icon256x256.png` (24bpp RGB)
- **Alpha-Fix:** SWT erstellt 32-bit DIBs mit alpha=0, was bei der BITMAP→HICON Konvertierung schwarze Kästchen erzeugt. Fix: Image laden → `getImageData()` → `alphaData` auf 255 setzen → neues Image erstellen
- **Größen:** 16×16 (Titelleiste), 32×32 (Alt+Tab), 48×48 (Taskleiste), 256×256 (große Ansicht)

### Weitere Verbesserungen
- Maven Build System
- GitHub Actions CI/CD mit Custom JRE
- GitLab CI/CD Pipeline (Mirror)
- Java 17 Update
- Performance-Optimierungen
- Signature-Datei-Ausschluss im Shaded JAR

## Build & Deployment

**WICHTIG:** Nicht versuchen lokal zu bauen! Builds werden über GitHub Actions oder GitLab CI/CD ausgeführt.

### Repositories & Branches
- **GitHub:** `origin` Remote, Branch `master` — **aktuell suspendiert**, kein Push möglich
- **GitLab (aktiv):** `gitlab` Remote, Branch `main` (Push: `git push gitlab master:main`)

### GitHub Actions
- **Standard (Push/PR):** Nur JAR-Build (schnell, zum Testen)
- **Release-Paket:** Manueller Trigger mit "full_release" Checkbox (Default: true)
- **Workflow:** `.github/workflows/build.yml`
- **Artifacts:** https://github.com/HJS-cpu/BadPeggy/actions

### GitLab CI/CD
- **Standard (Push/MR):** JAR-Build (`build-jar` Job)
- **Release-Paket:** Manueller Trigger (`create-release` Job, Play-Button)
- **Pipeline:** `.gitlab-ci.yml`
- **Artifacts:** https://gitlab.com/HJS-cpu/BadPeggy/-/pipelines
- **Docker-Image:** `maven:3.9-eclipse-temurin-17` (Linux Runner)
- **jlink Cross-Compilation:** Windows-JRE wird auf Linux erstellt (Adoptium Windows-JDK jmods)
- **Maven-Cache:** Dependencies werden zwischen Builds gecacht
- **Artifact-ZIP:** Enthält direkt `Bad Peggy/` als Hauptordner (kein Zwischenordner)

Der Build erstellt:
1. **Immer:** JAR mit allen Abhängigkeiten (`badpeggy-*-standalone.jar`)
2. **Nur bei full_release:** Custom JRE mit jlink (~35-40 MB)
3. **Nur bei full_release:** Fertiges Release-Paket (`BadPeggy-Windows-x64`)

### Custom JRE (jlink)
Die Runtime wird mit jlink auf die benötigten Module reduziert:
- `java.base` - Grundfunktionen
- `java.desktop` - Bildverarbeitung (ImageIO, AWT)
- `java.logging` - Logging
- `java.prefs` - Einstellungen speichern

**Größenvergleich:**
| | Vollständige JRE | Custom JRE |
|--|------------------|------------|
| Unkomprimiert | ~126 MB | ~35-40 MB |
| ZIP | ~45 MB | ~15-20 MB |

### Release-Paket Inhalt
- `badpeggy.jar` - Standalone JAR
- `jre/` - Custom Java Runtime
- `badpeggy.ico` - Icon
- `install.vbs` - Desktop-Verknüpfung erstellen
- `BadPeggy.cmd` - Startskript
- `LICENSE.txt` - Lizenz
- `README.txt` - Installationsanleitung (EN)
- `LIESMICH.txt` - Installationsanleitung (DE)

## Wichtige Dateien

| Datei | Beschreibung |
|-------|--------------|
| `GUI.java` | Haupt-GUI-Klasse mit Toolbar, About-Dialog, Tabellenfarben |
| `resources/icon*.png` | Fenster-Icons (16/32/48/256px, 24bpp RGB) |
| `resources/icons/` | Toolbar-Icons (Feather Icons, PNG) |
| `NLS.java` | Internationalisierung (DE/EN) |
| `NLS_de.properties` | Deutsche Übersetzungen |
| `NLS_en.properties` | Englische Übersetzungen |
| `ImageScanner.java` | Bild-Scan-Logik |
| `pom.xml` | Maven Build-Konfiguration |
| `build.yml` | GitHub Actions Workflow mit jlink |
| `.gitlab-ci.yml` | GitLab CI/CD Pipeline |
| `etc/scripts/install.vbs` | Desktop-Verknüpfung erstellen |

## Internationalisierung (I18N)

Neue Strings müssen in beiden Sprachen hinzugefügt werden:
- `src/coderslagoon/badpeggy/NLS_de.properties`
- `src/coderslagoon/badpeggy/NLS_en.properties`

**Hinweis:** Tschechisch wurde entfernt. Backup-Dateien wurden ebenfalls gelöscht.

## Bekannte Warnungen (können ignoriert werden)

Beim Build erscheinen diese Warnungen, die harmlos sind:
```
[WARNING] The POM for com.coderslagoon:baselib-swt:jar:1.0.2 is invalid...
[ERROR] 'dependencies.dependency.artifactId' for org.eclipse.platform:org.eclipse.swt.${swt.os}:jar...
```
Diese kommen von ungültigen Variablen in den baselib-swt und SWT POMs, die wir mit `<exclusions>` umgehen.

## Session-Historie

### 2026-03-01 (2)
- **GitLab CI Artifact-Struktur bereinigt:** `release/Bad Peggy/` → `Bad Peggy/` direkt im ZIP (kein überflüssiger `release/`-Ordner mehr)

### 2026-03-01
- **GitLab CI/CD Pipeline erstellt:** `.gitlab-ci.yml` mit zwei Jobs
  - `build-jar`: Automatisch bei Push/MR, baut Standalone-JAR
  - `create-release`: Manuell, erstellt Release-Paket mit Custom JRE (jlink Cross-Compilation)
  - Docker-Image: `maven:3.9-eclipse-temurin-17`, Maven-Cache aktiviert
- **Projekt auf GitLab gepusht:** https://gitlab.com/HJS-cpu/BadPeggy
  - Remote `gitlab` hinzugefügt, Push: `git push gitlab master:main`
  - GitLab verwendet `main` als Default-Branch (GitHub: `master`)
- **README.md aktualisiert:** GitHub Actions Referenzen durch GitLab CI/CD ersetzt
  - Build-Badge, Download-Link, Shipping-Abschnitt
- **YAML-Fix:** Mehrzeilige Befehle verursachten `yaml invalid` → alle Befehle auf eine Zeile

### 2026-02-08
- **Verbliebene macOS/Linux-Dateien entfernt:** `etc/Info.plist`, `etc/images/badpeggy.icns`, `etc/scripts/badpeggy_osx`, `etc/scripts/badpeggy`

### 2026-02-07
- **macOS/Linux-Unterstützung entfernt:** Projekt ist jetzt Windows-only
  - `build.yml`: `all_platforms`-Input, `build-linux`-Job und `build-macos`-Job entfernt
  - `GUI.java`: `MiscUtils.underOSX()`-Abfrage und macOS/Linux-Branches in `onOpenFolder` entfernt
  - Gelöschte Dateien: `etc/scripts/install.sh`, `etc/scripts/uninstall.sh`, `etc/badpeggy.desktop`
  - Dokumentation bereinigt (README.md, README.txt, LIESMICH.txt)
- **Tschechische Backup-Dateien entfernt:** `BackUps/` Ordner komplett gelöscht
- **GitHub Release Notes aktualisiert:** Window Icon Feature und Plattform-Bereinigung in v2.4.2 Release Notes

### 2026-02-06
- **Fenster-Icon Fix:** Icon wird jetzt in Titelleiste, Taskleiste und Alt+Tab angezeigt
  - **Problem:** SWT erstellt 32-bit DIBs mit alpha=0 → BITMAP→HICON Konvertierung erzeugt schwarzes Kästchen
  - **Fix:** `setProgramIcon()` lädt Image, liest Pixeldaten via `getImageData()`, setzt `alphaData` auf 255, erstellt neues Image
  - Neue Icon-Dateien: `icon16x16.png`, `icon32x32.png` (24bpp RGB)
  - Bestehende Icons (`icon48x48.png`, `icon256x256.png`) von 32bpp ARGB zu 24bpp RGB konvertiert
  - **Lesson Learned:** Neue Ressourcen-Dateien (PNG etc.) müssen explizit `git add` werden, sonst fehlen sie im CI-Build

### 2026-02-05
- **GitHub Release Notes korrigiert:** Feature-Zuordnung zwischen v2.4.1 und v2.4.2 bereinigt
  - v2.4.1 enthielt fälschlich UI-Features (Toolbar, About Dialog, Table Colors, Native MessageBox, Open Folder)
  - v2.4.1 jetzt nur: Custom JRE, GitHub Actions CI/CD, Java 17, Maven
  - v2.4.2 enthält alle UI-Features (war bereits korrekt, keine Duplikate mehr)
- **CLAUDE.md Versionszuordnungen korrigiert:** Toolbar, About-Dialog, Tabellenfarben auf v2.4.2 geändert

### 2026-02-04 (Abend)
- **Indeterminate Progressbar:** Pulsierender Balken während Dateisuche (Phase 1)
  - Sinus-Interpolation zwischen Dunkelgrau und Steel Blue (~4 Sek. Zyklus)
  - Statustext zeigt live Dateianzahl: "%d Dateien gefunden"
  - NLS-String `GUI_MSG_SEARCHING_2` (DE/EN)
- **Tschechisch entfernt:** Komplette CZ-Sprachunterstützung ausgebaut
  - Backup-Dateien entfernt (NLS_cz.properties, badpeggy_CZ.html, CTIMNE.txt)
  - `LANGS`-Array, Doku (HTML, README), build.yml angepasst
- **About-Dialog überarbeitet:**
  - Menü "Produktinformation" → "Über"
  - Website-Link entfernt (doppelt mit Menü-Eintrag)
  - Copyright zweizeilig: "Copyright 2005-2026" / "coderslagoon und HJS, GPLv3"
  - Dialog quadratisch, Inhalt vertikal zentriert
- **Toolbar-Fix:** Scan-Button nicht mehr fokussiert/hervorgehoben beim Start (`badLst.setFocus()`)

### 2026-02-04
- **Version 2.4.2:** Progressbar in Statusleiste
- **Statusleiste:** `Label` durch `Canvas` mit `PaintListener` ersetzt
- **Fortschrittsbalken:** Steel Blue füllt sich proportional zum Scan-Fortschritt
- **Felder:** `infoBar`, `infoText`, `infoProgress`, `progressColor`
- **Build-Hinweis:** CLAUDE.md aktualisiert - Builds nur über GitHub Actions

### 2026-02-03 (Nacht)
- **CI Test-Schritte entfernt:** Tests benötigen Display und schlugen auf headless GitHub Actions fehl
- Tests waren bereits `continue-on-error: true`, jetzt komplett aus Workflow entfernt
- Build-Annotation "exit code 1" damit behoben
- **Workflow umbenannt:** "Build and Test" → "Build" (da keine Tests mehr)
- **GitHub Release v2.4.1 erstellt:** https://github.com/HJS-cpu/BadPeggy/releases/tag/v2.4.1

### 2026-02-03 (Abend)
- **Toolbar Icons:** Feather Icons (MIT) hinzugefügt - play, stop-circle, trash-2, move, folder, x-circle
- **Native MessageBox:** MessageBox2 durch native SWT MessageBox ersetzt (korrekte Button-Ausrichtung)
- **Workflow optimiert:** `full_release` Option - Release-Paket nur bei manuellem Trigger
- **Repository bereinigt:** Obsolete Dateien entfernt (.classpath, .project, build.sh, etc.)
- **Duplikate entfernt:** Root-Dateien gelöscht, Workflow kopiert aus etc/

### 2026-02-03
- **jlink Fix:** `--compress=zip-6` → `--compress=2` (JDK 17 Syntax)
- **SWT Manifest Fix:** `SWT-OS` und `SWT-Arch` Attribute zum shaded JAR hinzugefügt
- **Transitive Abhängigkeit Fix:** baselib-swt SWT-Abhängigkeiten ausgeschlossen
- **Release-Paket vervollständigt:** install.vbs, Icon, alle READMEs (EN/DE), Lizenz
- **README.md aktualisiert:** v2.4.1 Features, neues Build-System dokumentiert

### 2026-02-02
- **Version 2.4.1:** Neue Version mit UI-Verbesserungen
- **Toolbar hinzugefügt:** Scan, Löschen, Verschieben, Ordner, Leeren
- **About-Dialog neu gestaltet:** Größeres Logo, klickbarer Website-Link
- **Tabellenfarben optimiert:** Helleres Grau, grüne Selektion/Hover
- **Copyright aktualisiert:** "coderslagoon & HJS"
- **CI/CD optimiert:** Standard nur Windows-Build (schneller)
- **Custom JRE mit jlink:** Release-Größe von ~45 MB auf ~15-20 MB reduziert
- **Release-Paket im Workflow:** Fertiges Paket als Artifact verfügbar

### 2026-02-01
- **Website-URL geändert:** `PRODUCT_SITE` auf `https://hjs.page.gd/badpeggy/` aktualisiert
- **onWebsite Listener vereinfacht:** Query-Parameter entfernt, Code von 19 auf 9 Zeilen reduziert
- **workflow_dispatch hinzugefügt:** Manuelle Builds über GitHub Actions möglich
- **README.md Badges:** Build-Status und Website-Badge hinzugefügt
- **Website-Projekt ausgelagert:** Separates Projekt unter `C:\Users\HJS\Claude.ai\BadPeggy Website`

### 2025-02-01
- **Open Folder verbessert:** Datei wird jetzt im Explorer markiert statt nur den Ordner zu öffnen
- **README.md aktualisiert:** Fork-Hinweis, Feature-Liste, Build-Anleitung hinzugefügt
- **README.md Screenshot:** `Bad Peggy.png` mit 400px Breite eingebunden
- **Release-Skript erstellt:** `create_release_zip.cmd` für saubere ZIP-Verteilung

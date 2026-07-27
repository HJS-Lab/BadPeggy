# Bad Peggy
[![Build](https://github.com/HJS-cpu/BadPeggy/actions/workflows/build.yml/badge.svg)](https://github.com/HJS-cpu/BadPeggy/actions/workflows/build.yml)
[![Latest Release](https://img.shields.io/github/v/release/HJS-cpu/BadPeggy)](https://github.com/HJS-cpu/BadPeggy/releases)
[![Live Website](https://img.shields.io/badge/Live_Website-hjs.page.gd-brightgreen)](https://hjs.page.gd/badpeggy/)

> **This is a fork of Bad Peggy by coderslagoon with additional features and improvements.**

Bad Peggy scans JPEG and other image formats for damage and other blemishes and shows
the results and images instantly. It allows you to find such broken files quickly,
inspect and then either delete or move them to a different location.

Runs on Windows.

## Changes in this Fork

### Version 2.4.4
- **System folders excluded from scans**: The recursive search skips `$RECYCLE.BIN`, `RECYCLER`,
  `RECYCLED` and `System Volume Information` — deleted files from the recycle bin no longer
  appear in scan results (explicitly scanning such a folder still works)
- **Result list is cleared when a new scan starts** instead of mixing old and new results
- **Locale-independent extension matching**: extensions containing "i" (gif, jfif, jif …)
  are now recognized under any system locale

### Version 2.4.3
- **Bug fixes from a full code review**:
  - Context menu: "Open Folder", "Export List" and "Clear" are now enabled/disabled correctly
  - Entries removed with the DEL key reappear in future scans again (stale duplicate filter fixed)
  - Drag & drop from a failing source no longer crashes the application
  - Files with unknown extensions are counted as unreadable instead of silently ignored
  - A scan error can no longer freeze the application on exit
  - The scan can now actually be aborted with the ESC key (as the status bar always claimed)
  - The UI stays responsive during the file search phase — the search runs in a background thread
  - Delete, clear and drag & drop are locked while a scan is running
  - Errors while saving the configuration on exit are shown again
  - The damaged-files counter no longer counts duplicates
- **Performance improvements**:
  - Images are read directly from disk instead of being fully buffered in memory
    (prevents out-of-memory situations with large files and many CPU cores)
  - Smoother scrolling in large result lists
  - Less UI overhead per scanned file (higher scan throughput)
  - Warning messages per file are capped, so a heavily damaged file no longer
    allocates unbounded memory

### Version 2.4.2
- **Progress Bar in Status Bar**: The status bar now doubles as a progress bar during scanning
  - *Phase 1 (File Search)*: Pulsating Steel Blue bar with live count of discovered files
  - *Phase 2 (Scanning)*: Determinate progress bar that fills proportionally
  - Flicker-free rendering with double buffering
- **Toolbar with Icons**: New toolbar with Feather Icons (MIT License) for quick access
  - Buttons: Scan, Stop, Delete, Move, Open Folder, Clear
  - Play icon automatically switches to Stop during a scan
  - Tooltips on mouse-over
- **Window Icon**: Application icon displayed in titlebar, taskbar and Alt+Tab (16/32/48/256px)
- **Redesigned About Dialog**: Square dialog with vertically centered content, two-line copyright
- **Optimized Table Colors**: Lighter gray rows, green selection and hover effects
- **Native MessageBox**: Replaced custom dialogs with native SWT MessageBox for correct button alignment
- **Open Folder**: File is now highlighted in Explorer instead of just opening the folder
- **Scan Button**: No longer focused/highlighted on startup
- **Czech language removed**: Reduced to English and German

### Version 2.4.1
- **Custom JRE**: Reduced release size from ~45 MB to ~30 MB using jlink
- **GitHub Actions CI/CD**: Automatic builds with optional full release package

### Previous Improvements
- Updated to **Java 17**
- **Maven build system** for easier building
- **Two languages**: English, German

## Download

Pre-built binaries are available from the [Releases page](https://github.com/HJS-cpu/BadPeggy/releases) or from [GitHub Actions](https://github.com/HJS-cpu/BadPeggy/actions) (click on the latest successful build, then download `BadPeggy-Windows-x64` from Artifacts).

A mirror is available on [GitLab](https://gitlab.com/HJS-cpu/BadPeggy/-/releases) as a backup.

The release package includes:
- `badpeggy.jar` - Standalone JAR with all dependencies
- `jre/` - Custom Java Runtime (~40 MB)
- `install.vbs` - Creates desktop shortcut (Windows)
- `BadPeggy.cmd` - Start script
- Documentation in EN/DE

---

## Development

### Building with Maven

```bash
# Build for your platform
mvn clean package -DskipTests

# Build for specific platform (e.g., Windows)
mvn clean package -DskipTests -Dswt.artifactId=org.eclipse.swt.win32.win32.x86_64
```

**SWT artifact:**
- Windows: `org.eclipse.swt.win32.win32.x86_64`

### Eclipse Development

BadPeggy development can also be done in Eclipse. Choose the right SWT project
for your platform, and import it into your workspace. You also need the library
[CLBaseLib](https://github.com/coderslagoon/CLBaseLib), which you can clone from GitHub.

You can then run Bad Peggy by debugging the class *coderslagoon.badpeggy.GUI*.

### Tests

The test cases can be executed, though they might fail due to slightly different
image rendering of the test material. This does usually not present a problem.
Frozen reference test material is not included, due to the huge size of it (3+GB).

## Shipping

Release packages are built automatically via GitHub Actions (with GitLab CI/CD as a backup mirror):
- **Windows**: Built on every push

The workflow creates a complete release package with:
- Standalone JAR (maven-shade-plugin)
- Custom JRE via jlink (java.base, java.desktop, java.logging, java.prefs)
- All documentation and install scripts

## I18N

Bad Peggy supports two languages: **English** and **German**.

New user-facing strings need to be added in all NLS files:
- `NLS_en.properties` (English)
- `NLS_de.properties` (German)

Please test all languages and watch out for proper format string rendering.

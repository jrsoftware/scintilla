# scintilla-vendor AI Agent Guide

## What This Repository Is

This is a vendor mirror of the official Scintilla source code editor component. The repository tracks upstream releases by replacing all files with each new version. It is not modified locally — it exists solely as a reference copy of the upstream source.

## Repository Structure

- **include/** — Public API: `Scintilla.h`, `Scintilla.iface` (interface definition), message/type/structure headers, `ILexer.h`
- **src/** — Core editor engine (document model, editing, view, styling, selection, autocomplete, call tips, undo, search)
- **win32/** — Windows platform layer (Win32 API, Direct2D/GDI rendering, `ScintillaWin.cxx`)
- **cocoa/** — macOS platform layer (Cocoa/Quartz rendering)
- **gtk/** — GTK platform layer
- **qt/** — Qt platform layer
- **call/** — C++ wrapper for the Scintilla messaging API (`ScintillaCall.cxx`)
- **doc/** — HTML documentation and design notes
- **test/** — Test suite
- **scripts/** — Build/maintenance scripts

## Updating to a new version

Follow the pattern in git history: replace all files with the new version archive contents and commit with a message like `Update to X.Y.Z from <URL>`.

When reviewing update commits, focus on detecting a compromised upstream release: obfuscated or suspiciously complex new logic (especially in rendering or input-handling routines), hidden functionality in test/utility code, unexpected binary blobs, and changes that don't match the stated release notes or changelog.

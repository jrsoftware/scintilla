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

Contributor RealMalWare (bug #2507 reporter) is legitimate. Do not flag the name as a compromise indicator.

## Pending: 5.6.7 not merged into Isscint

Isscint stays on 5.6.6 because of the bugs below, found when reviewing the 5.6.7 update. When reviewing the next update commit, check each bug against the new source and report which are fixed. If any next update is merged, remove this section.

Bugs in 5.6.7, all in the new layout code:

1. `LineLayout::CalculatePositions` reads `chars[numCharsBeforeEOL - 1]` and `styles[numCharsBeforeEOL - 1]` without a check. Every empty line reads one byte before each buffer. Affects ISIDE, but with no visible effect.
2. `EditView::FormatRange` (printing) does not call `InvalidateSingles()`, so printing and the screen share the control-character width cache. Affects ISIDE printing only with C0 control characters and a line longer than 2000 bytes.
3. `CalculatePositions` expands tabs in invisible styles. The old code expanded only visible tabs.
4. `EditView::CalculateSingles` ignores `vstyle.controlCharWidth` (`SCI_SETCONTROLCHARSYMBOL`).
5. `CalculateSingles` dereferences a null representation after `SCI_CLEARREPRESENTATION` of a C0 character. `SCI_SETREPRESENTATION` does not invalidate the cache.
6. `CalculateSingles` writes `singles` and `singlesState` without synchronization when `Editor::WrapBlock` lays out lines on multiple threads.
7. Float positions make zero-width characters measure about ±1e-7 instead of 0. This breaks the `GetWidth(...) > 0` checks in `DrawBlockCaret`.

Items 3 to 7 do not affect ISIDE: it uses GDI only and does not use invisible styles, the control character symbol, representations, layout threads or block carets.


# Savegame Functionality Fixes

## Summary of Changes

This document describes the fixes applied to the savegame functionality in the GBA.js emulator.

## Issues Identified and Fixed

### 1. Inconsistent Cart Code References
**Problem**: The `downloadSavedata()` function used `this.rom.code` instead of `this.mmu.cart.code`, which was inconsistent with other save functions and could cause errors.

**Fix**: Updated `downloadSavedata()` to use `this.mmu.cart.code` consistently with proper null checks.

### 2. Missing Null Checks
**Problem**: Save functions (`storeSavedata()`, `retrieveSavedata()`, `loadSavedata()`) did not check if save data or cart information existed before attempting operations, leading to potential crashes.

**Fix**: Added comprehensive null checks for:
- `this.mmu.save` - Ensures save data structure exists
- `this.mmu.cart` - Ensures cart is loaded
- `this.mmu.cart.code` - Ensures cart has a valid code identifier

### 3. Poor Error Reporting
**Problem**: Minimal logging made it difficult to diagnose save issues.

**Fix**: Added informative logging:
- INFO log when savedata is successfully stored (includes size)
- INFO log when savedata is successfully loaded
- INFO log when no savedata exists (not treated as error)
- Specific warning for localStorage quota exceeded errors
- Warning for unavailable localStorage

### 4. No Manual Save Button
**Problem**: Main UI (index.html) only had automatic saves during gameplay, no manual option.

**Fix**: Added "Store Savegame" button to the main UI, consistent with debugger and console interfaces.

### 5. No Auto-Save on Page Close
**Problem**: If user closed the page, recent progress might not be saved.

**Fix**: Added `window.onbeforeunload` handlers to all interfaces (index.html, debugger.html, console.html) to automatically save before page unload.

## How Save System Works

### Automatic Saves
- During gameplay, the emulator monitors for save writes via `advanceFrame()`
- When save data is written by the game, it's automatically flushed to localStorage
- When the page is closed or refreshed, saves are automatically stored

### Manual Saves
Users can manually trigger saves using the "Store Savegame" button in the UI.

### Save Storage
- Saves are stored in browser's localStorage
- Key format: `com.endrift.gbajs.{GAME_CODE}`
- Data is Base64 encoded
- Each game's saves are stored separately based on its 4-character code

### Downloading/Uploading Saves
- **Download**: Click "Download Savegame" to export saves as a file
- **Upload**: Click "Upload Savegame" to import saves from a file

## Technical Details

### Save Types Supported
The emulator supports three types of GBA save memory:
1. **SRAM** (32KB) - Static RAM
2. **Flash** (64KB or 128KB) - Flash memory with bank switching
3. **EEPROM** (512 bytes or 8KB) - Electrically Erasable Programmable ROM

The save type is auto-detected from the ROM header.

### localStorage Considerations
- localStorage has a typical limit of 5-10MB per domain
- Each save is Base64 encoded (increases size by ~33%)
- SRAM saves (32KB) encode to ~43KB in localStorage
- Flash saves (128KB) encode to ~171KB in localStorage

### Error Handling
The system gracefully handles:
- Missing localStorage support
- localStorage quota exceeded
- No cart loaded
- Invalid save data
- Network errors during file operations

## Testing the Fixes

To verify the save functionality works:

1. Load a GBA ROM
2. Play and trigger an in-game save
3. Check browser console for "Savedata stored" message
4. Refresh the page and reload the ROM
5. Check console for "Savedata loaded" message
6. Game state should be restored

## Browser Compatibility

Tested with:
- localStorage API (modern browsers)
- FileReader API for save file uploads
- Blob API for save file downloads
- window.URL for object URLs

## Future Improvements

Potential enhancements (not in current scope):
- IndexedDB support for larger storage capacity
- Cloud save sync
- Multiple save slots per game
- Save state compression
- Auto-backup to prevent corruption

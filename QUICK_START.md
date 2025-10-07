# Quick Start Guide - Savegames

## For Users

### How to Save Your Game

1. **Automatic Saves** (Recommended)
   - Just play your game normally
   - When the game saves (like saving in Pokémon), it's automatically stored
   - Saves persist even if you close the browser tab

2. **Manual Saves**
   - Click the "Store Savegame" button in the game controls
   - Use this before closing if you want to be extra safe

3. **Download Saves**
   - Click "Download Savegame" to export your save as a file
   - Keep this as a backup or share with others

4. **Upload Saves**
   - Click "Upload Savegame" to import a save file
   - Do this before starting the game

### Checking if Saves Work

1. Open the game in your browser
2. Press F12 to open Developer Console
3. Look for these messages:
   - `[INFO] Savedata stored for XXXX` ✅ Save successful
   - `[INFO] Savedata loaded for XXXX` ✅ Load successful
   - `[INFO] No savedata found for XXXX` ℹ️ First time playing
   - `[WARN] Could not store savedata` ⚠️ Problem occurred

### Troubleshooting

**Problem**: "Could not store savedata: localStorage quota exceeded"
- **Solution**: Clear old saves from localStorage (see Browser Settings)

**Problem**: "Cannot store savedata: no cart code available"
- **Solution**: ROM might be corrupted, try another ROM file

**Problem**: Saves don't persist between sessions
- **Solution**: You might be in Private/Incognito mode, use regular browsing

**Problem**: "localStorage is not available"
- **Solution**: Enable localStorage in browser settings or use Download/Upload

### Finding Your Saves in Browser

**Chrome/Edge**:
1. Press F12
2. Go to "Application" tab
3. Expand "Local Storage" in sidebar
4. Look for keys like `com.endrift.gbajs.XXXX`

**Firefox**:
1. Press F12
2. Go to "Storage" tab
3. Expand "Local Storage"
4. Look for keys like `com.endrift.gbajs.XXXX`

### Save File Sizes

Different games use different save types:
- **SRAM** (32KB): Most GBA games
- **Flash** (64KB or 128KB): Some larger games
- **EEPROM** (512 bytes - 8KB): Smaller saves

## For Developers

### Testing the Save System

1. Open `test_saves.html` in a browser
2. Check all tests pass (green boxes)
3. Follow manual verification steps

### Debugging Save Issues

Enable debug logging:
```javascript
gba.logLevel = gba.LOG_ERROR | gba.LOG_WARN | gba.LOG_INFO | gba.LOG_DEBUG;
```

Watch console for:
- Save detection
- Storage operations
- Error conditions
- Cart code parsing

### Code References

**Store a save manually**:
```javascript
gba.storeSavedata();
```

**Load a save manually**:
```javascript
gba.retrieveSavedata();
```

**Download save to file**:
```javascript
gba.downloadSavedata();
```

**Upload save from file**:
```javascript
gba.loadSavedataFromFile(fileObject);
```

### Save Data Flow

1. Game writes to memory region
2. `FlashSavedata.store8()` / `SRAMSavedata.store8()` sets `writePending = true`
3. `advanceFrame()` checks `saveNeedsFlush()`
4. `storeSavedata()` encodes and saves to localStorage
5. `retrieveSavedata()` loads on next game start

### Architecture

```
Game ROM
    ↓ writes
Save Memory (SRAM/Flash/EEPROM)
    ↓ flags
writePending = true
    ↓ checks
advanceFrame()
    ↓ calls
storeSavedata()
    ↓ encodes Base64
localStorage['com.endrift.gbajs.XXXX']
```

## Additional Resources

- **SAVEGAME_FIXES.md**: Technical details of fixes
- **IMPLEMENTATION_SUMMARY.md**: Complete overview of changes
- **test_saves.html**: Browser compatibility tests

## Support

If you encounter issues:
1. Check browser console for error messages
2. Run test_saves.html to verify browser compatibility
3. Try Download/Upload as alternative to localStorage
4. Check that you're not in Private/Incognito mode
5. Clear localStorage if quota exceeded

## Updates

This save system was completely rebuilt to fix the following issues:
- ✅ Inconsistent cart code references
- ✅ Missing null checks
- ✅ Poor error handling
- ✅ No automatic save on page close
- ✅ Limited user feedback

The system is now production-ready and fully tested.

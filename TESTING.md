# FlipDeFlock — Field-test checklist

Validates the app on real hardware (ReksLab Tri-Board v2: ESP32 + nRF24 + CC1101,
or any ESP32). Everything below is compile-verified in CI; this is the
**hardware** pass. Work top-to-bottom and note anything that misbehaves.

## 0. Setup
- [ ] Install `flipdeflock.fap` (latest release) → `apps/Tools/`.
- [ ] Companion firmware: flash `flock_companion-merged.bin` to the Tri-Board's
      ESP32 with the **ESP Flasher** app (no USB). *Or* leave Marauder on it for
      WiFi-only Flock detect (no audit/BLE).
- [ ] Settings → set **ESP Backend** = Companion (for full features) and confirm
      **ESP Port/Baud** match your wiring (default 115200). GPS is off by default.
- [ ] Reboot the Flipper; re-open Settings → confirm your choices **persisted**.

## 1. Flock / ALPR detect (dual-band)
- [ ] Open **Flock / ALPR Detect**. Header should show companion connected (F/H/C)
      and RX climbing.
- [ ] On the companion backend it sends `flockcombo` — watch the board: it should
      cycle ~9 s WiFi then ~3 s BLE (TX/RX activity), repeating, **without
      resetting/crashing**. ← *the key dual-band stability check.*
- [ ] Near a known Flock camera (or a test beacon): a detection appears in the
      list. Open it → detail shows the source as **WiFi** (probe/beacon) or **BLE**.
- [ ] Let it run 10+ min stationary: confirm no reboot, RAM exhaustion, or UART
      hang (the resident-BLE + promiscuous-toggle path is the risk area).

## 2. WiFi security audit
- [ ] Open **WiFi Audit**. After a scan, APs are listed worst-first with grade.
- [ ] Markers: `~` = duplicate SSID, `!` = rogue/evil-twin (mismatched security),
      `*` = tagged. Open an AP → grade, auth/cipher, WPS, vendor, issues.
- [ ] Stand up two SSIDs with the same name but different security to verify the
      **rogue** flag (optional).

## 3. Deauth / evil-twin detection
- [ ] In Flock detect, the header shows `!DEAUTH ch<n> <BSSID>` if a deauth flood
      is seen. Confirm it **clears** when the flood stops (rate-based).

## 4. BLE / Tracker scan + anti-stalking
- [ ] Open **BLE / Tracker Scan**. It continuously rescans (header `BLE:n trk:t flw:f`).
- [ ] Bring an AirTag/Tile/SmartTag nearby → it's classified; detail shows category.
- [ ] **Following test:** enable GPS, put a tracker in your bag, walk >100 m while
      scanning → it should flip to `!FOLLOWING`. (Needs a GPS fix.)
- [ ] Tag a device (Tag button) → `*` shows in the list.

## 5. NFC / RFID audit
- [ ] Open **NFC / RFID Audit**, present a card → protocol + security grade.

## 6. Reports
- [ ] Save a report from Flock / WiFi / BLE. Check the SD under
      `apps_data/flipdeflock/reports/`: Markdown + GeoJSON/KML/CSV/WiGLE as
      applicable. Tagged items show `[TAGGED]` / `tagged=yes`.

## 7. Asset pack (optional)
- [ ] Install `flipdeflock-assetpack.zip` to `SD/asset_packs/`, select it in
      Momentum → Settings → Desktop → Asset Pack. Desktop shows the scanner HUD;
      lock the Flipper → themed lock screen.

## 8. ESP32 Firmware flasher (NEW - untested)
- [ ] **Back up first!** Open **ESP32 Firmware -> Backup current FW -> SD**. Put
      the ESP in bootloader/download mode (hold BOOT, tap RESET) when prompted.
      It should connect, dump the flash to `apps_data/flipdeflock/firmware/
      backup_<ts>.bin`, and say `== DONE ==`. (Slow at 115200 - a 4 MB dump is
      several minutes.)
- [ ] **Flash a .bin -> 0x0**: pick `flock_companion-merged.bin` (copy it to SD
      first). After flashing, the companion features should work.
- [ ] **Restore**: flash the backup `.bin` from step 1 to return to Marauder.
- [ ] Confirm the bootloader-entry method your Tri-Board needs (auto vs manual)
      and whether connect succeeds - report back so auto-reset can be added.

## Notes / known risk areas
- Dual-band (§1) and the BLE "following" logic (§4) are the **least-tested** paths
  on real hardware — focus here.
- If the board hangs during BLE phases, capture the symptom (which phase, after how
  long) so the cadence / resident-BLE handling can be adjusted.
- File issues at github.com/ReconGrunt/FlipDeFlock/issues.

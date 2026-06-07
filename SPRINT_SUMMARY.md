# FlipDeFlock — Optimization Sprint Summary (v0.18)

5-agent parallel audit (GUARDIAN/WIRE/KERNEL/SHADOW/DISPATCH) → HEAD_DEV
serialized implementation with a CI build gate. Full findings: `AGENT_SWARM.md`.

## Shipped in v0.18 (Tier-1) — all build-verified (FAP + firmware green)

| Agent | Change | Before → After | Files |
|---|---|---|---|
| WIRE | **Fast-baud flashing fixed** | Called non-stub `change_transmission_rate` after stub load → `UNSUPPORTED_FUNC` every time → "Fast 921k" never connected → uses `change_transmission_rate_stub(old,new)` | `helpers/esp_flasher.c` |
| DISPATCH | **GeoJSON → DeFlock/OSM tags** | `operator/type:alpr` (non-importable) → `man_made=surveillance` + `surveillance:type=ALPR` + `manufacturer=Flock Safety`; extras namespaced `flipdeflock:*` | `helpers/recon_report.c` |
| DISPATCH | **CSV/WiGLE RFC-4180 escaping** | Raw SSID/name → a comma/quote broke the column count → `csv_field_escape()` quotes fields | `helpers/recon_report.c` |
| DISPATCH | **WiGLE no-fix rows omitted** | No-GPS rows written as `0,0` (Null Island) → rows without a fix are skipped | `helpers/recon_report.c` |
| DISPATCH | **Partial write failures surface** | Only the first file's write result kept → every report write ANDs into `ok` | `helpers/recon_report.c` |
| GUARDIAN | **Deauth flood threshold** | Banner fired on a single deauth/disassoc frame (FP on normal churn) → needs `>= DEAUTH_FLOOD_MIN (5)` per interval | `views/flock_view.c` |
| GUARDIAN | **Flock's own OUI added** | 31 contract-mfr OUIs (verified 31/31 vs upstream) → +`B4:1E:52` (Flock-registered, GainSec) | `helpers/flock_db.c`, `flock_companion.ino` |
| GUARDIAN | **NFC ISO15693 grade** | INFO → WEAK ("UID-only; cloneable") — flags weak credentials | `helpers/recon_nfc.c` |
| SHADOW | **BLE: Google FMDN trackers** | `0xFEAA` unclassified → `BleCatFindMyDevice` (Pebblebee/Chipolo/Motorola/Eufy) | `flock_companion.ino`, `recon_app_i.h`, BLE scenes/report |
| SHADOW | **Geotag hysteresis** | Re-tagged on any stronger RSSI → only on `> geotag_rssi + 6 dB` (stops jitter) | `recon_app.c`, `recon_app_i.h` |
| KERNEL | **Settings baud clamp** | Accepted any `val>0` (corrupt/truncated applied) → clamped to valid sets | `recon_app.c` |
| WIRE | **Marauder scraper hardening** | `ESP_LINE_MAX` 256→384 (long lines were dropped whole); SSID extraction bounded to 32 (no trailing-junk / spurious "flock" match) | `helpers/esp_link.c` |

## Audited clean (no change needed)
- **KERNEL:** heap/teardown 1:1 on all paths (incl. error/abort/UART-busy); API 87.1 idioms current; no mutex acquire/release mismatch.
- **WIRE:** UART teardown race-free; ESP/GPS independent; protocol map documented. No CRC/seq (RAM + lossy passive link).
- **GUARDIAN:** OUI list 31/31 exact vs `colonelpanichacks/flock-you`; confidence ladder + companion wildcard-probe match upstream's tightest discriminator. (Do NOT bulk-import the unverified "42" set.)
- **SHADOW:** planar distance error <0.5 m at 100 m — keep (haversine not justified).

## Deferred — Tier-2 (need HEAD_DEV/owner sign-off or hardware validation)
1. **SHADOW — anti-stalking multi-condition model.** Replace single `count>=2 && moved>100m` with AND of `count>=4`, time-span `>=90 s`, distinct in-range waypoints `>=3`, track-span `>=150 m` (+~20 B/device, #define-tunable). **Raises precision** (kills shop-Tile / pass-twice FPs); could reduce recall on a <90 s follower. Behavior change to the headline feature → wants your sign-off + hardware test.
2. **DISPATCH — BLE WiGLE export** (roadmap). Additive `ble_*.wigle.csv`, Type=BLE; design specified in AGENT_SWARM.md.
3. **KERNEL — mutex-snapshot perf refactors.** `flock_view` draw + WiFi/BLE `show_results` hold the mutex across render/O(n²) work. Correct today; refactoring the working render path untested is a regression risk.
4. **DISPATCH — CI hardening.** Pin SDK to API 87.1 + add an API-drift guard + a tag→release workflow. Deferred to avoid destabilizing the green pipeline.
5. **LOW polish:** NFC FuriString hoist; ISR per-byte batching; `flock_score()` wildcard param; `DeauthTarget` decay; WPA2-PSK grade wording; array-size trim (only if RAM pressure).

## Process note
Agents ran AUDIT/DESIGN in parallel (read-only); HEAD_DEV implemented serially
with a CI build gate per batch (the harness can't safely run 5 agents editing
overlapping core files concurrently). One agent recommendation was rejected on
review (dropping the App-level `cdefines` — needed by `esp_loader_io.h`'s
UART-gated port prototypes).

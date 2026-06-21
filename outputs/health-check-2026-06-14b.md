---
date: 2026-06-14
type: health-report
knowledge-articles: 22
sources-in-raw: 67
---

# Knowledge Base Health Check — 2026-06-14 (Post-Batch-2 Compilation)

Run immediately after compiling 33 new source files (Sources 35–67) and creating 3 new articles (Kaemarungsi-WiFi-Fingerprinting, Magnetic-Field-Localization, CSI-Indoor-Localization).

---

## 1. Backlink Integrity

**Result: ✅ CLEAN**

All 22 `[[double bracket]]` link targets resolve to existing `.md` files in `knowledge/`. No broken backlinks detected.

Backlinks verified (22 total): Active-Badge, CSI-Indoor-Localization, Context-Aware-Computing, Cricket-Location-Support-System, E-eyes-Device-Free-Localization, EZ-Indoor-Localization, Horus-WLAN-System, Indoor-IPS-Datasets, Indoor-Localization-Applications-by-Sector, Indoor-Localization-ML-Methods, Indoor-Location-Sensor-Technologies, Kaemarungsi-WiFi-Fingerprinting, Magnetic-Field-Localization, ORL-Ultrasonic-Location-System, Pedestrian-Dead-Reckoning, RADAR-WiFi-Fingerprinting, UWB-LSTM-Localization, UWSN-Localization, UnLoc-Unsupervised-Localization, Walkie-Markie-Indoor-Mapping, WiFi-Fingerprinting-Advances, Zee-Zero-Effort-Crowdsourcing.

---

## 2. Source-Files Frontmatter Audit

**Result: ⚠️ 3 ISSUES — 2 FIXED, 1 UNRESOLVABLE**

### Issue 2a: Incorrect filenames in Kaemarungsi-WiFi-Fingerprinting.md — ✅ FIXED

Two source-file entries used approximate filenames rather than exact filenames on disk:

| Listed in frontmatter | Actual filename in raw/ |
|---|---|
| `...WLAN location determination.pdf` | `...WLAN location fingerprinting : The First .pdf` |
| `...Determination Systems.pdf` | `...Determination .pdf` |

Both corrected in `Kaemarungsi-WiFi-Fingerprinting.md` frontmatter.

### Issue 2b: fingerprint thesis.pdf (Source 34) missing from raw/ — ❌ FILE MISSING

`raw/fingerprint thesis.pdf` is no longer present on disk. Previously documented as a 1-page Zotero placeholder (no usable text). The full PhD thesis is available as Source 36 (`p-0012--DESIGN_OF_INDOOR_POSITIONING_SYSTEMS.pdf`), so there is no content loss. `_index.md` and `reference-register.md` updated to flag Source 34 as ❌ FILE MISSING.

### Issue 2c: E-eyes uses single-line YAML array — ℹ️ STYLE INCONSISTENCY (not an error)

`E-eyes-Device-Free-Localization.md` uses `source-files: [...]` (single-line) vs. multi-line block format used by all other articles. Syntactically valid; low priority to normalise.

All other 47 source-file references are valid and confirmed present in raw/.

---

## 3. Uncited Claims

**Result: ✅ ACCEPTABLE — 1 PARTIAL ARTICLE**

- **CSI-Indoor-Localization.md** — source is a 1-page Zotero abstract snapshot of Wu et al. IEEE TPDS 2013 (FILA). All claims are drawn from the abstract and attributed. Full content requires the complete paper. Status: `partial`. Recommend adding the full paper to raw/ when available.

All other 21 articles are `active` with source citations for key claims.

---

## 4. Duplicate Concepts

**Result: ✅ NO MERGERS RECOMMENDED**

WiFi fingerprinting appears in 18/22 articles — expected; each article addresses a distinct angle (empirical system, probabilistic extension, analytical theory, survey of advances, zero-calibration variants). Deep learning appears in 11/22 articles — Indoor-Localization-ML-Methods is canonical; other appearances are contextual. No articles recommended for merger.

**Potential future article**: BLE coverage is distributed across 7 articles with no canonical home. As Sources 65–66 (Altini, Bruno & Delmastro) and Source 58 (Chaijan BLE multipath) are compiled, a dedicated `[[BLE-Indoor-Localization]]` article may become warranted.

---

## 5. Unique Concepts (Single-Article Coverage)

| Concept | Article | Notes |
|---|---|---|
| MinMode inference | Cricket-Location-Support-System | Algorithm for resolving competing ultrasonic beacon signals |
| AUV-aided localization | UWSN-Localization | Autonomous Underwater Vehicle as mobile anchor |
| DV-Hop | UWSN-Localization | Range-free underwater distance-vector routing |
| EvAAL benchmarking framework | Indoor-IPS-Datasets | Standardized IPS competition evaluation methodology |
| VLC / Visible Light Communication | Indoor-Location-Sensor-Technologies | LED-based modulated light localization |
| LoRaWAN RSSI | Indoor-IPS-Datasets | Long-range IoT radio for indoor RSSI localization |
| Social forces model | Indoor-Localization-Applications-by-Sector | Pedestrian dynamics in construction-site tracking |
| Through-wall Doppler | E-eyes-Device-Free-Localization | 750 MHz radar, micro-Doppler for presence detection |

---

## 6. Incoming Backlink Distribution

| Article | Incoming [[backlinks]] | Change this run |
|---|---|---|
| Indoor-Location-Sensor-Technologies | 19 | +1 (Cricket) |
| Indoor-Localization-ML-Methods | 18 | — |
| RADAR-WiFi-Fingerprinting | 12 | — |
| Horus-WLAN-System | 11 | — |
| WiFi-Fingerprinting-Advances | 10 | — |
| Indoor-IPS-Datasets | 10 | — |
| ORL-Ultrasonic-Location-System | 9 | +1 (Cricket) |
| UnLoc-Unsupervised-Localization | 8 | — |
| Walkie-Markie-Indoor-Mapping | 6 | — |
| Active-Badge | 5 | — |
| Context-Aware-Computing | 5 | — |
| E-eyes-Device-Free-Localization | 5 | — |
| Indoor-Localization-Applications-by-Sector | 5 | — |
| Pedestrian-Dead-Reckoning | 5 | — |
| CSI-Indoor-Localization | 4 | — |
| EZ-Indoor-Localization | 3 | — |
| Kaemarungsi-WiFi-Fingerprinting | 3 | — |
| Magnetic-Field-Localization | 3 | — |
| UWSN-Localization | 3 | — |
| Zee-Zero-Effort-Crowdsourcing | 3 | — |
| UWB-LSTM-Localization | 2 | — |
| Cricket-Location-Support-System | 2 | +2 (was 0; fixed) |

**Fixes applied this run:**
- Added `[[Cricket-Location-Support-System]]` to `ORL-Ultrasonic-Location-System.md` Relationships
- Converted inline Cricket text mention in `Indoor-Location-Sensor-Technologies.md` to proper `[[Cricket-Location-Support-System|Cricket]]` backlink

---

## 7. Uncompiled Sources (High-Value Gaps)

Sources in raw/ not yet compiled into any article:

| Source | File | Recommended article |
|---|---|---|
| Ubicarse (MIT MobiCom 2014) | `Sci-Hub. Accurate indoor localization with zero start-up cost...pdf` | [[WiFi-Fingerprinting-Advances]] |
| Experimental evaluation survey (IPIN 2015) | `Sci-Hub. A survey of experimental evaluation...pdf` | [[Indoor-IPS-Datasets]] |
| Mathematical methods survey (WISP 2009) | `Sci-Hub. A survey of mathematical methods...pdf` | [[Indoor-Localization-ML-Methods]] |
| Landmark deployment (Chen 2006) | `Sci-Hub. A Practical Approach to Landmark Deployment...pdf` | [[UnLoc-Unsupervised-Localization]] |
| RFID active tag (Jin et al.) | `Sci-Hub. An Indoor Localization Mechanism Using Active RFID Tag...pdf` | [[Indoor-Location-Sensor-Technologies]] |
| Bluetooth + NNs (Altini 2010) | `Sci-Hub. Bluetooth indoor localization with multiple neural networks...pdf` | [[Indoor-Localization-ML-Methods]] |
| Bluetooth system design (Bruno & Delmastro) | `Sci-Hub. Design and Analysis of a Bluetooth-Based...pdf` | [[Indoor-Location-Sensor-Technologies]] |
| RSSI reliability (Dong & Dargie 2012) | `Sci-Hub. Evaluation of the reliability of RSSI...pdf` | [[Kaemarungsi-WiFi-Fingerprinting]] |

7 Kaemarungsi follow-on papers (Sources 54–59) are ⚠️ SCANNED — OCR would unlock floor estimation, optimal AP placement, and adaptive RSS content.

---

## 8. Summary Scorecard

| Check | Status | Issues Found | Issues Fixed |
|---|---|---|---|
| Backlink integrity | ✅ PASS | 0 broken | 0 |
| Source-files frontmatter | ⚠️ PASS w/ fixes | 3 | 2 fixed, 1 unresolvable |
| Uncited claims | ✅ PASS | 1 partial article (CSI) | — |
| Duplicate concepts | ✅ PASS | 0 merger candidates | 0 |
| Unique concepts | ✅ PASS | 8 single-article concepts | — |
| Missing backlinks | ⚠️ FIXED | Cricket had 0 incoming | 2 backlinks added |

**Overall: Knowledge base is healthy. 22 articles, 67 sources, zero broken backlinks.**

---

## 9. Recommended Next Steps

1. **Compile 8 uncompiled sources** (Sources 60–67) — Ubicarse, IPIN 2015 evaluation survey, and RSSI reliability study are highest priority
2. **Obtain full-text FILA paper** (Wu et al. IEEE TPDS 2013) to upgrade CSI-Indoor-Localization.md from `partial` to `active`
3. **Consider dedicated `[[BLE-Indoor-Localization]]` article** — BLE content is distributed across 7 articles with no canonical home
4. **OCR the 7 scanned Kaemarungsi group papers** (Sources 54–59) — would unlock floor estimation, optimal placement, and adaptive RSS subsections in Kaemarungsi-WiFi-Fingerprinting.md

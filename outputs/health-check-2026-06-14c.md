---
date: 2026-06-14
type: health-report
run: 3 (post-fix verification)
knowledge-articles: 22
sources-in-raw: 67
compiled: 49
uncompiled: 8
scanned: 7
duplicates: 3
---

# Knowledge Base Health Check — 2026-06-14c (Post-Fix Verification)

Run to verify fixes applied in health-check-2026-06-14b.md and confirm no regressions. No new compilation since last run; 8 sources remain pending (compilation plan presented, awaiting approval).

---

## 1. Backlink Integrity — ✅ CLEAN

All 22 `[[double bracket]]` link targets resolve to existing `.md` files. No broken backlinks.

**Change from previous run:** Cricket-Location-Support-System now has **2 incoming backlinks** (was 0). Fixes confirmed:
- `ORL-Ultrasonic-Location-System.md` → `[[Cricket-Location-Support-System]]` ✅
- `Indoor-Location-Sensor-Technologies.md` → `[[Cricket-Location-Support-System|Cricket]]` ✅

---

## 2. Source-Files Frontmatter — ✅ CLEAN

**Zero broken references.** All 49 source-file paths listed in article frontmatter resolve to files present in raw/.

17 files in raw/ are not in any article's frontmatter — all accounted for:

| Category | Count | Status |
|---|---|---|
| Pending compilation (Sources 60–67) | 8 | Plan presented, awaiting approval |
| Scanned PDFs (Sources 53–59) | 7 | Cannot compile without OCR; documented in register |
| Sci-Hub duplicates (Sources 50–52) | 3 | Duplicate content already compiled; documented in register |
| Grep false-positive (Eeyes mobicom14 wang.pdf) | 1 | IS registered in E-eyes frontmatter; grep misses single-line YAML arrays |

**Note on false positive:** `E-eyes-Device-Free-Localization.md` uses single-line array syntax (`source-files: ["raw/...", "raw/..."]`), which causes the grep to capture only the last item on each line. The file IS correctly registered. Recommend normalising to multi-line YAML format for consistency (low priority).

---

## 3. YAML Frontmatter Completeness — ✅ PERFECT

All 22 articles have all required fields: `tags`, `date-compiled`, `source-files`, `status`. No missing fields across any article.

| Status | Count |
|---|---|
| active | 21 |
| partial | 1 (CSI-Indoor-Localization — abstract-only source) |

---

## 4. Uncited Claims — ✅ ACCEPTABLE

One article remains partial: **CSI-Indoor-Localization.md** — source is a 1-page Zotero abstract snapshot. All claims in the article are drawn from and attributed to the available abstract. No unattributed claims elsewhere.

---

## 5. Duplicate Concepts — ✅ NO MERGERS NEEDED

No change from previous run. The distributed treatment of overlapping topics (WiFi fingerprinting across 18 articles, BLE across 7, PDR across 10) remains appropriate — each article presents a distinct angle. No articles recommended for merger.

---

## 6. Unique Concepts (Single-Article Coverage)

No change from previous run:

| Concept | Article |
|---|---|
| MinMode inference | Cricket-Location-Support-System |
| AUV-aided localization | UWSN-Localization |
| DV-Hop routing | UWSN-Localization |
| EvAAL benchmarking framework | Indoor-IPS-Datasets |
| LoRaWAN indoor RSSI | Indoor-IPS-Datasets |
| VLC / Visible Light Communication | Indoor-Location-Sensor-Technologies |
| Through-wall Doppler radar | E-eyes-Device-Free-Localization |

**Concepts not yet present** (will appear after pending compilation plan is approved):
- `maxL-minE` algorithm → Chen 2006 (Source 60) → [[UnLoc-Unsupervised-Localization]]
- `BIPS` (Bluetooth Indoor Positioning System) → Bruno & Delmastro 2003 (Source 66) → [[Indoor-Location-Sensor-Technologies]]
- SAR / Synthetic Aperture Radar localization → Ubicarse (Source 63) → [[EZ-Indoor-Localization]]
- RSSI reliability empirics → Dong & Dargie 2012 (Source 67) → [[RADAR-WiFi-Fingerprinting]]

---

## 7. Incoming Backlink Distribution

| Article | Links | Δ vs. last run |
|---|---|---|
| Indoor-Location-Sensor-Technologies | 19 | — |
| Indoor-Localization-ML-Methods | 18 | — |
| RADAR-WiFi-Fingerprinting | 12 | — |
| Horus-WLAN-System | 11 | — |
| WiFi-Fingerprinting-Advances | 10 | — |
| Indoor-IPS-Datasets | 10 | — |
| ORL-Ultrasonic-Location-System | 8 | — |
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
| Cricket-Location-Support-System | 2 | **+2** (fixed previous run) |

---

## 8. Summary Scorecard

| Check | Status | Issues | Actions |
|---|---|---|---|
| Backlink integrity | ✅ PASS | 0 | — |
| Source-files frontmatter | ✅ PASS | 1 false-positive (grep limitation) | No action needed |
| YAML completeness | ✅ PASS | 0 | — |
| Uncited claims | ✅ PASS | 1 partial article (CSI) | Needs full paper |
| Duplicate concepts | ✅ PASS | 0 | — |
| Unique concepts | ✅ PASS | 7 single-article concepts | Expected; not gaps |
| Backlink isolation | ✅ PASS | 0 isolated articles | — |

**Overall: Knowledge base is fully healthy. Previous fixes confirmed. Zero regressions.**

---

## 9. Pending Actions

1. **Approve and execute compilation plan** — 8 sources (60–67) are extracted and planned; 6 article updates ready to write
2. **Obtain full-text CSI paper** — Wu et al. IEEE TPDS 2013 (FILA); upgrade CSI-Indoor-Localization.md to `active`
3. **Normalise E-eyes YAML** — convert single-line `source-files: [...]` to multi-line block format (cosmetic only)
4. **OCR the 7 scanned Kaemarungsi papers** — would unlock Sources 54–59 (floor estimation, optimal placement, adaptive RSS, BLE multipath)

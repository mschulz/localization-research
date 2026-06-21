---
date: 2026-06-15
type: health-check
articles-checked: 22
sources-in-raw: 69
sources-compiled: 65 (of 69; 4 uncompilable)
---

# Knowledge Base Health Check — 2026-06-15

## Summary

Knowledge base is **healthy**. No broken backlinks. No broken frontmatter references. One new uncompiled source discovered in `raw/`. Three new sources compiled since last health check (Singh 2021, Roohi & Roshan Fekr 2025, Aziz & Koo 2025 supplementary).

---

## 1. Backlink Validation

**Result: ✅ PASS — 0 broken backlinks**

All 24 unique `[[double bracket]]` references resolve to existing articles in `knowledge/`:

Active-Badge, CSI-Indoor-Localization, Context-Aware-Computing, Cricket-Location-Support-System, E-eyes-Device-Free-Localization, EZ-Indoor-Localization, Horus-WLAN-System, Indoor-IPS-Datasets, Indoor-Localization-Applications-by-Sector, Indoor-Localization-ML-Methods, Indoor-Location-Sensor-Technologies, Kaemarungsi-WiFi-Fingerprinting, Magnetic-Field-Localization, ORL-Ultrasonic-Location-System, Pedestrian-Dead-Reckoning, RADAR-WiFi-Fingerprinting, UWB-LSTM-Localization, UWSN-Localization, UnLoc-Unsupervised-Localization, Walkie-Markie-Indoor-Mapping, WiFi-Fingerprinting-Advances, Zee-Zero-Effort-Crowdsourcing.

---

## 2. Source-Files Frontmatter Audit

**Result: ✅ PASS — 0 broken references**

All 65 files cited in article frontmatter (both YAML list and inline JSON formats) exist in `raw/`.

### Files in raw/ not referenced in any article frontmatter (10 total)

| File | Category | Status |
|---|---|---|
| `Sci-Hub. A Survey of Indoor Localization Systems and Technologies...pdf` | Duplicate | Dup of `A_Survey_of_Indoor_Localization_Systems_and_Techno.pdf` — intentional |
| `Sci-Hub. Indoor localization without the pain...pdf` | Duplicate | Dup of `ez-mobicom.pdf` — intentional |
| `Sci-Hub. Recent Advances in Indoor Localization...pdf` | Duplicate | Dup of `077620951.pdf` — intentional |
| `CSI_based_Indoor_Localization.pdf` | Duplicate | Preprint of Wu et al. 2013 FILA — same paper as `Sci-Hub. CSI-Based Indoor Localization...pdf` (compiled) |
| `A Practical Approach to Landmark Deployment for Indoor Localization - Chen 2006.pdf` | Alternate copy | Clean version; Sci-Hub copy already in `RADAR-WiFi-Fingerprinting.md` frontmatter |
| `Sci-Hub. Analysis of WLAN's received signal strength indication...pdf` | Scanned PDF | Cannot extract text — flagged in reference-register |
| `Sci-Hub. Indoor localization improvement via adaptive RSS fingerprinting database...pdf` | Scanned PDF | Koweerawong 2013 — cannot extract text |
| `Sci-Hub. Optimal placement of reference nodes...pdf` | Scanned PDF | Aomumpai 2014 — cannot extract text |
| `IEEE Xplore Full-Text PDF.pdf` | Partial source | Liu et al. 2023 — 1-page abstract only; full paper behind paywall |
| **`v1_ CSI_based_Indoor_localization_using_Ensemble_Neural_Networks.pdf`** | **⚠️ NEW UNCOMPILED** | **Sobehy, Renault & Mühlethaler (IFIP MLN 2019) — CSI + Ensemble NNs. Not yet compiled.** |

**Action required**: Compile `v1_ CSI_based_Indoor_localization_using_Ensemble_Neural_Networks.pdf` → likely updates `Indoor-Localization-ML-Methods.md` and `CSI-Indoor-Localization.md`.

### Frontmatter format inconsistency (minor)

Some articles use inline JSON format (`source-files: ["raw/file.pdf"]`) and others use YAML list format. Both are valid but inconsistent. Articles using inline JSON: Cricket, Zee, Walkie-Markie, Pedestrian-Dead-Reckoning, UnLoc, E-eyes, UWSN, Horus, UWB-LSTM. No action required but standardisation to YAML list format would improve tooling compatibility.

---

## 3. Contradiction Flags (Active)

Three active contradictions tracked across articles. All flagged inline with ⚠️:

| Contradiction | Articles |
|---|---|
| **BLE/iBeacon 12cm vs 1–3m accuracy**: Aziz & Koo / Singh 2021 cite 12cm optimised result; Yang et al. 2021 states 1–3m general deployment | `Indoor-Localization-Applications-by-Sector`, `Indoor-Localization-ML-Methods`, `Indoor-Location-Sensor-Technologies` |
| **VAE vs SMOTE for imbalanced fingerprinting**: Alhomayani & Mahoor 2021 show VAE/CVAE outperforms SMOTE/ADASYN because RSS distributions are not linearly interpolable | `Indoor-Localization-ML-Methods`, `Indoor-IPS-Datasets` |
| **Coverage-optimal vs localisation-optimal AP placement**: Coverage deployments (collinear) systematically underperform vs symmetric distributed placement for fingerprinting | `RADAR-WiFi-Fingerprinting` |

All contradictions are accurately flagged with source attribution. No new contradictions introduced by Sources 71–73.

---

## 4. Duplicate Concept Detection

No concepts require merging. The following topics appear in multiple articles **appropriately** (as cross-references, not redundant primary content):

- **BLE 12cm accuracy claim**: Primary in `Indoor-Localization-Applications-by-Sector`; contradiction-flagged cross-refs in `Indoor-Localization-ML-Methods` and `Indoor-Location-Sensor-Technologies` ✅
- **VAE/CVAE augmentation (Alhomayani)**: Primary in `Indoor-Localization-ML-Methods`; cross-referenced in `Indoor-IPS-Datasets` ✅
- **UWB LSTM 7cm / Poulose**: Primary in `UWB-LSTM-Localization`; cited as benchmarks across 4 other articles ✅
- **UJIIndoorLoc dataset**: Primary in `Indoor-IPS-Datasets`; cited as benchmark in `Indoor-Localization-ML-Methods` and `Magnetic-Field-Localization` ✅
- **Wearable/Contactless taxonomy (Roohi)**: Primary in `Indoor-Location-Sensor-Technologies`; mentioned in `Indoor-Localization-Applications-by-Sector` ✅

---

## 5. Unique Concepts (appearing in only one article)

These topics are each covered in exactly one article. They represent the unique intellectual contribution of that article to the knowledge base. No expansion or cross-linking needed at this time.

| Unique Concept | Home Article |
|---|---|
| Radio map construction via UAV / WiGAN (GPR-GAN) / GIFT (gradient fingerprinting) / SLAM / crowdsourcing | `Indoor-Localization-ML-Methods` |
| Wearable vs Contactless vs Fusion taxonomy; contactless mean 0.39±0.55m vs wearable 1.66±1.62m | `Indoor-Location-Sensor-Technologies` |
| Poucet system (GPS+LoRa+UWB+altimeter) / Project Sentinel (hard+soft+predetermined fusion) / AARs / fratricide reduction | `Indoor-Localization-Applications-by-Sector` |
| FILA OFDM subcarrier frequency diversity / Pearson-correlation fingerprinting | `CSI-Indoor-Localization` |
| WiFi-Mark (RSS trend reversal landmark) / pathway map embedding | `Walkie-Markie-Indoor-Mapping` |
| MinMode inference / RF+ultrasonic concurrent transmission (Cricket) | `Cricket-Location-Support-System` |
| Geomagnetic anomaly fingerprinting (5 coordinate systems) / DTW-magnetic | `Magnetic-Field-Localization` |
| Calm technology / tabs-pads-boards / Weiser ubicomp vision | `Context-Aware-Computing` |
| ZUPT / foot-mounted IMU / zero-velocity update / step event formulation | `Pedestrian-Dead-Reckoning` |
| Ambient landmark detection (WiFi/magnetic/inertial) / SLAM-inspired zero-war-drive localization | `UnLoc-Unsupervised-Localization` |
| Underwater acoustic modem / AUV-aided localization / 3D UW ranging | `UWSN-Localization` |
| Augmented particle filter (x, y, stride, heading offset) / zero-effort crowdsourcing | `Zee-Zero-Effort-Crowdsourcing` |
| Log-distance path loss + genetic algorithm / zero-calibration WiFi | `EZ-Indoor-Localization` |
| chi-squared distance metric / left-skewed RSS Gaussian / AP placement guidelines | `Kaemarungsi-WiFi-Fingerprinting` |
| IR sequential time-multiplexed location system / 12-bit badge ID | `Active-Badge` |
| Bayesian MAP / temporal autocorrelation / location clustering with joint probability | `Horus-WLAN-System` |
| EMD classifier / MD-DTW / device-free CSI activity recognition | `E-eyes-Device-Free-Localization` |

---

## 6. New Uncompiled Source Requiring Action

```
raw/v1_ CSI_based_Indoor_localization_using_Ensemble_Neural_Networks.pdf
```

**Authors**: Sobehy, A., Renault, É., Mühlethaler, P.  
**Venue**: MLN 2019 (2nd IFIP International Conference on Machine Learning for Networking), Paris, December 2019  
**DOI**: 10.1007/978-3-030-45778-5_25 (HAL: hal-02334588)  
**Content**: CSI-based indoor localization using ensemble neural networks. Builds on FILA-class CSI fingerprinting. Likely contributes to `CSI-Indoor-Localization.md` and `Indoor-Localization-ML-Methods.md`.  
**Status**: Uncompiled — file readable (not scanned). Ready for processing.

---

## 7. Knowledge Base Statistics

| Metric | Value |
|---|---|
| Articles | 22 |
| Sources in raw/ | 69 |
| Sources compiled (full text) | 57 |
| Sources compiled (partial/supplementary) | 3 |
| Sources uncompilable (scanned) | 3 |
| Sources partial (paywall/truncated) | 1 |
| Sources duplicate (not compiled) | 5 |
| Broken backlinks | **0** |
| Broken frontmatter refs | **0** |
| Active contradiction flags | 3 |
| New uncompiled sources found | **1** |

---

## 8. Recommended Next Actions

1. **Compile `v1_ CSI_based_Indoor_localization_using_Ensemble_Neural_Networks.pdf`** — new source, ready to process
2. **Upload full PDF of Liu et al. IEEE Access 2023** (`IEEE Xplore Full-Text PDF.pdf`) if accessible — currently only abstract
3. **Upload clean PDFs for 3 scanned sources** if accessible:
   - Koweerawong et al. ICOIN 2013 (adaptive RSS fingerprint)
   - Aomumpai et al. 2014 (optimal AP placement)
   - WLAN RSSI analysis (scanned, no author identified)
4. **Standardise frontmatter format** from inline JSON to YAML list (low priority)

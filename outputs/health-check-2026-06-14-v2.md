# Knowledge Base Health Check

> Date: 2026-06-14 (v2 — post 5-PDF compilation pass)
> Articles checked: 14
> Sources in raw/: 15
> Status: ✅ Healthy — 0 issues found; 2 tracked contradictions; no merges required

---

## 1. Backlink Validation

Scanned all 14 articles for `[[double-bracket]]` links and verified each resolves to a file in `knowledge/`.

| Backlink | Status |
|---|---|
| `[[Active-Badge]]` | ✅ Resolves |
| `[[Context-Aware-Computing]]` | ✅ Resolves |
| `[[E-eyes-Device-Free-Localization]]` | ✅ Resolves |
| `[[Horus-WLAN-System]]` | ✅ Resolves |
| `[[Indoor-IPS-Datasets]]` | ✅ Resolves |
| `[[Indoor-Localization-Applications-by-Sector]]` | ✅ Resolves |
| `[[Indoor-Localization-ML-Methods]]` | ✅ Resolves |
| `[[Indoor-Location-Sensor-Technologies]]` | ✅ Resolves |
| `[[ORL-Ultrasonic-Location-System]]` | ✅ Resolves |
| `[[RADAR-WiFi-Fingerprinting]]` | ✅ Resolves |
| `[[UWSN-Localization]]` | ✅ Resolves |
| `[[UnLoc-Unsupervised-Localization]]` | ✅ Resolves |
| `[[Walkie-Markie-Indoor-Mapping]]` | ✅ Resolves |
| `[[WiFi-Fingerprinting-Advances]]` | ✅ Resolves |

**No broken backlinks detected.** (Compares favourably to v1 which had one broken `[[Active Office]]` link, already fixed.)

---

## 2. YAML Frontmatter Audit

All 14 articles have the four required fields: `tags`, `date-compiled`, `source-files`, `status`.

| Article | tags | date-compiled | source-files | status |
|---|---|---|---|---|
| Active-Badge.md | ✅ | ✅ | ✅ | ✅ |
| Context-Aware-Computing.md | ✅ | ✅ | ✅ | ✅ |
| E-eyes-Device-Free-Localization.md | ✅ | ✅ | ✅ | ✅ |
| Horus-WLAN-System.md | ✅ | ✅ | ✅ | ✅ |
| Indoor-IPS-Datasets.md | ✅ | ✅ | ✅ | ✅ |
| Indoor-Localization-Applications-by-Sector.md | ✅ | ✅ | ✅ | ✅ |
| Indoor-Localization-ML-Methods.md | ✅ | ✅ | ✅ (5 sources) | ✅ |
| Indoor-Location-Sensor-Technologies.md | ✅ | ✅ | ✅ (6 sources) | ✅ |
| ORL-Ultrasonic-Location-System.md | ✅ | ✅ | ✅ | ✅ |
| RADAR-WiFi-Fingerprinting.md | ✅ | ✅ | ✅ | ✅ |
| UWSN-Localization.md | ✅ | ✅ | ✅ | ✅ |
| UnLoc-Unsupervised-Localization.md | ✅ | ✅ | ✅ | ✅ |
| Walkie-Markie-Indoor-Mapping.md | ✅ | ✅ | ✅ | ✅ |
| WiFi-Fingerprinting-Advances.md | ✅ | ✅ | ✅ (2 sources) | ✅ |

---

## 3. Source Citation Coverage

All source-files listed in article frontmatter were verified against `raw/`. **No missing raw files.**

| Article | Sources cited | All present in raw/ |
|---|---|---|
| ORL-Ultrasonic-Location-System | ORL tr.97.10 | ✅ |
| Context-Aware-Computing | ORL tr.97.10 | ✅ |
| Active-Badge | ORL tr.97.10 | ✅ |
| Indoor-Location-Sensor-Technologies | ORL + sensors + applsci + 2403 + 077620951 + A_Survey_of_Indoor | ✅ all 6 |
| Indoor-Localization-ML-Methods | sensors + 2403 + infocom2000 + youssef + A_Survey_of_ML | ✅ all 5 |
| UWSN-Localization | 1-s2.0 UWSN paper | ✅ |
| Indoor-Localization-Applications-by-Sector | applsci-15 | ✅ |
| Indoor-IPS-Datasets | 2403.04333 | ✅ |
| RADAR-WiFi-Fingerprinting | infocom2000 | ✅ |
| Horus-WLAN-System | youssef | ✅ |
| E-eyes-Device-Free-Localization | Eeyes mobicom14 wang | ✅ |
| WiFi-Fingerprinting-Advances | COMST16 + 1610.05424 | ✅ both |
| UnLoc-Unsupervised-Localization | unloc.pdf | ✅ |
| Walkie-Markie-Indoor-Mapping | nsdi13-final130 | ✅ |

**_index.md is accurate**: 14 articles listed, 15 source files listed — both match disk contents exactly.

---

## 4. Contradiction Flags

Two active contradictions are correctly flagged in the knowledge base:

### Contradiction 1 — BLE/iBeacon accuracy in healthcare

- `Indoor-Localization-Applications-by-Sector.md` (citing Aziz & Koo 2025, Nguyen et al. 2019): iBeacon accuracy of **12cm** in hospital settings.
- `Indoor-Location-Sensor-Technologies.md` (citing Yang et al. 2021): BLE typical accuracy **1–3m**.
- `Indoor-Localization-ML-Methods.md` (citing Yang et al. 2021): BLE typical accuracy **1–3m**.
- **Status**: ⚠️ Flagged in both articles. Assessment: both figures are plausible — 12cm from a highly optimised controlled study; 1–3m is representative of general deployment. No resolution needed; contradiction flag is informative and correct.

### Contradiction 2 — NLOS mention in Context-Aware-Computing

- `Context-Aware-Computing.md` line 77 mentions NLOS in the context of IR signal flooding ("NLOS-tolerant"). This is not a contradiction per se — it is a correct factual statement about the ParcTab system — but it caused the NLOS concept-distribution check to flag this article. **No action required**; the mention is accurate and in appropriate context.

---

## 5. Duplicate Concept Detection

The following concepts appear across multiple articles. Each case was reviewed:

| Concept | # Articles | Assessment |
|---|---|---|
| RSSI fingerprinting | 11 | ✅ Expected — foundational; each article contributes distinct scope |
| Dead-reckoning | 6 | ✅ OK — UnLoc/Walkie-Markie are canonical; others reference in passing |
| Particle filter | 6 | ✅ OK — ML Methods is canonical; others reference as technique |
| Machine learning / DNN | 8 | ✅ OK — ML Methods is canonical; others in context |
| NLOS | 5 | ✅ OK — ML Methods covers mitigation in depth; others contextual |
| Kalman filter | 4 | ✅ OK — ML Methods has full taxonomy; others contextual |
| UWB | 5 | ✅ OK — Sensor Technologies covers hardware; ML Methods covers algorithms; Applications covers use cases |
| Device-free | 4 | ✅ OK — E-eyes is canonical; others reference as category |
| Crowdsourcing | 4 | ✅ OK — UnLoc and Walkie-Markie are canonical; others reference as technique |
| SLAM | 4 | ✅ OK — UnLoc covers SLAM analogy; ML Methods covers SLAM+crowdsourcing; UWSN covers underwater SLAM; distinct scopes |
| BLE / Bluetooth | 5 | ✅ OK — Sensor Technologies covers hardware; ML Methods covers AoA+DL; Applications covers sector deployments |
| CSI | 4 | ✅ OK — E-eyes is canonical CSI article; others reference as signal type |

**No merges recommended.**

---

## 6. Unique Concepts per Article

Concepts that appear in exactly one article — confirming each article contributes non-overlapping knowledge:

| Unique Concept | Canonical Article |
|---|---|
| EMD (Earth Mover's Distance) classifier | E-eyes-Device-Free-Localization |
| MD-DTW (Multi-Dimensional DTW) trajectory matching | E-eyes-Device-Free-Localization |
| Arturia displacement-vector graph embedding | Walkie-Markie-Indoor-Mapping |
| WiFi-Mark (RSS trend reversal landmark) | Walkie-Markie-Indoor-Mapping (+ reference in WiFi-Fingerprinting-Advances) |
| OLM (Organic Landmark) K-means spatial confinement | UnLoc-Unsupervised-Localization |
| Cramér-Rao Lower Bound (CRLB) accuracy limits | Indoor-Location-Sensor-Technologies |
| DBL vs. MBL localization taxonomy | Indoor-Location-Sensor-Technologies |
| RTOF (Return Time of Flight) ranging | Indoor-Location-Sensor-Technologies |
| DeepFi (CSI + Restricted Boltzmann Machine) | Indoor-Localization-ML-Methods |
| GPMKDR dimension reduction | Indoor-Localization-ML-Methods |
| DQN / DRL robot navigation | Indoor-Localization-ML-Methods |
| Transfer learning for fingerprinting | Indoor-Localization-ML-Methods |
| AUV-aided UWSN localization | UWSN-Localization |
| FMCW RADAR device-free sensing | Indoor-Location-Sensor-Technologies / Indoor-Localization-ML-Methods |

---

## 7. _index.md Consistency

| Check | Result |
|---|---|
| Article count in index | 14 ✅ matches disk |
| Source file count in index | 15 ✅ matches disk |
| All article links resolve | ✅ 14/14 |
| All source file entries exist | ✅ 15/15 |

---

## 8. Summary of Actions Taken

No issues found requiring fixes in this health check. All problems detected in the previous health check (v1) remain resolved.

| # | Check | Result |
|---|---|---|
| 1 | Backlink validation (14 articles) | ✅ No broken links |
| 2 | YAML frontmatter (all 4 required fields) | ✅ All present |
| 3 | Source file coverage (frontmatter vs raw/) | ✅ All 15 raw files present |
| 4 | _index.md accuracy | ✅ Counts and links correct |
| 5 | Contradiction flags | ⚠️ 2 tracked, both correctly flagged, no resolution needed |
| 6 | Duplicate concept detection | ✅ No merges required |
| 7 | Unique concept inventory | ✅ 14 unique concepts identified |

---

## 9. Recommendations for Next Compilation

These high-priority references appear repeatedly across sources and would most enrich the knowledge base if added to `raw/`:

1. **Woodman & Harle** *Pedestrian localisation for indoor environments.* UbiComp 2008. — cited by both UnLoc and Walkie-Markie as the PDR foundation
2. **Rai et al.** *Zee: Zero-effort crowdsourcing for indoor localization.* MobiCom 2012. — cited by Walkie-Markie; zero-effort SLAM approach
3. **Chintalapudi et al.** *Indoor localization without the pain (EZ).* MobiCom 2010. — cited by UnLoc; GPS-seeded approach
4. **Poulose & Han** *UWB Indoor Localization Using Deep Learning LSTM Networks.* Appl. Sci. 2020. — cited by Sonny et al.; source of the 7cm UWB+LSTM accuracy figure
5. **Priyantha et al.** *The Cricket Location-Support System.* MobiCom 2000. — ultrasonic complement to RADAR; cited by Horus

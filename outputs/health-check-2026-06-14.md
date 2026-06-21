# Knowledge Base Health Check

> Date: 2026-06-14  
> Articles checked: 12  
> Sources in raw/: 10  
> Status: ✅ Healthy — 2 issues found and fixed; 1 contradiction tracked; no merges required

---

## 1. Backlink Validation

Scanned all 12 articles for `[[double-bracket]]` links and verified each resolves to a file in `knowledge/`.

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
| `[[WiFi-Fingerprinting-Advances]]` | ✅ Resolves |
| `[[Active Office]]` | ❌ **BROKEN** — no `Active-Office.md` exists |

**Fix applied:** `[[Active Office]]` in `ORL-Ultrasonic-Location-System.md` (line 12) was a dangling reference to a concept covered in `[[Context-Aware-Computing]]`. Removed the brackets (de-linked) to resolve without creating a stub article. The concept remains cited correctly in context.

---

## 2. YAML Frontmatter Audit

All 12 articles have the four required fields: `tags`, `date-compiled`, `source-files`, `status`.

| Article | tags | date-compiled | source-files | status |
|---|---|---|---|---|
| Active-Badge.md | ✅ | ✅ | ✅ | ✅ |
| Context-Aware-Computing.md | ✅ | ✅ | ✅ | ✅ |
| E-eyes-Device-Free-Localization.md | ✅ | ✅ | ✅ | ✅ |
| Horus-WLAN-System.md | ✅ | ✅ | ✅ | ✅ |
| Indoor-IPS-Datasets.md | ✅ | ✅ | ✅ | ✅ |
| Indoor-Localization-Applications-by-Sector.md | ✅ | ✅ | ✅ | ✅ |
| Indoor-Localization-ML-Methods.md | ✅ | ✅ | ✅ | ✅ |
| Indoor-Location-Sensor-Technologies.md | ✅ | ✅ | ✅ | ✅ |
| ORL-Ultrasonic-Location-System.md | ✅ | ✅ | ✅ | ✅ |
| RADAR-WiFi-Fingerprinting.md | ✅ | ✅ | ✅ | ✅ |
| UWSN-Localization.md | ✅ | ✅ | ✅ | ✅ |
| WiFi-Fingerprinting-Advances.md | ✅ | ✅ | ✅ | ✅ |

---

## 3. Source Citation Coverage

**Issue found and fixed:** `Indoor-Localization-ML-Methods.md` had RADAR and Horus sections added during a prior compilation pass (from `infocom2000.pdf` and `youssef.pdf`), but these source files were not reflected in its `source-files` frontmatter field.

**Fix applied:** Updated `source-files` to:
```yaml
source-files: ["raw/sensors-21-08086.pdf", "raw/2403.04333v1.pdf", "raw/infocom2000.pdf", "raw/youssef.pdf"]
```

**All other source-file mappings are accurate:**

| Article | Sources cited in frontmatter | Raw file present |
|---|---|---|
| ORL-Ultrasonic-Location-System | ORL New Location Technique | ✅ |
| Context-Aware-Computing | ORL New Location Technique | ✅ |
| Active-Badge | ORL New Location Technique | ✅ |
| Indoor-Location-Sensor-Technologies | ORL + sensors + applsci + 2403 | ✅ all |
| Indoor-Localization-ML-Methods | sensors + 2403 + infocom2000 + youssef | ✅ all (fixed) |
| UWSN-Localization | 1-s2.0 UWSN paper | ✅ |
| Indoor-Localization-Applications-by-Sector | applsci-15 | ✅ |
| Indoor-IPS-Datasets | 2403.04333 | ✅ |
| RADAR-WiFi-Fingerprinting | infocom2000 | ✅ |
| Horus-WLAN-System | youssef | ✅ |
| E-eyes-Device-Free-Localization | Eeyes mobicom14 wang | ✅ |
| WiFi-Fingerprinting-Advances | COMST16 + 1610.05424 | ✅ all |

---

## 4. Contradiction Flags

One active contradiction is correctly flagged in the knowledge base:

**BLE/iBeacon accuracy — healthcare context**
- `Indoor-Localization-Applications-by-Sector.md` (citing Aziz & Koo 2025, `raw/applsci-15-01544-v2.pdf`): iBeacon accuracy of **12cm** achieved in hospital settings (Nguyen et al. 2019).
- `Indoor-Localization-ML-Methods.md` (citing Yang et al. 2021, `raw/sensors-21-08086.pdf`): BLE typical accuracy is **1–3m**.
- Status: ⚠️ **Flagged** in `Indoor-Localization-Applications-by-Sector.md` lines 30 and 43. Both figures are plausible; the 12cm figure likely reflects a highly tuned specific deployment, not general BLE performance.

No other contradictions detected.

---

## 5. Duplicate Concept Detection

The following concepts appear in multiple articles. Each case was reviewed — none require merging, as each article treats the concept from a distinct scope:

| Concept | Articles | Assessment |
|---|---|---|
| RSSI fingerprinting / radio map | 9 articles | ✅ Expected — foundational concept; each article contributes distinct content |
| Kalman filters | 4 articles | ✅ OK — ML Methods has full taxonomy; others reference in application context |
| UWB | 5 articles | ✅ OK — IPS Technologies covers hardware; ML Methods covers algorithms; Applications covers use cases |
| Device-free localization | 4 articles | ✅ OK — E-eyes article is canonical; others reference as category/technique |

No merges recommended at this time.

---

## 6. Orphan / Dead Reference Check

All 10 `source-files` listed in the `_index.md` Source Files table exist in `raw/`. No article references a raw file that has been removed.

---

## Summary of Actions Taken

| # | Issue | Severity | Action |
|---|---|---|---|
| 1 | `[[Active Office]]` broken backlink in ORL article | Medium | Fixed — removed brackets |
| 2 | `Indoor-Localization-ML-Methods.md` missing `infocom2000.pdf` and `youssef.pdf` in source-files | Low | Fixed — updated frontmatter |

**No articles require merging. No stubs need creating. No data loss detected.**

---

## Recommendations for Next Compilation

The following high-priority references appear repeatedly across sources but are not yet in `raw/`. Adding them would most enrich the knowledge base:

1. **Yassin et al.** *Recent advances in indoor localization.* IEEE Comm. Surveys, 2016. — cited in Sources 2 and 4
2. **Zafari et al.** *A survey of indoor localization systems and technologies.* IEEE Comm. Surveys, 2019. — most comprehensive modern survey
3. **Nessa et al.** *A Survey of Machine Learning for Indoor Positioning.* IEEE Access, 2020. — cited in Source 2
4. **Wang et al.** *UnLoc: Unsupervised Indoor Localization.* MobiSys 2012. — cited in Sources 8 and 9; foundational landmark-based method
5. **Shen et al.** *Walkie-Markie.* NSDI 2013. — temporal pattern fingerprinting; cited in Source 9

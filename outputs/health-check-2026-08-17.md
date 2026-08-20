---
tags: [health-check, maintenance, knowledge-base]
date: 2026-08-17
type: health-report
run: scheduled (weekly, Monday 08:00)
---

# Knowledge Base Health Check — 2026-08-17

**Scope:** `knowledge/` (23 articles + `_index.md`), `raw/` (84 PDFs), `outputs/` (reports, reference register).
**Run type:** Automated weekly health check. No user present. Write actions this run were limited to writing this report. The authorised frontmatter check found no JSON to convert, so no article edits were made. No files were deleted or renamed.

## Summary

The graph remains structurally clean: **0 broken backlinks, 0 orphan articles, 0 dangling `source-files:` entries, 0 articles missing citations, and no JSON frontmatter to convert.**

Two of last week's four recommended actions are **closed**: `_index.md` has been refreshed (now correctly reports 84 sources, last updated 2026-08-14), and the duplicate *Wang et al. 2021 Fast Construction* Zotero copy is now registered.

Two carry-overs remain: **3 genuinely unprocessed sources** and **1 zero-byte file**. New this run: **3 dangling `raw/` paths in prose/table text** (not frontmatter) caused by filename transcription drift — a cosmetic but fixable citation-integrity issue.

Base has grown since 2026-08-10: **22 → 23 articles**, **79 → 84 sources**.

## 1. Broken backlinks

**None.** Every `[[wikilink]]` across all 23 articles and `_index.md` resolves to an existing file in `knowledge/`. ✅

**Orphans:** none. Every article receives at least one inbound link from another article, and all 23 appear in the `_index.md` article table (verified set-equal against disk).

## 2. Articles referencing sources no longer in `raw/`

**Frontmatter: clean.** All `source-files:` entries across all 23 articles resolve to a physically present file in `raw/` on a literal path match. ✅

**Prose / table references: 3 dangling paths.** These sit in body text and citation tables, not frontmatter, so they do not break compilation — but they will not resolve if followed.

| File | Line | Written as | Actual file on disk | Cause |
|---|---|---|---|---|
| `knowledge/_index.md` | 90 | `raw/Sci-Hub. Analysis of WLAN received signal strength indication for indoor location fingerprinting .pdf` | `…WLAN’s received signal…` | Missing curly apostrophe `’s` |
| `knowledge/Kaemarungsi-WiFi-Fingerprinting.md` | 250 | `raw/Sci-Hub. Indoor localization improvement via adaptive RSS fingerprinting database.pdf` | `…database : The Internationa.pdf` | Truncated Sci-Hub suffix |
| `knowledge/RADAR-WiFi-Fingerprinting.md` | 196 | `raw/fingerprint thesis.pdf` | *not present in `raw/`* | File removed; row already annotated "⚠️ 1-page Zotero placeholder — do not use" |

The RADAR entry is a deliberate do-not-use marker, so it is informational rather than an error; the other two are transcription drift and should be corrected to the exact on-disk filenames.

## 3. Claims lacking source citations

**None.** All 23 articles carry populated `tags`, `date-compiled`, `source-files`, and `status` frontmatter. `outputs/reference-register.md` maps sources to compiled articles (71 ✅ compiled markers, 3 🔁 duplicate markers across 252 table rows). ✅

## 4. Duplicate concepts

**No duplicate articles.** Each of the 23 covers a distinct system or concept; no merges are warranted.

**Duplicate source files** — verified by MD5 over all 84 PDFs (83 unique hashes):

- `A_Comprehensive_Review_of_Indoor_Localization_Tech.pdf` ≡ `applsci-15-01544-v2.pdf` — **byte-identical** (Aziz & Koo 2025). Already flagged in `_index.md`; content merged into [[Indoor-Location-Sensor-Technologies]]. No action.
- `Sci-Hub. Indoor localization without the pain …pdf` = `ez-mobicom.pdf` (Chintalapudi et al., MobiCom 2010) — Sci-Hub mirror, different bytes, same paper. Flagged 🔁.
- `Sci-Hub. A Survey of Indoor Localization Systems and Technologies …pdf` = `A_Survey_of_Indoor_Localization_Systems_and_Techno.pdf` (Zafari et al. 2019) — flagged 🔁.
- `Sci-Hub. Recent Advances in Indoor Localization …pdf` = `077620951.pdf` (Yassin et al. 2017) — flagged 🔁.
- `Wang et al. - 2021 - Fast Construction … | My Library | Zotero.pdf` = `fast-construction-of-the-radio-map-based-on-the-improved-low-115csk6gui.pdf` — **now registered in `_index.md` (line 61). Closed since last run.** ✅

## 5. Frontmatter format (YAML) — task-specific check

**All 23 compiled articles use valid YAML frontmatter**, delimited by `---`, and all carry the four required keys (`tags`, `date-compiled`, `source-files`, `status`). **No JSON frontmatter was found anywhere, so no conversion was performed.** ✅

`_index.md` carries no frontmatter by design (it is the index, not a compiled article) — left unchanged.

One article, `WiFi-Fingerprinting-Advances.md`, additionally carries an `updated: 2026-08-14` key. This is a valid YAML extension and is preserved.

## 6. Unique concepts across the base

All 23 articles are distinct, non-overlapping concept nodes:

- **Foundational systems** — [[ORL-Ultrasonic-Location-System]], [[Active-Badge]], [[Cricket-Location-Support-System]], [[Context-Aware-Computing]]
- **WiFi fingerprinting lineage** — [[RADAR-WiFi-Fingerprinting]], [[Horus-WLAN-System]], [[Kaemarungsi-WiFi-Fingerprinting]], [[WiFi-Fingerprinting-Advances]]
- **Zero-calibration / crowdsourcing** — [[EZ-Indoor-Localization]], [[Zee-Zero-Effort-Crowdsourcing]], [[UnLoc-Unsupervised-Localization]], [[Walkie-Markie-Indoor-Mapping]]
- **Device-free / channel-state** — [[E-eyes-Device-Free-Localization]], [[CSI-Indoor-Localization]]
- **Inertial / field-based** — [[Pedestrian-Dead-Reckoning]], [[Magnetic-Field-Localization]]
- **Deep learning / signal recovery** — [[UWB-LSTM-Localization]], [[Compressive-Sensing-Localization]]
- **Surveys / cross-cutting** — [[Indoor-Location-Sensor-Technologies]], [[Indoor-Localization-ML-Methods]], [[Indoor-Localization-Applications-by-Sector]], [[Indoor-IPS-Datasets]], [[UWSN-Localization]]

`Compressive-Sensing-Localization` is the newest node (compiled 2026-08-14) and is the sole home for the ℓ₁ sparse-recovery approach, correctly cross-linked to `WiFi-Fingerprinting-Advances` for the fingerprinting-side reformulation. No concept overlap detected.

## 7. Stale sources — not updated in 30+ days (task-specific flag)

Cutoff: last modified before **2026-07-18**.

**79 of 84 `raw/` PDFs exceed the 30-day threshold.** This is expected for a static source archive, so the actionable signal is which stale sources remain **uncompiled**.

**Modified within the last 30 days (5 files — all compiled 2026-08-14):**

| Age | File | Status |
|---|---|---|
| 19d | `balestrieri2020.pdf` | ✅ → [[Compressive-Sensing-Localization]] |
| 26d | `tabibiazar2011.pdf` | ✅ → [[Compressive-Sensing-Localization]] |
| 26d | `2107.01192v1.pdf` | ✅ → [[WiFi-Fingerprinting-Advances]] |
| 26d | `s41598-024-75306-3.pdf` | ✅ → [[WiFi-Fingerprinting-Advances]] |
| 29d | `I am researching localisation techniques… .pdf` | ⚠️ Not compiled — synthetic LLM secondary source, catalogued only |

*(Ages are calendar-day differences from 2026-08-17.)*

**Genuinely unprocessed stale sources (no register entry, not compiled) — carried over from 2026-08-10 and 2026-08-06, action still recommended:**

- `raw/1901_Enhancing Bluetooth Location Service_FINAL.pdf` (2019-02-19, 2736 days) — orphan
- `raw/Adaptive_Sampling_for_Fingerprinting_Localization.pdf` (2026-06-15, 63 days) — orphan; distinct from the already-compiled *Liu et al. 2016 — Adaptive Sampling of RF Fingerprints*
- `raw/TIIS Vol 13, No 1-12.pdf` (2019-02-22, 2733 days) — orphan

**Empty / corrupt file — removal recommended:**

- `raw/A Practical Approach to Landmark Deployment for Indoor Localization - Chen 2006.pdf` — **0 bytes**. The paper is already compiled into [[RADAR-WiFi-Fingerprinting]] via the working Sci-Hub copy (register Source 60), so this file is redundant. Third consecutive run flagging it.

**Oldest sources by mtime** (original publication-era timestamps, not staleness of content): `ORL New Location Technique tr.97.10.pdf` (2001-04-12), then the two 2019 orphans above.

## 8. Index integrity

`knowledge/_index.md` is **current** — a change from the last three runs.

- Header reads *"Sources in raw/: 84 PDFs (83 content-bearing + 1 empty)"* — matches disk exactly (84 files, 83 unique hashes, 1 zero-byte).
- *"Last updated: 2026-08-14"* — 3 days old.
- Article table lists 23 entries; disk holds 23 article files. **Set-equal, no drift.** ✅
- Source-tracking table holds 78 `raw/` rows.

**Last week's recommended action 4 (update index header 69 → 79) is closed.** ✅

## Recommended actions

1. **Compile or triage the 3 unprocessed sources** — *Enhancing Bluetooth* (2019), *Adaptive Sampling for Fingerprinting Localization*, *TIIS Vol 13*. Outstanding across three runs; the second is the most likely to add substantive content given the existing fingerprinting cluster.
2. **Delete the 0-byte** `A Practical Approach … Chen 2006.pdf` — paper already covered via the working Sci-Hub copy.
3. **Repair the 2 dangling prose citations** (§2) — restore the curly apostrophe in `_index.md` line 90 and the full Sci-Hub suffix in `Kaemarungsi-WiFi-Fingerprinting.md` line 250.
4. **Optionally decide on** `raw/fingerprint thesis.pdf` — referenced in `RADAR-WiFi-Fingerprinting.md` line 196 but absent from `raw/`. Either drop the row or re-word it as a historical note.

## Change since last run (2026-08-10)

| Metric | 2026-08-10 | 2026-08-17 |
|---|---|---|
| Articles | 22 | **23** (+`Compressive-Sensing-Localization`) |
| Sources in `raw/` | 79 | **84** |
| Broken backlinks | 0 | 0 |
| Dangling frontmatter refs | 0 | 0 |
| JSON frontmatter | 0 | 0 |
| Unprocessed orphan sources | 3 | 3 (unchanged) |
| 0-byte files | 1 | 1 (unchanged) |
| `_index.md` header accurate | ❌ (69 vs 79) | ✅ (84 vs 84) |
| Wang et al. Zotero dup registered | ❌ | ✅ |

Five sources were added and four compiled in the 2026-08-14 run, producing one new article and expanding `WiFi-Fingerprinting-Advances`. Structural health is unchanged and clean. This run executed on schedule (Monday 08:00).

---
*Generated by the scheduled weekly health check. Backlink resolution, `source-files:` path resolution, frontmatter parsing, MD5 duplicate detection, and 30-day staleness were all computed programmatically over 23 articles and 84 `raw/` files. Task scope this run: health check + 30-day staleness flag + YAML frontmatter check. No JSON frontmatter existed to convert, and no deletions or renames were requested, so none were made.*

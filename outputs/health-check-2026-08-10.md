---
tags: [health-check, maintenance, knowledge-base]
date: 2026-08-10
type: health-report
run: scheduled (weekly, Monday 08:00)
---

# Knowledge Base Health Check — 2026-08-10

**Scope:** `knowledge/` (22 articles + `_index.md`), `raw/` (79 PDF sources, 80 files incl. `.DS_Store`), `outputs/` (reports).
**Run type:** Automated weekly health check. No user present. Write actions this run were limited to writing this report; the authorized frontmatter YAML check found no JSON to convert (§5), so no article edits were made. No files were deleted or renamed.

## Summary

The knowledge graph is healthy and structurally unchanged: **0 broken backlinks, 0 dangling source references, 0 articles missing citations, and no JSON frontmatter to convert (all frontmatter is already YAML).** Nothing has entered `raw/` since mid-June, so **all 79 sources trip the 30-day staleness flag** — expected for a static archive.

**Notable correction vs. last week (2026-08-06):** the prior report flagged 10 `source-files:` paths as literal-path mismatches (cleaned names missing the Zotero suffix). That was a false alarm. This run verifies programmatically that **all 83 `source-files:` entries resolve exactly to files on disk** — the frontmatter already stores the full Zotero-suffixed names (e.g. `… Positioning .pdf | My Library |.pdf`). No path normalization is needed; item 1 from last week's recommended actions is closed.

Carried-over items still outstanding: one 0-byte source file, three genuinely unprocessed sources, one uncompiled duplicate copy, and a stale `_index.md` header (says 69 sources; 79 present).

## 1. Broken backlinks

None. Every `[[wikilink]]` across all 22 articles resolves to an existing article file. ✅

## 2. Articles referencing sources no longer in `raw/`

None. All 83 `source-files:` references across the 22 articles resolve to a physically present file in `raw/` on a literal path match. ✅ (This supersedes last week's path-mismatch flag — see Summary.)

## 3. Claims lacking source citations

None. All 22 articles carry populated `source-files:` frontmatter, and `reference-register.md` maps each source to its compiled article. ✅

## 4. Duplicate concepts

No duplicate **articles** — each of the 22 covers a distinct system or concept; no merges required. Known duplicate **source files** remain correctly identified and status-flagged in `reference-register.md` (no action needed):

- `Sci-Hub. Indoor localization without the pain …pdf` = `ez-mobicom.pdf` (Chintalapudi et al., MobiCom 2010).
- `Sci-Hub. A Survey of Indoor Localization Systems and Technologies …pdf` = `A_Survey_of_Indoor_Localization_Systems_and_Techno.pdf` (Zafari et al. 2019).
- `Sci-Hub. Recent Advances in Indoor Localization …pdf` = `077620951.pdf` (Yassin et al. 2017).
- `A_Comprehensive_Review_of_Indoor_Localization_Tech.pdf` = `applsci-15-01544-v2.pdf` (Aziz & Koo 2025) — byte-identical (9,573,515 bytes each); already merged into the sensor-technologies article.

**New this run:** `Wang et al. - 2021 - Fast Construction of the Radio Map … | My Library | Zotero.pdf` is a duplicate copy of the already-compiled `fast-construction-of-the-radio-map-based-on-the-improved-low-115csk6gui.pdf` (same paper, cited in `[[WiFi-Fingerprinting-Advances]]`). The Zotero copy itself is not registered. Content is covered; recommend adding a duplicate note to `reference-register.md` or removing the redundant copy.

## 5. Frontmatter format (YAML) — task-specific check

**All 22 compiled articles use valid YAML frontmatter** delimited by `---`. **No JSON frontmatter was found, so no conversion was performed.** `_index.md` has no frontmatter by design (it is the index, not a compiled article) — no change made. Task requirement satisfied. ✅

## 6. Unique concepts across the base

Each article is a distinct, non-overlapping concept node:

Foundational systems — ORL Ultrasonic, Active Badge, Cricket, Context-Aware Computing. WiFi fingerprinting lineage — RADAR, Horus, Kaemarungsi analytical model, WiFi-Fingerprinting-Advances. Zero-calibration / crowdsourcing — EZ, Zee, UnLoc, Walkie-Markie. Device-free / channel-state — E-eyes, CSI-Indoor-Localization. Inertial / field-based — Pedestrian Dead Reckoning, Magnetic-Field-Localization. Deep learning — UWB-LSTM. Surveys / cross-cutting — Indoor-Location-Sensor-Technologies, Indoor-Localization-ML-Methods, Indoor-Localization-Applications-by-Sector, Indoor-IPS-Datasets, UWSN-Localization.

No orphan articles: every article is reachable through `_index.md` and interlinked via backlinks.

## 7. Stale sources — not updated in 30+ days (task-specific flag)

Cutoff: files last modified before **2026-07-11**. By modification time, **all 79 `raw/` PDF sources exceed the 30-day threshold** (bulk touched on 2026-06-14/15; three legacy PDFs retain original dates: `ORL New Location Technique tr.97.10.pdf` 2001-04-12, `1901_Enhancing Bluetooth Location Service_FINAL.pdf` 2019-02-19, `TIIS Vol 13, No 1-12.pdf` 2019-02-22). Zero sources were modified within the last 30 days. This is expected for a static source archive, so the actionable signal is which stale sources are **still not compiled** into `knowledge/`:

**Genuinely unprocessed (no register entry, not compiled) — action recommended:**

- `raw/1901_Enhancing Bluetooth Location Service_FINAL.pdf` — orphan.
- `raw/Adaptive_Sampling_for_Fingerprinting_Localization.pdf` — orphan (distinct from the already-compiled *Liu et al. 2016 — Adaptive Sampling of RF Fingerprints*).
- `raw/TIIS Vol 13, No 1-12.pdf` — orphan.

**Empty / corrupt file — recommend removal:**

- `raw/A Practical Approach to Landmark Deployment for Indoor Localization - Chen 2006.pdf` — **0 bytes**. The paper is already compiled into `[[RADAR-WiFi-Fingerprinting]]` via the working Sci-Hub copy (register Source 60), so this empty file is redundant.

**Already status-flagged (no action needed):** the ⚠️ SCANNED / ⚠️ PARTIAL Sci-Hub sources (e.g. `Sci-Hub. Analysis of WLAN's received signal strength …pdf`, register Source 53) remain correctly flagged.

## 8. Index integrity

`knowledge/_index.md` is **out of date**:

- Header reads *"Sources in raw/: 69"* — `raw/` currently holds **79 PDFs**.
- *"Last updated: 2026-06-15"* — ~8 weeks stale.

Recommend refreshing the header count and last-updated date. (Not changed automatically — index maintenance falls under the compilation rules, not the health-check rules.)

## Recommended actions

1. Compile or triage the 3 unprocessed sources (Enhancing Bluetooth, Adaptive Sampling, TIIS Vol 13).
2. Delete the empty `A Practical Approach … Chen 2006.pdf` (paper already covered).
3. Note or remove the duplicate `Wang et al. 2021 Fast Construction … Zotero.pdf` copy (§4).
4. Update `_index.md` header: source count 69 → 79, refresh last-updated date.

## Change since last run (2026-08-06)

Same 22 articles, same 79 sources, same 0/0/0 backlink/dangling/citation status, frontmatter still all-YAML. The one substantive change is a **correction**: last week's §2 literal-path-mismatch flag (10 paths) was a false positive — this run confirms all `source-files:` paths resolve exactly on disk. All other outstanding items (3 orphans, 1 empty file, stale index header) are unchanged carry-overs. This run executed on schedule (Monday 08:00).

---
*Generated by the scheduled weekly health check. Structural checks (backlinks, source references, citations, frontmatter format, 30-day staleness) run programmatically over all 22 articles and 80 `raw/` files. Task scope this run: health check + 30-day staleness flag + YAML frontmatter check; no JSON frontmatter existed to convert, and no deletions/renames were requested, so none were made.*

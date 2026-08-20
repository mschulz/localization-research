---
tags: [health-check, maintenance, knowledge-base]
date: 2026-08-03
type: health-report
run: scheduled (weekly, Monday 08:00)
---

# Knowledge Base Health Check — 2026-08-03

**Scope:** `knowledge/` (22 articles + `_index.md`), `raw/` (79 PDF sources), `outputs/` (reports).
**Run type:** Automated weekly health check. No user present; write actions taken only where the task explicitly authorized them.

## Summary

The core knowledge graph is healthy: **0 broken backlinks, 0 dangling source references, 0 articles missing citations.** The main issues are (1) an **out-of-date `_index.md` header** (claims 69 sources; 79 are present), (2) **three genuinely unprocessed sources and one empty/corrupt file** sitting in `raw/`, and (3) the whole base has been **static for ~43–49 days**, so everything trips the 30-day staleness flag. Seven old reports are due for deletion but the automated run could not obtain delete permission (see below).

## 1. Broken backlinks

None. Every `[[wikilink]]` across all 22 articles resolves to an existing article file. ✅

## 2. Articles referencing sources no longer in `raw/`

None. Every path in every article's `source-files:` frontmatter maps to a file that exists in `raw/`. ✅

## 3. Claims lacking source citations

No structural gaps. All 22 articles carry populated `source-files:` frontmatter, and the `reference-register.md` maps each source to its compiled article. ✅

## 4. Duplicate concepts

No duplicate **articles** — each of the 22 covers a distinct system or concept, so no merges are required. Several duplicate **source files** exist but are already correctly identified and status-flagged in `reference-register.md` (no action needed):

- `Sci-Hub. Indoor localization without the pain …pdf` = duplicate of `ez-mobicom.pdf` (Chintalapudi et al., MobiCom 2010).
- `Sci-Hub. A Survey of Indoor Localization Systems and Technologies …pdf` = duplicate of `A_Survey_of_Indoor_Localization_Systems_and_Techno.pdf` (Zafari et al. 2019).
- `Sci-Hub. Recent Advances in Indoor Localization …pdf` = duplicate of `077620951.pdf` (Yassin et al. 2017).
- `A_Comprehensive_Review_of_Indoor_Localization_Tech.pdf` = same paper as `applsci-15-01544-v2.pdf` (Aziz & Koo 2025) — the two are byte-identical (9,573,515 bytes each); new content was already merged into the sensor-technologies article.

## 5. Unique concepts across the base

Each article represents a distinct, non-overlapping concept node:

Foundational systems — ORL Ultrasonic, Active Badge, Cricket, Context-Aware Computing. WiFi fingerprinting lineage — RADAR, Horus, Kaemarungsi analytical model, WiFi-Fingerprinting-Advances. Zero-calibration / crowdsourcing — EZ, Zee, UnLoc, Walkie-Markie. Device-free / channel-state — E-eyes, CSI-Indoor-Localization. Inertial / field-based — Pedestrian Dead Reckoning, Magnetic-Field-Localization. Deep learning — UWB-LSTM. Surveys / cross-cutting references — Indoor-Location-Sensor-Technologies, Indoor-Localization-ML-Methods, Indoor-Localization-Applications-by-Sector, Indoor-IPS-Datasets, UWSN-Localization.

No orphan articles: every article is reachable through `_index.md` and interlinked via backlinks.

## 6. Stale sources — not updated in 30+ days (task-specific flag)

By file modification time, **all 79 `raw/` sources exceed the 30-day threshold** (oldest touched 48–49 days ago on 2026-06-14/15; three legacy PDFs retain original dates from 2001–2019). This is expected for a static source archive, so the useful signal is which stale sources are **still not compiled into `knowledge/`**:

**Genuinely unprocessed (not in `reference-register.md`, not compiled, not status-flagged) — action recommended:**

- `raw/1901_Enhancing Bluetooth Location Service_FINAL.pdf` — orphan; no register entry.
- `raw/Adaptive_Sampling_for_Fingerprinting_Localization.pdf` — orphan; no register entry. (Distinct from the already-compiled *Liu et al. 2016 — Adaptive Sampling of RF Fingerprints*.)
- `raw/TIIS Vol 13, No 1-12.pdf` — orphan; no register entry.

**Empty / corrupt file — recommend removal:**

- `raw/A Practical Approach to Landmark Deployment for Indoor Localization - Chen 2006.pdf` — **0 bytes**. The paper itself is already compiled into `[[RADAR-WiFi-Fingerprinting]]` via the working Sci-Hub copy (register Source 60), so this empty file is redundant.

**Already status-flagged (no action needed):**

- `raw/Sci-Hub. Analysis of WLAN's received signal strength …pdf` — register Source 53, flagged ⚠️ SCANNED — cannot compile.
- Other ⚠️ SCANNED / ⚠️ PARTIAL sources (Koweerawong 2013, Aomumpai 2014, *IEEE Xplore Full-Text PDF* abstract-only) remain correctly flagged.

## 7. Index integrity

`knowledge/_index.md` is **out of date**:

- Header reads *"Sources in raw/: 69"* but `raw/` currently holds **79 PDFs**.
- *"Last updated: 2026-06-15"* — no updates in ~7 weeks.

Recommend refreshing the header count and last-updated date, and adding the three unprocessed sources above to the compile queue.

## 8. Old-report cleanup (over one month old)

The task calls for deleting reports older than one month (before 2026-07-03). **Deletion could not be completed** in this automated run — the file-delete permission prompt was declined and cannot be approved without a user present. The following **7 reports are due for deletion** and should be removed manually or on the next interactive approval:

- `outputs/graph-summary-2026-06-21.md`
- `outputs/graphify-observations-2026-06-21.md`
- `outputs/health-check-2026-06-14.md`
- `outputs/health-check-2026-06-14-v2.md`
- `outputs/health-check-2026-06-14b.md`
- `outputs/health-check-2026-06-14c.md`
- `outputs/health-check-2026-06-15.md`

Kept intentionally: `reference-register.md` (persistent infrastructure, not a dated report); `health-check-2026-07-03.md` (exactly one month old, not *over*); all July weekly summaries.

## Recommended actions

1. Compile or triage the 3 unprocessed sources (Enhancing Bluetooth, Adaptive Sampling, TIIS Vol 13).
2. Delete the empty `A Practical Approach … Chen 2006.pdf` (paper already covered).
3. Update `_index.md` header: source count 69 → 79, refresh last-updated date.
4. Delete the 7 old reports listed in §8 once delete permission is available.

---
*Generated by scheduled weekly health check. Structural checks (backlinks, source references, citations) run programmatically over all 22 articles and 79 sources.*

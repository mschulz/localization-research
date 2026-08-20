---
tags: [health-check, maintenance, knowledge-base]
date: 2026-08-06
type: health-report
run: scheduled (weekly, Monday 08:00)
---

# Knowledge Base Health Check — 2026-08-06

**Scope:** `knowledge/` (22 articles + `_index.md`), `raw/` (79 PDF sources, 80 files incl. `.DS_Store`), `outputs/` (reports).
**Run type:** Automated weekly health check. No user present. Write actions were limited to (a) writing this report and (b) the authorized frontmatter check/convert — no conversion was needed (see §5). No files were deleted or renamed.

## Summary

The knowledge graph remains healthy and structurally unchanged since 2026-08-03: **0 broken backlinks, 0 truly dangling source references, 0 articles missing citations, and no JSON frontmatter to convert (all frontmatter is already YAML).** Nothing has been added to `raw/` since mid-June, so **all 79 sources trip the 30-day staleness flag** — expected for a static archive. Carried-over items still outstanding: one 0-byte source file, three genuinely unprocessed sources, and a stale `_index.md` header (says 69 sources; 79 present). One new, stricter observation this week: **10 `source-files:` paths use a cleaned filename that does not exactly match the Zotero-suffixed name on disk** — the sources are present and correctly mapped in `reference-register.md`, but the frontmatter paths won't resolve on a literal string match (§2).

## 1. Broken backlinks

None. Every `[[wikilink]]` across all 22 articles resolves to an existing article file. ✅

## 2. Articles referencing sources no longer in `raw/`

**No true dangling references** — every cited source is physically present in `raw/` and mapped in `reference-register.md`. ✅

**New observation (path hygiene, non-blocking):** 10 `source-files:` entries are stored under a *cleaned* filename that omits the Zotero export suffix (` | My Library | Zotero.pdf`) present on the actual file. A literal path lookup therefore fails even though the source exists. Affected frontmatter path → actual file on disk:

- `raw/Alhomayani and Mahoor - 2021 - Oversampling Highly Imbalanced Indoor Positioning .pdf` → `… Positioning .pdf | My Library |.pdf`
- `raw/Barsocchi et al. - 2016 - A multisource and multivariate dataset for indoor .pdf` → `… indoor .pdf | My Library | Zote.pdf`
- `raw/Toth and Tamas - 2016 - Miskolc IIS hybrid IPS Dataset for hybrid indoor .pdf` → `… indoor .pdf | My Library | Zotero.pdf`
- `raw/Papaioannou et al. - 2017 - Tracking People in Highly Dynamic Industrial Envir.pdf` → `… Envir.pdf | My Library | Zo.pdf`
- `raw/Almalioglu et al_2020_Milli-RIO.pdf` → `… Milli-RIO.pdf | My Library | Zotero.pdf`
- `raw/Xiao et al. - 2016 - A Survey on Wireless Indoor Localization from the .pdf` → `… from the .pdf | My Library | Zotero.pdf`
- `raw/Liu et al. - 2016 - Adaptive Sampling of RF Fingerprints for Fine-grai.pdf` → `… Fine-grai.pdf | My Library | Zotero.pdf`
- `raw/Tan et al. - 2020 - An Efficient Fingerprint Database Construction App.pdf` → `… App.pdf | My Library | Zotero.pdf`
- `raw/Wang et al. - 2021 - Fast Construction of the Radio Map Based on the Im.pdf` → `… Im.pdf | My Library | Zotero.pdf`
- `raw/Sci-Hub. Analysis of WLAN received signal strength …pdf` → `Sci-Hub. Analysis of WLAN's received signal strength …pdf` (apostrophe dropped in frontmatter)

Fix (recommendation only — not done automatically to avoid unreviewed edits across 5 articles): either rename the `raw/` files to drop the ` | My Library | …` suffix, or update the `source-files:` paths to match the on-disk names. Renaming the files is cleaner and keeps the frontmatter readable. Prior reports counted these as resolving because the register maps them; this run flags the literal-path mismatch explicitly.

## 3. Claims lacking source citations

No structural gaps. All 22 articles carry populated `source-files:` frontmatter, and `reference-register.md` maps each source to its compiled article. ✅

## 4. Duplicate concepts

No duplicate **articles** — each of the 22 covers a distinct system or concept; no merges required. Known duplicate **source files** remain correctly identified and status-flagged in `reference-register.md` (no action needed):

- `Sci-Hub. Indoor localization without the pain …pdf` = `ez-mobicom.pdf` (Chintalapudi et al., MobiCom 2010).
- `Sci-Hub. A Survey of Indoor Localization Systems and Technologies …pdf` = `A_Survey_of_Indoor_Localization_Systems_and_Techno.pdf` (Zafari et al. 2019).
- `Sci-Hub. Recent Advances in Indoor Localization …pdf` = `077620951.pdf` (Yassin et al. 2017).
- `A_Comprehensive_Review_of_Indoor_Localization_Tech.pdf` = `applsci-15-01544-v2.pdf` (Aziz & Koo 2025) — byte-identical (9,573,515 bytes each); already merged into the sensor-technologies article.

## 5. Frontmatter format (YAML) — task-specific check

**All 22 compiled articles use valid YAML frontmatter** delimited by `---`. **No JSON frontmatter was found, so no conversion was performed.** `_index.md` has no frontmatter, which is by design (it is the index, not a compiled article) — no change made.

## 6. Unique concepts across the base

Each article is a distinct, non-overlapping concept node:

Foundational systems — ORL Ultrasonic, Active Badge, Cricket, Context-Aware Computing. WiFi fingerprinting lineage — RADAR, Horus, Kaemarungsi analytical model, WiFi-Fingerprinting-Advances. Zero-calibration / crowdsourcing — EZ, Zee, UnLoc, Walkie-Markie. Device-free / channel-state — E-eyes, CSI-Indoor-Localization. Inertial / field-based — Pedestrian Dead Reckoning, Magnetic-Field-Localization. Deep learning — UWB-LSTM. Surveys / cross-cutting — Indoor-Location-Sensor-Technologies, Indoor-Localization-ML-Methods, Indoor-Localization-Applications-by-Sector, Indoor-IPS-Datasets, UWSN-Localization.

No orphan articles: every article is reachable through `_index.md` and interlinked via backlinks.

## 7. Stale sources — not updated in 30+ days (task-specific flag)

Cutoff: files last modified before **2026-07-07**. By modification time, **all 79 `raw/` PDF sources exceed the 30-day threshold** (bulk touched 52–53 days ago on 2026-06-14/15; three legacy PDFs retain original dates: `ORL New Location Technique tr.97.10.pdf` 2001-04-12, `1901_Enhancing Bluetooth Location Service_FINAL.pdf` 2019-02-19, `TIIS Vol 13, No 1-12.pdf` 2019-02-22). This is expected for a static source archive, so the actionable signal is which stale sources are **still not compiled** into `knowledge/`:

**Genuinely unprocessed (no register entry, not compiled, not status-flagged) — action recommended:**

- `raw/1901_Enhancing Bluetooth Location Service_FINAL.pdf` — orphan.
- `raw/Adaptive_Sampling_for_Fingerprinting_Localization.pdf` — orphan (distinct from the already-compiled *Liu et al. 2016 — Adaptive Sampling of RF Fingerprints*).
- `raw/TIIS Vol 13, No 1-12.pdf` — orphan.

**Empty / corrupt file — recommend removal:**

- `raw/A Practical Approach to Landmark Deployment for Indoor Localization - Chen 2006.pdf` — **0 bytes**. The paper is already compiled into `[[RADAR-WiFi-Fingerprinting]]` via the working Sci-Hub copy (register Source 60), so this empty file is redundant.

**Already status-flagged (no action needed):** `raw/Sci-Hub. Analysis of WLAN's received signal strength …pdf` (register Source 53, ⚠️ SCANNED) and other ⚠️ SCANNED / ⚠️ PARTIAL sources remain correctly flagged.

## 8. Index integrity

`knowledge/_index.md` is **out of date**:

- Header reads *"Sources in raw/: 69"* — `raw/` currently holds **79 PDFs**.
- *"Last updated: 2026-06-15"* — ~7.5 weeks stale.

Recommend refreshing the header count and last-updated date. (Not changed automatically — index maintenance falls under the compilation rules, not the health-check rules.)

## Recommended actions

1. Normalize the 10 mismatched `source-files:` paths in §2 — preferably by renaming the Zotero-suffixed files in `raw/`.
2. Compile or triage the 3 unprocessed sources (Enhancing Bluetooth, Adaptive Sampling, TIIS Vol 13).
3. Delete the empty `A Practical Approach … Chen 2006.pdf` (paper already covered).
4. Update `_index.md` header: source count 69 → 79, refresh last-updated date.

## Change since last run (2026-08-03)

No structural changes: same 22 articles, same 79 sources, same 0/0/0 backlink/dangling/citation status. New this week: the §2 literal-path mismatch is now called out explicitly, and the frontmatter-is-YAML check (task requirement) is confirmed passing. Note: this run executed on a Thursday rather than Monday.

---
*Generated by the scheduled weekly health check. Structural checks (backlinks, source references, citations, frontmatter format, staleness) run programmatically over all 22 articles and 80 `raw/` files. Task scope this run: health check + 30-day staleness flag + YAML frontmatter check; no deletions were requested, so none were made.*

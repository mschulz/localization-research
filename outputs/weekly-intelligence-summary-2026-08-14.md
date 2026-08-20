---
date: 2026-08-14
type: weekly-intelligence-summary
week-covered: 2026-08-08 to 2026-08-14 (since last run 2026-08-07)
new-files-in-raw: 5
files-compiled: 4
files-flagged-not-compiled: 1
new-articles: 1
articles-in-knowledge: 23
sources-catalogued: 79
raw-pdf-count: 84
frontmatter-format: YAML
---

# Weekly Intelligence Summary — 2026-08-14

## Summary

**Five new PDFs were added to `raw/` this week** — the first new source activity since the mid-June import. They were not caught by a plain modification-time check (`find -newermt 2026-08-08` returns nothing) because each file carries its **original download mtime (July)** preserved through the copy into the vault. They were instead detected by reconciling counts: `raw/` grew from 79 to **84 PDFs** since the 2026-08-07 run, and none of the five appears in `outputs/reference-register.md` or any article's `source-files`. This corrects a miss in the two prior runs (2026-08-06 and 2026-08-07), which reported "no new files" while these were already present.

Four are peer-reviewed papers and were **compiled**. The fifth is an **LLM-generated review article** and was **cataloged but not compiled** (see Frontmatter/Provenance note).

## New Files This Week

| File | What it is | Action |
|---|---|---|
| `raw/tabibiazar2011.pdf` | Tabibiazar & Basir, IEEE SMC 2011 — *Compressive Sensing Indoor Localization* | ✅ Compiled → new article |
| `raw/balestrieri2020.pdf` | Balestrieri et al., IEEE TIM 2020 — *Compressive Sampling for RF Emitter Localization* | ✅ Compiled → new article |
| `raw/2107.01192v1.pdf` | Wang, Tiku & Pasricha, arXiv 2021 — *CHISEL* compression-aware DL WiFi fingerprinting | ✅ Compiled → existing article |
| `raw/s41598-024-75306-3.pdf` | Yang et al., Scientific Reports 2024 — *FDP* fingerprint dictionary preprocessing (CDL) | ✅ Compiled → existing article |
| `raw/I am researching localisation techniques...Generate a review article..pdf` | LLM-generated 25-page review; no primary citations | ⚠️ Cataloged, not compiled |

## Compilation

**New article created:** [[Compressive-Sensing-Localization]] — the dedicated home for compressive sensing / compressive sampling as a localization primitive. Covers Tabibiazar & Basir 2011 (WSN localization recast as ℓ₁ sparse recovery over a spatial grid; dictionary coherence; M ≥ O(K·log(N/K))) and Balestrieri et al. 2020 (sub-Nyquist Non-Uniform Sampling wideband spectrum sensors for RF-emitter localization; ~1 m accuracy with 10 receivers; RMSE near the CRLB up to compression ratio 64). Full entity summary (Hardware/Software/Algorithm/Accuracy/Evaluation/Metrics/Baseline) and backlinks to [[WiFi-Fingerprinting-Advances]], [[Indoor-Localization-ML-Methods]], [[UWSN-Localization]], [[Indoor-Location-Sensor-Technologies]].

**Existing article updated:** [[WiFi-Fingerprinting-Advances]] — added two subsections:
- **CHISEL** (Wang, Tiku & Pasricha 2021): Convolutional Autoencoder + CNN, compression-aware; 24×24 RSSI "images"; 171,209 params / ~801 KB shrunk to ~148 KB via QAT + pruning (81% reduction); UJIIndoorLoc ~100% building/floor, ~6.95 m mean error; ~2.63 m (38%) over SAE / 1D-CNN baselines. Directly answers the article's Heterogeneous-Devices / embedded-deployability challenge.
- **FDP** (Yang et al. 2024): Convolutional Dictionary Learning for radio-map preprocessing; ~28% data compression; 41–48% accuracy improvement; strengthens the Radio-Map-Construction and Sparsity-Based-Localization themes.

Frontmatter `source-files` for `WiFi-Fingerprinting-Advances.md` extended with both new PDFs; tags, references table, and relationships updated. A cross-link to CHISEL/CS was also added to [[Indoor-Localization-ML-Methods]].

**Index & register:** `knowledge/_index.md` updated (new article row, revised WiFi-Fingerprinting summary, 5 new source-file rows, counts 22→23 articles / 84 PDFs, date). `outputs/reference-register.md` extended with Sources 75–79, each with a link to the file in `raw/` and a compiled ✅ / flagged ⚠️ status (CLAUDE.md rules 10–11).

## Emerging Theme

Three of the four compiled papers converge on **sparsity/compression** at different stages of the localization pipeline: sparse *position* recovery (Tabibiazar), sub-Nyquist *signal* acquisition (Balestrieri), sparse *dictionary* radio-map coding + database compression (FDP), and *model* compression for embedded inference (CHISEL). This is a coherent new cluster in the knowledge base linking fingerprinting, ML methods, and the new compressive-sensing article — relevant to on-device/IoT localization on microcontroller-class hardware.

## Frontmatter Check (YAML)

All source material this week was PDF (no JSON metadata to convert). All 23 articles in `knowledge/` were scanned for JSON-style frontmatter (a leading `{` in place of a `---` block): **none found — every article uses valid YAML frontmatter** (`tags`, `date-compiled`, `source-files`, `status`). The new article and this summary both use YAML frontmatter. No conversion was necessary.

## Provenance Note — LLM-Generated Review (not compiled)

`raw/I am researching localisation techniques...Generate a review article..pdf` is a machine-generated survey (its filename is the generation prompt) with no verifiable primary citations. Per CLAUDE.md (cite specific source files for every factual claim; flag contradictions explicitly), unverifiable synthetic content was **not merged** into knowledge articles, to avoid introducing uncited claims. It is cataloged as Source 79 for provenance; its topics are already covered by primary sources in the three articles noted above. Recommend the user confirm this is the intended handling.

## Knowledge Base Status

- **23 articles** in `knowledge/` (was 22); newest: [[Compressive-Sensing-Localization]] (2026-08-14)
- **79 sources catalogued** in `outputs/reference-register.md` (was 74) — 71 compiled ✅
- **84 PDFs in `raw/`** (83 content-bearing + the 0-byte Chen 2006 file, excluded)
- Last health check: 2026-08-10 — see `outputs/health-check-2026-08-10.md`

## Outstanding Items (carried over)

1. Correct the description of the 0-byte Chen 2006 file in `reference-register.md` / `_index.md` to "empty/broken download."
2. Source a full-text copy of Liu et al. (IEEE Access 2023) — currently abstract-only in `IEEE Xplore Full-Text PDF.pdf`.
3. OCR the scanned Sci-Hub PDFs flagged non-compilable (WLAN RSSI fingerprinting analysis, adaptive RSS fingerprinting database, optimal reference-node placement).
4. Confirm intended handling of the LLM-generated review (Source 79) — currently cataloged but not compiled.
5. Consider a report-retention policy: the two 2026-07-04 summaries are now >1 month old.

## Recommended Actions

Backlog cleared for this cycle — the four academic sources are compiled and cross-linked. A health check is due (last was 2026-08-10); running one would verify the new backlinks to [[Compressive-Sensing-Localization]] resolve across the vault.

---
*Generated automatically by the weekly-file-check scheduled task.*

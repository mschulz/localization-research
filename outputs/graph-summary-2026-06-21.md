---
title: Knowledge Graph Summary — Post-Update
date: 2026-06-21
status: complete
graph-stats:
  nodes: 349
  edges: 490
  communities: 27
  prior-nodes: 299
  prior-edges: 405
  prior-communities: 29
---

# Knowledge Graph Summary — 2026-06-21

## Graph Statistics

| Metric | Before Update | After Update | Delta |
|--------|--------------|-------------|-------|
| Nodes | 299 | 349 | +50 |
| Edges | 405 | 490 | +85 |
| Communities | 29 | 27 | −2 |

Update triggered by: CSI backlink fix in `knowledge/CSI-Indoor-Localization.md` (added INFOCOM 2012 preprint to `source-files` frontmatter) and re-extraction of `outputs/graphify-observations-2026-06-21.md`.

---

## God Nodes (most connected)

| Rank | Node | Edges |
|------|------|-------|
| 1 | Indoor Localization ML Methods | 30 |
| 2 | WiFi Fingerprinting Advances and Deployment Challenges | 26 |
| 3 | Indoor Location Sensor Technologies Survey | 25 |
| 4 | RADAR WiFi RSSI Fingerprinting | 23 |
| 5 | UnLoc Unsupervised Indoor Localization | 21 |
| 6 | Horus WLAN Location Determination System | 18 |
| 7 | Pedestrian Dead Reckoning (Woodman PhD) | 17 |
| 8 | Indoor IPS Public Datasets | 16 |
| 9 | ORL Ultrasonic Location System | 15 |
| 10 | Reference Register | 15 |

---

## Communities (27 total)

| ID | Label | Size | Cohesion | Ext. Edges |
|----|-------|------|----------|------------|
| C0 | Fingerprinting Foundations & History | 75 | 0.05 | 34 |
| C1 | Crowdsourced & Collaborative Localization | 44 | 0.07 | 4 |
| C2 | Core RSSI Fingerprinting Methods | 39 | 0.07 | 1 |
| C3 | Knowledge Graph Meta-Analysis | 26 | 0.12 | 29 |
| C4 | CSI Localization Knowledge Article | 26 | 0.10 | 0 |
| C5 | FILA CSI System (TPDS 2013) | 19 | 0.11 | 0 |
| C6 | mmWave Radar Dead Reckoning | 15 | 0.14 | 0 |
| C7 | Motion-Assisted Collaborative Positioning | 15 | 0.13 | 0 |
| C8 | Cricket Ultrasound System | 9 | 0.22 | 0 |
| C9 | Bayesian & Neural Network Methods | 9 | 0.22 | 1 |
| C10 | Matrix Completion for Radio Maps | 8 | 0.29 | 0 |
| C11 | CSI Activity Recognition (E-eyes) | 7 | 0.29 | 1 |
| C12 | FILA Preprint (INFOCOM 2012) | 7 | 0.33 | 0 |
| C13 | Adaptive RSS Database Methods | 7 | 0.29 | 0 |
| C14 | Genetic Algorithm Node Placement | 7 | 0.29 | 0 |
| C15 | Bluetooth Indoor Positioning | 7 | 0.29 | 0 |
| C16 | RFID Localization & Surveys | 6 | 0.33 | 0 |
| C17 | Geomagnetic Fingerprinting Datasets | 4 | 0.50 | 0 |
| C18 | Zero-Configuration EZ Localization | 4 | 0.50 | 0 |
| C19 | Benchmark Competitions & Datasets | 4 | 0.50 | 0 |
| C20 | LANDMARC RFID System | 3 | 1.00 | 0 |
| C21 | AP Selection by RSS Variance | 2 | 1.00 | 0 |
| C22 | Evaluation Methodology | 2 | 1.00 | 0 |
| C23 | BLE Multipath Diversity | 1 | 1.00 | 0 |
| C24 | Radio Map Tensor Completion | 1 | 1.00 | 0 |
| C25 | Particle Filter Localization | 1 | 1.00 | 0 |
| C26 | Bluetooth Enhancement 2019 | 1 | 1.00 | 0 |

---

## Surprising Connections

- **`Chen et al. Landmark Deployment` ↔ `Genetic Algorithm Node Placement`** [INFERRED, semantically similar]  
  Two independent papers solving the same AP/landmark placement problem from different traditions (SECON 2006 empirical vs. genetic optimisation).

- **`VAE/CVAE Oversampling` ↔ `Crowdsourced Radio Map Collection`** [INFERRED]  
  Oversampling for BLE fingerprint imbalance connects to crowdsourced map construction — both address data sparsity in radio maps.

- **`Class Imbalance in Fingerprinting Datasets` → `VAE/CVAE Oversampling`** [INFERRED]  
  Problem statement in `knowledge/Indoor-IPS-Datasets.md` connects to solution in raw BLE paper.

- **`Milli-RIO mmWave+IMU` ↔ `Pedestrian Dead Reckoning (Woodman)`** [INFERRED, semantically similar]  
  Extracted from `outputs/graphify-observations-2026-06-21.md` — parallel independent development of radar vs. IMU dead reckoning, never citing each other.

---

## Suggested Questions

1. **Why does `Indoor Localization ML Methods` bridge Fingerprinting Foundations → Knowledge Graph Meta-Analysis → CSI Activity Recognition?**  
   _(betweenness centrality 0.053 — highest cross-community connector after the observations file)_

2. **Why does `Knowledge Graph Observations` itself connect Fingerprinting Foundations to Crowdsourced Localization and Knowledge Graph Meta-Analysis?**  
   _(betweenness 0.073 — the observations document is now the #1 bridge node in the graph)_

3. **Are the 8 inferred edges around `Indoor Localization ML Methods` (e.g. with VAE/CVAE and Bluetooth Altini 2010) actually correct?**

4. **Are the 6 inferred edges around `Indoor Location Sensor Technologies Survey` (with 3D Survey Sesyuk 2022 and BIPS) correct?**

5. **Are the 4 inferred edges around `RADAR WiFi RSSI Fingerprinting` (with AP Landmark Placement Theory and NNSS) correct?**

---

## Structural Notes

### Emergent Community: Knowledge Graph Meta-Analysis (C3)
The observations file `outputs/graphify-observations-2026-06-21.md` was re-extracted in this update and formed its own community (C3, 26 nodes, 29 external edges). Concept nodes extracted from it — e.g. *"Rejection Chain: Active Badge to Crowdsourcing"*, *"Community 5 mmWave Radar Island"*, *"Infrastructure-Free Dead Reckoning Systems"* — are now the second-highest cross-community connectors in the graph. The analysis document is itself acting as a bridge node.

### CSI Three-Way Split Persists
Communities C4, C5, and C12 all have 0 external edges despite covering the same FILA paper:
- **C4** (CSI Localization Knowledge Article) — nodes extracted from `knowledge/CSI-Indoor-Localization.md`
- **C5** (FILA TPDS 2013 Journal) — nodes extracted from the Sci-Hub journal PDF
- **C12** (FILA INFOCOM 2012 Preprint) — nodes extracted from the conference preprint PDF

The `source-files` backlink fix created new pointer nodes in C4 but did not draw edges to the existing PDF-extracted node IDs in C5/C12. A dedicated `knowledge/FILA-Paper-Versions.md` synthesis article using `[[double bracket links]]` to the existing concept nodes would close this gap.

### Isolated Literature Islands
- **C6 mmWave Radar Dead Reckoning** (Milli-RIO, 15 nodes, 0 external edges): cites only FMCW/IMU literature; connection to dead reckoning tradition is INFERRED from observations file only.
- **C12 FILA Preprint** (7 nodes, 0 external edges): conference preprint cluster remains structurally isolated from the journal version in C5.

### Isolated Nodes
126 nodes have ≤1 connection. Common causes: single-paper entities with no cross-references in compiled knowledge articles, or entities unique to one raw PDF with no backlink to a knowledge article.

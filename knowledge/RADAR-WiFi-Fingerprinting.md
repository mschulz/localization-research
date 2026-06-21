---
tags: [WiFi, RSSI, fingerprinting, indoor-localization, RADAR, empirical-method, signal-propagation, landmark-placement, RSSI-reliability]
date-compiled: 2026-06-15
source-files:
  - "raw/infocom2000.pdf"
  - "raw/Kaemarungsi 2004.pdf"
  - "raw/p-0012--DESIGN_OF_INDOOR_POSITIONING_SYSTEMS.pdf"
  - "raw/Sci-Hub. A Practical Approach to Landmark Deployment for Indoor Localization : 2006 3rd Annual IEEE .pdf"
  - "raw/Sci-Hub. Evaluation of the reliability of RSSI for indoor localization : 2012 International Conferen.pdf"
status: active
---

# RADAR — WiFi RSSI Fingerprinting

## Summary

RADAR is an RF-based system for locating and tracking users inside buildings, developed by Paramvir Bahl and Venkata N. Padmanabhan at Microsoft Research (published at IEEE INFOCOM 2000). It is the foundational paper establishing WiFi RSSI fingerprinting as a practical indoor positioning technique. RADAR operates by recording signal strength at multiple fixed base stations and using nearest-neighbour matching against an empirically-built radio map to estimate user position. Median accuracy: 2–3 metres. All modern WiFi fingerprinting systems descend conceptually from RADAR.

## Motivation

Prior indoor location systems (Active Badge, ParcTab) used IR with room-level granularity only. Radio-frequency wireless LANs, already being deployed at scale for data networking, offered a richer signal that varies continuously with position. The key insight: RSSI at each base station changes monotonically with distance, and the combination of RSSI values from multiple stations forms a location "fingerprint" unique to each position.

## System Architecture

**Testbed**: 3-storey office building, second floor — 43.5m × 22.5m (980 m²), 50+ rooms. Three base stations (BS1, BS2, BS3) running FreeBSD 3.0 with WaveLAN 802.11 NICs (2.4GHz, 2 Mbps). Mobile host: laptop running Windows 95 with matching NIC.

**Two operational phases:**

1. **Offline (training) phase**: Mobile user walks the floor indicating their location by clicking a floor map; base stations record RSSI + timestamp tuples (t, bs, ss). Data collected at 70 distinct locations × 4 orientations = 280 (x, y, d) tuples, ≥20 samples each.
2. **Online (real-time) phase**: Base stations continuously measure RSSI from the mobile host; system estimates position by matching the live RSSI vector against the offline radio map.

## Algorithm: Nearest Neighbours in Signal Space (NNSS)

The core technique: given a live RSSI vector (ss1, ss2, ss3) from the three base stations, find the radio map entry with minimum Euclidean distance in signal space:

> argmin √((ss1−ss'1)² + (ss2−ss'2)² + (ss3−ss'3)²)

Two variants for constructing the radio map:

### Empirical Method
Uses the actual measured RSSI data from the offline survey. Accuracy: **median ~2.94m**, 25th percentile ~1.92m, 75th percentile ~4.69m.

Key findings from sensitivity analysis:
- **k-nearest neighbours (k>1)**: Marginal improvement for k=5 (9% better at 50th percentile); large k degrades accuracy
- **Orientation matters**: RSSI can vary by up to 5 dBm depending on which direction the user faces (body acts as obstruction). Offline data should include all 4 orientations
- **Survey density**: Performance almost as good with 40 physical points as 70; diminishing returns below 40
- **Real-time samples**: 3 samples sufficient; 1 sample gives ~30% worse median accuracy
- **Mobile tracking**: Sliding window of 10 samples; median tracking error ~3.5m (19% worse than stationary)

### Radio Propagation (WAF) Model
Avoids labour-intensive site survey by computing RSSI theoretically using the Wall Attenuation Factor (WAF) model:

> P(d)[dBm] = P(d₀)[dBm] − 10n·log(d/d₀) − nW·WAF  (when nW < C)

Parameters (empirically fitted via linear regression for the testbed): n=1.52, WAF=3.1 dBm, C=4 walls. Accuracy: median ~4.3m — worse than empirical but far better than strongest-BS selection (8.16m). Enables deployment without site survey by using a floor plan to compute wall counts.

## Key Observations and Limitations

- RSSI is the strongest function of location (SNR is noisier; discarded)
- User body orientation introduces systematic error (not random), making it the main accuracy-limiting factor
- Multipath causes RSSI at a given location to not map cleanly to distance — motivating fingerprinting over pure trilateration
- The empirical method needs resurveying when base stations move or the building layout changes significantly
- Propagation model avoids resurveying but trades ~1.4m accuracy vs. empirical method

## Accuracy

| Method | 25th percentile | Median (50th) | 75th percentile |
|---|---|---|---|
| RADAR empirical | 1.92m | 2.94m | 4.69m |
| RADAR WAF model | ~1.86m | ~4.3m | — |
| Strongest BS (baseline) | 4.54m | 8.16m | 11.5m |
| Random (baseline) | 10.37m | 16.26m | 25.63m |

## Entity Summary

### Hardware
- **Medium**: 2.4GHz ISM-band RF (IEEE 802.11 WaveLAN)
- **Base stations**: Standard Pentium PCs with Lucent WaveLAN NICs; no specialised hardware; 3 units in testbed
- **Mobile host**: Laptop with matching WaveLAN NIC; broadcasts UDP beacon packets at 4Hz
- **No additional infrastructure beyond existing WiFi LAN**

### Software
- **Data collection tool**: Custom Tcl/Tk + Perl app; controls collection, synchronises clocks, records (t, x, y, d) tuples
- **NNSS search**: Simple linear-time nearest-neighbour search (small dataset didn't warrant R-tree/X-tree)
- **Wall count**: Cohen-Sutherland line-clipping algorithm on floor plan to count intervening walls for WAF model
- **Availability**: Research prototype; described in academic paper; no open-source release; Microsoft Research internal

### Algorithm
- **Fingerprinting**: Offline RSSI survey at known (x,y,d) positions → radio map database
- **Matching**: Euclidean nearest-neighbour search in signal space (NNSS) — deterministic, uses mean SS per station
- **k-NN extension**: Average coordinates of k nearest signal-space neighbours for marginal improvement
- **WAF propagation model**: Path loss exponent (n) + wall attenuation factor; linear regression to fit parameters from empirical data; Cohen-Sutherland for wall counting
- **Tracking**: Sliding time window of RSSI samples; reduces to sequence of stationary location problems

### Accuracy
- **Empirical method median**: ~2.94m; 90th percentile: ~5m
- **WAF model median**: ~4.3m
- **Update rate**: 4 Hz (4 beacon packets/second from mobile)
- **Coverage**: 980 m², 3 base stations sufficient; scales with more stations
- **Comparison**: RADAR empirical is 2.8× better than strongest-BS at median; 89% worse than [[Horus-WLAN-System]] (probabilistic) on comparable testbeds

## Analytical Framework for RADAR-Style Systems (Kaemarungsi & Krishnamurthy)

Kaemarungsi, K. and Krishnamurthy, P. (University of Pittsburgh), IEEE INFOCOM 2004 and PhD Thesis (Pitt, 2005). Sources: `raw/Kaemarungsi 2004.pdf` and `raw/p-0012--DESIGN_OF_INDOOR_POSITIONING_SYSTEMS.pdf`.

RADAR established the NNSS algorithm empirically. Kaemarungsi & Krishnamurthy provided the first analytical theory of why and how NNSS works — see [[Kaemarungsi-WiFi-Fingerprinting]] for the full treatment. Key results directly relevant to RADAR:

**Signal model**: RSS in dBm is approximately Gaussian with mean rᵢ (true fingerprint value) and standard deviation σ. The Euclidean distance Z used by RADAR satisfies: Z² ~ central chi-squared distribution with N degrees of freedom (N = number of APs) when evaluated at the true location. This explains why RADAR's performance improves with more APs.

**Design insights derived from RADAR's empirical findings**:
- RADAR used N=3 APs — the minimum for useful fingerprinting; the analytical model shows Pc (probability of correct location) increases substantially from N=3 to N=8 for σ≈2–3 dBm (typical indoor WiFi)
- RADAR's 2.94m median error is consistent with the analytical prediction for N=3, σ≈3 dBm, 1m grid spacing at University of Pittsburgh office building
- RADAR's finding that k=5 gives marginal improvement over k=1 aligns with the KNN analysis in the thesis: beyond k≈5, averaging pulls locations toward building centre

**Path-loss parameter analysis**: The WAF model RADAR used (n=1.52, WAF=3.1 dBm) corresponds to relatively steep indoor attenuation, which the analytical model predicts should give moderately good discrimination between fingerprints — consistent with RADAR's ~2.94m median outperforming the strongest-BS baseline (8.16m) by a factor of 2.8×.

## AP / Landmark Placement Theory (Chen et al., IEEE SECON 2006)

Chen, Y., Francisco, J.-A., Trappe, W., Martin, R.P. *A Practical Approach to Landmark Deployment for Indoor Localization.* 3rd Annual IEEE Communications Society Conference on Sensor and Ad Hoc Communications and Networks (SECON), 2006. Rutgers University. Source: `raw/Sci-Hub. A Practical Approach to Landmark Deployment for Indoor Localization : 2006 3rd Annual IEEE .pdf`; full 10-page paper verified at winlab.rutgers.edu.

RADAR established that AP (landmark) placement affects localization performance — the original RADAR testbed used a collinear/coverage-optimised deployment that was not optimal for localization accuracy. Chen et al. provide the first theoretical framework for finding optimal AP deployments.

**Core analysis — LLS upper bound**: The Linear Least Squares (LLS) variant of LS linearises the multilateration problem into Ax = b form, where A is determined solely by AP coordinates. The location error is bounded by:

> ‖x − x̃‖ ≤ ‖A⁺‖ · ‖e‖

where A⁺ is the Moore-Penrose pseudoinverse and ‖A⁺‖ = 1/γ₂ (γ₂ = smallest singular value of A). Minimising this bound requires simultaneously: (1) maximising γ₂ (geometry term) and (2) minimising ‖e‖ (distance estimation error term).

**maxL-minE algorithm**: An iterative algorithm that:
1. Starts with the optimal geometric pattern fitted to the maximum floor size
2. Reduces the pattern size iteratively, computing localization error at each step
3. Converges quickly (typically within a small number of iterations) to the deployment that minimises max error for the given floor dimensions and AP count

**Optimal geometric patterns** (from eigenvalue analysis of AᵀA):
- N=3 landmarks: equilateral triangle
- N=4: square (plus central landmark for N=5)
- N=6: two enclosing equilateral triangles
- N≥6: simple shapes enclosing one another, **not** hexagons (hexagons fail the λ₁ ≈ λ₂ condition)

**Experimental results** (802.11 WiFi + 802.15.4 ZigBee, CoRE building 200×80ft floor, 4 landmarks, 3 algorithms):

| Algorithm | Collinear error | Square (optimised) error | Improvement |
|---|---|---|---|
| LLS (bounded) | 38.56 ft | 24.73 ft | **35%** |
| NLS (bounded) | 20.23 ft | 15.37 ft | **24%** |
| RADAR (scene-matching) | 12.49 ft | 9.31 ft | **25%** |
| ABP (Bayesian area) | 13.11 ft | 10.09 ft | **23%** |
| BN (Bayesian network) | 22.25 ft | 20.16 ft | **9%** |

The same improvement holds for 802.15.4 ZigBee and for TOA-based ranging (~30% improvement over horizontal deployment).

**Hölder parameter**: The optimised deployment also reduces the Hölder parameter (max sensitivity to signal perturbations). Lower Hölder values mean the algorithm is less affected by noise, multipath, and measurement bias — confirming the placement benefit is not specific to noise-free conditions.

**Key tension**: Networks of APs are typically deployed to maximise signal **coverage** (collinear/horizontal placement along longest building dimension). This directly conflicts with the optimal placement for localization **accuracy** (symmetric, distributed geometric pattern). Systems using existing coverage-optimised AP deployments (like the original RADAR testbed) carry a systematic accuracy penalty that the maxL-minE algorithm can quantify and reduce.

**4-landmark rule-of-thumb**: Optimally placed 4 or 6 landmarks achieve accuracy comparable to 20 randomly distributed landmarks — significantly reducing infrastructure cost without sacrificing localization performance.

---

## RSSI Reliability for Mobile Indoor Localization (Dong & Dargie, ICWCUCA 2012)

Dong, Q. and Dargie, W. *Evaluation of the Reliability of RSSI for Indoor Localization.* International Conference on Wireless Communications in Unusual and Confined Areas (ICWCUCA), 2012. Technical University of Dresden. Source: `raw/Sci-Hub. Evaluation of the reliability of RSSI for indoor localization : 2012 International Conferen.pdf`; full 6-page paper verified at rn.inf.tu-dresden.de.

While RADAR treats RSSI fingerprinting as reliable when measured at known locations in static offline surveys, Dong & Dargie investigate whether RSSI can be used **during mobile node motion** for real-time localization in wireless sensor networks — finding it fundamentally unreliable.

**Hardware**: SunSPOT motes (Sun Microsystems) with CC2420 (Chipcon) 802.15.4/ZigBee radio at 2.4GHz. One node as base station (USB-to-laptop), one node worn on user's ankle. Corridor test environment: 27.5m straight, glass wall one side + concrete wall other side, LOS conditions with no additional obstacles.

**Reference curve establishment**: Static measurements at 1.6m intervals (10s each, averaged). Two curve-fitting approaches produce slightly different path loss exponents (n=4.2 vs n=1.7 depending on fitting method), illustrating the fundamental ambiguity of static RSSI-to-distance calibration.

**Dynamic reliability test — 4 estimation techniques applied to mobile RSSI**:

| Method | Result |
|---|---|
| **Raw data** | RSSI = -90 dBm maps simultaneously to 7m AND 26m; completely unusable |
| **Moving average** (100-sample window, 1s) | Smooth curve but still far from reference; large absolute distance errors |
| **Weighted average** | Better trajectory shape but each sample still far from true position |
| **Curve fitting** | Converges to reference only at >6m distance; diverges badly at short range |

**Conclusion**: None of the four estimation methods produce RSSI-to-distance mappings that reliably track actual node position during mobility. The authors conclude RSSI is unreliable as the *only* input for indoor sensor localization. This is consistent with RADAR's own observation that multipath causes RSSI to not map cleanly to distance — motivating fingerprinting over pure trilateration.

**Significance**: Dong & Dargie provide experimental validation for why pure RSSI-to-distance trilateration fails: real-world mobile RSSI is simply too noisy to invert to distance. This motivates fingerprinting (RADAR), probabilistic methods ([[Horus-WLAN-System]]), CSI-based approaches ([[CSI-Indoor-Localization]]), and infrastructure-free alternatives (Ubicarse) that do not assume a clean RSSI-to-distance mapping.

---

## References

| # | Reference | Priority | In raw/ |
|---|---|---|---|
| [Wan92] | Want, R., Hopper, A. et al. *The Active Badge Location System.* ACM TOIS, Jan 1992. | ⭐ High — predecessor system | — |
| [Har94] | Harter, A., Hopper, A. *A Distributed Location System for the Active Office.* IEEE Network, Jan 1994. | High | — |
| [Sei92] | Seidel, S., Rapport, T. *914 MHz path loss prediction model for indoor wireless communications.* IEEE Trans. Antennas, 1992. | Medium — WAF model source | — |
| [Rap96] | Rapport, T. *Wireless Communications — Principles and Practice.* IEEE Press, 1996. | Medium — propagation reference | — |
| [Hod97] | Hodes, T. et al. *Composable Ad Hoc Mobile Services for Universal Interaction.* MobiCom 1997. | Medium — baseline comparison | — |
| [Kae04a] | Kaemarungsi, K. & Krishnamurthy, P. *Modeling of Indoor Positioning Systems Based on Location Fingerprinting.* IEEE INFOCOM 2004. | ⭐ High — first analytical model for NNSS | ✅ `raw/Kaemarungsi 2004.pdf` |
| [Kae05] | Kaemarungsi, K. *Design of Indoor Positioning Systems Based on Location Fingerprinting Technique.* PhD Thesis, University of Pittsburgh, 2005. 193 pages. | ⭐ High — full design framework | ✅ `raw/p-0012--DESIGN_OF_INDOOR_POSITIONING_SYSTEMS.pdf` |
| [KaeOld] | Kaemarungsi, K. PhD Thesis (Zotero snapshot). ⚠️ 1-page Zotero title-page placeholder only — not the full thesis | — | `raw/fingerprint thesis.pdf` (do not use) |

## Relationships

- [[Horus-WLAN-System]] — probabilistic successor to RADAR; achieves ~0.5m median vs. RADAR's ~2.94m; Dong & Dargie's RSSI unreliability findings explain why probabilistic models outperform deterministic trilateration
- [[Kaemarungsi-WiFi-Fingerprinting]] — provides the analytical theory underpinning RADAR's NNSS algorithm; explains why N APs, σ, and grid spacing determine accuracy
- [[CSI-Indoor-Localization]] — FILA (Wu et al. 2013) directly addresses RADAR's RSSI limitations with CSI; same NNSS concept extended to per-subcarrier fingerprinting
- [[WiFi-Fingerprinting-Advances]] — surveys of advances built on RADAR's foundation
- [[Indoor-Location-Sensor-Technologies]] — WiFi as an indoor positioning medium; RFID/LANDMARC (Jin 2006) and BIPS Bluetooth (Bruno 2003) covered there as complementary positioning technologies
- [[Indoor-Localization-ML-Methods]] — modern ML extensions of RADAR's fingerprinting concept (DNN, SVM, KNN); Seco 2009 mathematical taxonomy of methods is foundational
- [[Indoor-IPS-Datasets]] — CRAWDAD and other public datasets used for benchmarking RADAR-style systems
- [[Active-Badge]] — predecessor IR-based system that motivated RF-based approach
- [[Context-Aware-Computing]] — application domain motivating indoor location
- [[EZ-Indoor-Localization]] — zero-calibration approach to WiFi localization; Ubicarse (Kumar et al. 2014) achieves 39cm median without any fingerprinting using SAR

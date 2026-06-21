---
tags: [WiFi, fingerprinting, RSSI, indoor-localization, analytical-model, signal-propagation, WLAN, path-loss, nearest-neighbor, probabilistic, BLE, multi-floor, multipath]
date-compiled: 2026-06-15
source-files:
  - "raw/Kaemarungsi 2004.pdf"
  - "raw/p-0012--DESIGN_OF_INDOOR_POSITIONING_SYSTEMS.pdf"
  - "raw/01331706.pdf"
  - "raw/distribution-of-wlan-received-signal-strength-indication-for-ind.pdf"
  - "raw/maneerat2014.pdf"
  - "raw/wisanmongkol2019.pdf"
status: active
---

# Kaemarungsi & Krishnamurthy — Analytical Framework for WiFi Fingerprinting

## Summary

Kamol Kaemarungsi and Prashant Krishnamurthy (University of Pittsburgh, Telecommunications Program) developed the first analytical framework for designing and evaluating indoor positioning systems based on WiFi location fingerprinting. Their work spans from INFOCOM 2004 (analytical model) through a full PhD thesis (University of Pittsburgh, 2005) and several follow-on papers on RSS properties and system design. Prior to this work, fingerprinting systems such as [[RADAR-WiFi-Fingerprinting]] and [[Horus-WLAN-System]] reported empirical results but lacked a mathematical model enabling principled system design. Kaemarungsi & Krishnamurthy's framework allows a system designer to predict accuracy and precision from first principles, selecting grid spacing, number of access points, and path-loss parameters before deployment.

## Core Contribution: Analytical Model for Fingerprinting (INFOCOM 2004)

Source: `raw/Kaemarungsi 2004.pdf` — "Modeling of Indoor Positioning Systems Based on Location Fingerprinting," IEEE INFOCOM 2004.

### Problem Statement

By 2004, multiple fingerprinting systems (RADAR, Horus, LANDMARC) existed but all lacked:
- A theoretical basis to *predict* performance before deployment
- Guidance on how many APs are needed for a target accuracy
- Understanding of how grid spacing and path-loss parameters affect precision
- A model for the tradeoff between granularity (small grid spacing) and discriminability (fingerprints too similar at small separations)

### Signal Model

Consider a fingerprinting system with N access points visible throughout an area, and an L×L grid of location fingerprints (L² total locations). A **location fingerprint** for position (x, y) is the vector of true mean RSS values from all N APs:

R̃ = [r₁, r₂, r₃, ..., rₙ]

The **sample RSS vector** measured at the mobile station is:

R = [ρ₁, ρ₂, ρ₃, ..., ρₙ]

Each ρᵢ is modelled as an independent Gaussian random variable (RSS in dBm is lognormally distributed in linear scale, hence Gaussian in dBm), with:
- Mean E{ρᵢ} = rᵢ (the true fingerprint value)
- Standard deviation σ (identical across all APs — simplifying assumption)

This Gaussian RSS assumption is supported by 802.11 measurements showing standard deviation of ~2.13 dBm over 5-hour to one-month windows with minimal time-of-day variation.

### Euclidean Distance Metric

The NNSS (Nearest Neighbor in Signal Space) algorithm — used by RADAR — computes the Euclidean distance between the sample vector and each stored fingerprint:

Z = sqrt(Σᵢ (ρᵢ − rᵢ)²) = sqrt(Σᵢ qᵢ²)

This distance metric can be of two types:

**Type 1 — True location distance**: When comparing R to the fingerprint of the MS's actual location, each qᵢ = ρᵢ − rᵢ is a zero-mean Gaussian. X = Z² follows a **central chi-squared distribution** with N degrees of freedom (χ²_N), scaled by σ²:

p(x) = (1 / (σᴺ · 2^(N/2) · Γ(N/2))) · e^(-x/(2σ²)) · x^(N/2 − 1)

**Type 2 — Wrong location distance**: When comparing R to a fingerprint from a different location, each qᵢ has a non-zero mean (the difference between the two locations' fingerprints). X = Z² follows a **non-central chi-squared distribution**.

### Key Analytical Results

The probability of correctly selecting the true location (Pc) depends on:

1. **Number of access points N**: More APs → sharper chi-squared distribution → better discrimination between correct and incorrect fingerprints. Increasing N from 3 to 8 significantly raises Pc for the same σ.

2. **RSS standard deviation σ**: Higher σ (more temporal variation in RSS) → broader distribution → lower Pc. Reducing σ (via averaging more samples) improves performance.

3. **Path-loss exponent**: Steeper path loss → larger separation between fingerprints of different locations → easier discrimination. Buildings with strong attenuation paradoxically yield better fingerprinting performance.

4. **Grid spacing**: Finer grid → fingerprints of adjacent points become similar → lower discrimination probability. There is a minimum effective grid spacing determined by σ and path-loss parameters.

The model extends from the two-location case to the L²-location case via an approximation treating the multi-location probability as a product of pairwise comparisons.

### Design Guidelines

The framework yields concrete recommendations:
- Choose the minimum number of APs such that N degrees of freedom in the chi-squared distribution provides adequate discrimination for the target accuracy
- Grid spacing should be no smaller than the "fingerprint separation threshold" determined by σ and path-loss exponent
- Collect sufficient RSS samples to reduce σ — averaging 100+ samples reduces σ substantially
- The model predicts ~2–3m accuracy is achievable with N=3–4 APs and σ≈2–3 dBm in a typical office building

## PhD Thesis: Design of Indoor Positioning Systems (2005)

Source: `raw/p-0012--DESIGN_OF_INDOOR_POSITIONING_SYSTEMS.pdf` — Kaemarungsi, K. PhD thesis, University of Pittsburgh, 2005. 193 pages.

The dissertation extends the INFOCOM 2004 paper into a comprehensive design framework. Key chapters:

### Chapter III — Properties of Received Signal Strength

Empirical characterisation of RSS in a real office building:
- **Distribution**: RSS in dBm is approximately Gaussian (supports the lognormal/normal-in-dB model); some locations deviate due to multipath structure
- **Standard deviation**: Typically 2–4 dBm over time at a fixed location; varies with AP distance and building geometry
- **Stationarity**: RSS is approximately stationary over short intervals; significant drift over hours/days as environment changes
- **Time dependency**: RSS exhibits temporal autocorrelation; adjacent measurements in time are not independent — this motivates collecting samples at intervals or averaging carefully
- **User orientation effect**: User body absorbs ~3–6 dBm depending on orientation; this is a key source of fingerprint uncertainty. Averaging across orientations is recommended during site survey
- **Card heterogeneity**: Different wireless card manufacturers produce different RSS quantisation — card make must be controlled or normalised
- **Causes of location error**: Identified as (1) RSS randomness at a fixed location, (2) insufficient separation between adjacent fingerprints, (3) path-loss gradient insufficient for given grid spacing

### Chapter IV — Mathematical Model

Extends INFOCOM 2004 model with:
- **Probabilistic approach** (Gaussian posterior): derives the full MAP (maximum a posteriori) probability of each location given the sample vector, treating fingerprints as class means and σ as noise
- **Pattern classification framing**: fingerprinting is equivalent to a minimum-distance classifier in N-dimensional signal space
- **Identity covariance assumption**: assumes RSS components are independent (diagonal covariance); validated empirically as a reasonable approximation
- **Error distance distribution**: derives the cumulative distribution of location error (CDF of distance from true location) under the analytical model — enables prediction of "90th percentile accuracy" for a given deployment

### Chapter V — System Design and Deployment

Practical design methodology:
- **Algorithm selection**: compares NNSS (nearest-neighbour), KNN averaging, probabilistic MAP, neural networks, and SVMs on the same dataset; KNN (k=3) performs comparably to MAP with lower computational cost
- **Performance-achieving design**: presents worked design examples: given a target "90% within 3m", compute required N and minimum σ from the model, then determine site survey density
- **Prototype evaluation**: implemented and tested in a real office environment; prototype achieves ~2m median error with 4 APs and 1m grid spacing
- **Design decision guidelines**: a practical flowchart from target accuracy → required N → required σ → required site-survey density

## Follow-On Research (Kaemarungsi Group)

### Properties of RSS for WLAN Location Fingerprinting (MobiQuitous 2004)

Kaemarungsi, K. & Krishnamurthy, P. *Properties of Indoor Received Signal Strength for WLAN Location Fingerprinting.* First Annual International Conference on Mobile and Ubiquitous Systems: Networking and Services (MobiQuitous 2004), Boston MA, Aug. 2004. Source: `raw/01331706.pdf`

This paper directly challenges the Gaussian RSS assumption made in the INFOCOM 2004 analytical model (and widely assumed in prior work), through detailed empirical characterisation of real RSS behaviour.

**User body and orientation effects**:
- User *presence* widens the RSS distribution significantly: standard deviation increases from 0.68 dBm (no user) to 3.00 dBm (user present) at the same location; mean shifts by ~1.2 dBm
- User *orientation* can attenuate a LOS signal by up to **9.32 dB** when the user's body blocks the path to the AP; NLOS APs can completely disappear (zero readings) when the user faces the AP directly
- Practical implication: fingerprint databases must be collected with user presence, and should either include all orientations or fix the user orientation as an application constraint

**RSS distribution (non-Gaussian)**:
- 39 of 46 measured RSS distributions showed left-skewed (not Gaussian) shape; only a few could be approximated by a normal distribution
- Left-skew arises from the upper bound on RSS (maximum receivable power at a location) — the range above the mean is narrower than the range below
- Gaussian model predicts performance on the same order as reality but can be either optimistic or pessimistic depending on the specific location
- Multi-modal distributions occur at some locations — likely from multi-path interference or changing occupancy

**Standard deviation and signal level**:
- Standard deviation *increases* with received signal strength (stronger signal → more variation). This is counter-intuitive: good link quality does not imply low positioning variance
- Consequence: two strong-signal nearby locations are harder to distinguish than two weak-signal nearby locations

**Stationarity**:
- RSS is approximately stationary over 15-minute intervals (mean and autocorrelation function stable)
- Non-stationary over hours — time-of-day and environmental changes (people, objects) shift the mean. RSS at the same location at 10AM vs 10PM can have different mean values

**AP independence**:
- RSS from multiple APs at the same location is empirically uncorrelated — correlation values all < 0.13. Validates the independence assumption in the analytical model
- Same-channel APs do not interfere with each other's reported RSS due to the 802.11b MAC CSMA/CA mechanism

**Practical accuracy**:
- 25-location system (1m grid, 16m² room, 3 APs): empirical misclassification probability ≈ 67%; Gaussian model predicts 85% (pessimistic)
- 2-location system (5.5m apart, 3 APs): empirical error ≈ 10.2×10⁻³; Gaussian analytical formula ≈ 0.24×10⁻³ (optimistic)
- Discrepancy confirms that actual RSS fingerprint patterns are asymmetric and more concentrated around their modes than Gaussian model assumes

---

### Distribution of WLAN RSSI for Indoor Location Determination (ISWPC 2006)

Kaemarungsi, K. *Distribution of WLAN Received Signal Strength Indication for Indoor Location Determination.* International Symposium on Wireless Pervasive Computing (ISWPC), 2006. Source: `raw/distribution-of-wlan-received-signal-strength-indication-for-ind.pdf`

Extends the MobiQuitous 2004 work to compare RSSI characteristics across **five different IEEE 802.11b/g WLAN cards** from different vendors, revealing that the choice of WLAN card significantly affects fingerprinting performance.

**Motivation**: RSSI is not a standardised measurement — each vendor's chipset maps RF power to an integer RSSI value differently (the 0–RSSI_Max range and its mapping to dBm varies per vendor). Using RSSI from one card type during site survey and a different card type online will yield mismatched fingerprints.

**Cards tested**: 3COM (3CRUSB10075), D-Link DWL-650+ (TI chipset), SMC EZ Connect (ADMTek chipset), Hawking HWC54G (Prism GT chipset), Intel PRO/Wireless 2200BG.

**RSSI ranges** (measured empirically):

| Card | Max RSSI (dBm) | Min RSSI (dBm) | Range (dBm) |
|---|---|---|---|
| 3COM 3CRUSB10075 | +10 | -94 | 104 |
| Intel PRO/Wireless 2200BG | -10 | -84 | 74 |
| Hawking HWC54G | 0 | -75 | 75 |
| SMC EZ Connect | -14 | -82 | 68 |
| D-Link DWL-650+ | -50 | -100 | 50 |

**Key finding**: D-Link card (narrowest range: 50 dBm) showed poor ability to differentiate locations — the mean RSSI at the nearest location to an AP was not substantially higher than at the farthest location, making location discrimination difficult. 3COM (widest range: 104 dBm) showed the best differentiation.

**Distribution shape** (confirmed across cards):
- Standard deviation typically ≤3 dBm for all cards; 3COM highest, D-Link lowest
- Left-skewed distributions observed for Intel, Hawking, 3COM, SMC cards (consistent with MobiQuitous 2004 findings)
- D-Link is an exception: showed more right-skewed distributions — due to its compressed RSSI range and chipset-specific RF measurement
- Multi-modal distributions appear when user was absent during part of measurement
- RSSI distribution approaches lognormal (symmetric) when received signal is weak (~-75 dBm) — because the range between sensitivity floor and mean is narrower, limiting asymmetry
- At strong signal (~above -55 dBm), left-skew is pronounced — the upper bound of RSS (maximum attenuated power at close range) limits positive deviation while negative deviation is wider

**Deployment implications**:
- The same WLAN card must be used for both site survey and live positioning (or an explicit RSSI mapping between cards must be calibrated)
- Cards with wider RSSI range and finer granularity yield better fingerprinting discrimination
- Card-specific RSSI quantisation means that any Gaussian or lognormal model must be calibrated per-card, not assumed universal

---

### Floor Estimation Algorithm for Multi-Story Buildings (ECTI 2014)

Maneerat, K., Prommak, C., & Kaemarungsi, K. *Floor Estimation Algorithm for Wireless Indoor Multi-Story Positioning Systems.* 11th International Conference on Electrical Engineering/Electronics, Computer, Telecommunications and Information Technology (ECTI-CON), 2014. Source: `raw/maneerat2014.pdf`

Extends the fingerprinting framework to the floor-determination problem in multi-story buildings, using **IEEE 802.15.4 wireless sensor network (ZigBee)** rather than WiFi.

**Problem**: 2D fingerprinting algorithms estimate (x, y) but not floor. In multi-story buildings, a false floor assignment (e.g., reporting floor 3 instead of floor 2) is far worse than a 2–3m 2D error because it places the user in a completely wrong space.

**Multi-floor technique taxonomy** (three types):
1. Floor-first: estimate floor number from RSS, then determine (x,y) position within that floor
2. Position-first: estimate (x,y) position first, then infer floor number from the computed location
3. Simultaneous: estimate floor and (x,y) jointly

**Proposed algorithm**: Floor-first approach using RSS from IEEE 802.15.4 nodes. The algorithm identifies which floor's RSS fingerprint pattern most closely matches the observed RSS at the mobile target. Compared against: (1) nearest-floor algorithm and (2) group-variance algorithm from literature.

**Results**: Proposed algorithm achieves floor estimation precision of **>90%** — highest among the three algorithms compared. The approach works with WSN hardware, making it applicable to industrial/IoT deployments where ZigBee is preferred over WiFi.

**Relationship to Kaemarungsi framework**: The floor estimation problem is conceptually an extension of the same fingerprint-matching principle: instead of matching to a 2D grid point, the system matches to a floor-level fingerprint. The same chi-squared model of fingerprint discrimination from INFOCOM 2004 applies, but in the inter-floor rather than intra-floor signal space.

---

### Multipath Mitigation for BLE Indoor Localization (ISCIT 2019)

Wisanmongkol, J., Klinkusoom, L., Sanpechuda, T., Kovavisaruch, L., & Kaemarungsi, K. *Multipath Mitigation for RSSI-Based Bluetooth Low Energy Localization.* 19th International Symposium on Communications and Information Technologies (ISCIT), 2019. Source: `raw/wisanmongkol2019.pdf`

Adapts the RSSI-based fingerprinting principles from WiFi to BLE, addressing the specific challenge of multipath-induced RSSI fluctuations in BLE systems. BLE RSSI can drop 20–30 dB from a distance shift of less than 10 cm — making standard fingerprinting unreliable without additional mitigation.

**2-stage multipath mitigation technique**:

**Stage 1 — RSSI Filtering (Weighted Average with Threshold)**: A low-complexity filter that excludes outlier RSSI measurements. Unlike a simple moving average (which always incorporates all samples), this filter detects persistent deviations and forces the running average to update only when the new input is consistent (below threshold RSSIth) or the deviation has persisted long enough (counter C > Cth). Algorithm parameters: weight w=9, threshold RSSIth=0.6, counter threshold Cth=5 samples.

Variance reduction: raw RSSI = 0.0034, moving average filter = 0.0031, proposed filter = **0.0028** — the proposed filter eliminates outlier spikes without lagging behind genuine RSSI changes.

**Stage 2 — RSSI Combining (Maximum Ratio Combining / Space Diversity)**: Two BLE tags are attached to the tracked object, separated by at least λ/4. Since the signals from each tag experience different fading, the probability of both being in deep fade simultaneously is minimised. The Maximum Ratio Combiner (MRC) weights each tag's RSSI by how strong it is relative to the noise floor:

```
MRC = Σᵢ [ (RSSIᵢ − RSSImin) / Σⱼ(RSSIⱼ − RSSImin) ] × RSSIᵢ
```

where RSSImin = −90 dBm (BLE sensitivity floor). Tags with stronger signal receive proportionally higher weight in the combination.

**Propagation model**: Log-distance path loss RSSI = RSSId₀ + 10n·log₁₀(d/d₀) + Xσ. Path loss exponent n varies 0.92–2.30 per anchor in the experimental room (6.8 × 4m office); Xσ reflects shadowing.

**Positioning algorithm**: Geo-n (resilient trilateration that eliminates erroneous intersection points). Minimum 3 anchors; RSSI → distance via inverted path-loss model.

**Results**:

| Configuration | Tag T1 | Tag T2 | Combined |
|---|---|---|---|
| 4 anchors (all) | 2.93m | 2.92m | 3.00m overall |
| 3 anchors (optimised) | 1.80m | 1.90m | **1.50m** overall |

Anchor selection is critical — one anchor (A1) had a slow-decaying RSSI profile making distance estimates unreliable at far ranges. Removing it improved overall combined accuracy from 3.0m to 1.5m. This suggests anchor placement and selection is as important as the filtering/combining algorithm.

**Remaining papers (not yet available as clean PDFs)**:

| Paper | File | Key Contribution |
|---|---|---|
| Koweerawong & Kaemarungsi, 2013 | `raw/Sci-Hub. Indoor localization improvement via adaptive RSS fingerprinting database.pdf` | ⚠️ SCANNED — adaptive database maintenance to handle RSS drift |
| Aomumpai & Kaemarungsi, 2014 | `raw/Sci-Hub. Optimal placement of reference nodes for wireless indoor positioning systems : 2014 11th In.pdf` | ⚠️ SCANNED — optimal AP placement to maximise fingerprint discrimination |

## Entity Summary

### Hardware
- **Medium**: WiFi 802.11b WLAN RSSI (received signal strength in dBm)
- **Infrastructure**: Standard WLAN access points — no specialised hardware required
- **Mobile station**: Any WiFi-capable device; no modification required

### Software
- **Algorithm**: Euclidean NNSS, KNN (k=1–5), MAP probabilistic estimator, neural networks, SVMs — all analysed and compared in thesis
- **Implementation**: Research prototype; theoretical framework is hardware-agnostic
- **Availability**: PhD thesis publicly available; analytical model is closed-form (no code release required)

### Algorithm
- **Location estimation**: Euclidean distance in N-dimensional RSS signal space; select fingerprint with minimum distance
- **Statistical model**: RSS ~ N(rᵢ, σ²) in dBm; distance-squared metric ~ χ²_N distribution
- **Design tool**: Given target accuracy and deployment constraints, compute required N, σ, and grid spacing analytically without empirical trial-and-error

### Accuracy
- **Prototype**: ~2m median error with 4 APs, 1m grid spacing, office environment (thesis)
- **Theoretical**: Framework predicts accuracy from N and σ; performance degrades rapidly below 3 APs or above σ≈4 dBm
- **Comparison**: Comparable empirical accuracy to RADAR (~3m) and Horus (~0.5–2m); Kaemarungsi's key contribution is the *analytical* rather than empirical performance characterisation

### Evaluation
- **Method**: Indoor office building, University of Pittsburgh; real 802.11b network; controlled site survey
- **Ground truth**: Known fixed locations on pre-measured grid
- **Metrics**: Probability of correct location estimation Pc; CDF of location error (percentile accuracy)
- **Baseline**: RADAR (NNSS algorithm) treated as the reference system; Horus used for comparison

## References

| # | Reference | In raw/ |
|---|---|---|
| [RADAR] | Bahl & Padmanabhan, INFOCOM 2000 — RADAR | ✅ `raw/infocom2000.pdf` |
| [Horus] | Youssef & Agrawala, MobiSys 2005 — Horus | ✅ `raw/youssef.pdf` |
| [Kae04] | Kaemarungsi & Krishnamurthy, INFOCOM 2004 | ✅ `raw/Kaemarungsi 2004.pdf` |
| [Kae05] | Kaemarungsi, PhD thesis, Univ. Pittsburgh, 2005 | ✅ `raw/p-0012--DESIGN_OF_INDOOR_POSITIONING_SYSTEMS.pdf` |
| [KaeMQ04] | Kaemarungsi & Krishnamurthy, MobiQuitous 2004 | ✅ `raw/01331706.pdf` |
| [KaeISW06] | Kaemarungsi, ISWPC 2006 | ✅ `raw/distribution-of-wlan-received-signal-strength-indication-for-ind.pdf` |
| [ManECTI14] | Maneerat, Prommak & Kaemarungsi, ECTI-CON 2014 | ✅ `raw/maneerat2014.pdf` |
| [WisISCIT19] | Wisanmongkol et al. & Kaemarungsi, ISCIT 2019 | ✅ `raw/wisanmongkol2019.pdf` |

## Relationships

- [[RADAR-WiFi-Fingerprinting]] — Kaemarungsi develops the analytical theory behind the NNSS algorithm introduced by RADAR; RADAR is the empirical predecessor
- [[Horus-WLAN-System]] — Horus introduced probabilistic (Bayesian) fingerprinting; Kaemarungsi's MAP model provides an analytical underpinning for the probabilistic approach
- [[WiFi-Fingerprinting-Advances]] — Kaemarungsi's framework is cited as foundational in the He & Chan COMST 2016 survey; subsequent work extends to adaptive databases, sparse recovery, and crowdsourcing
- [[Indoor-Localization-ML-Methods]] — thesis compares NNSS, KNN, MAP, neural networks, and SVMs — a foundational comparison for ML-based localization
- [[Indoor-IPS-Datasets]] — thesis identifies key dataset collection requirements (orientation averaging, sample count, temporal stability) that shaped subsequent dataset design practice

---
tags: [WiFi, CSI, fingerprinting, OFDM, indoor-localization, RSSI, FILA, device-based, multipath, physical-layer, propagation-model, trilateration, Bayesian, ensemble-learning, MIMO, data-augmentation]
date-compiled: 2026-06-15
source-files:
  - "raw/Sci-Hub. CSI-Based Indoor Localization : IEEE Transactions on Parallel and Distributed Systems, 2013.pdf"
  - "raw/CSI_based_Indoor_Localization.pdf"
  - "raw/v1_ CSI_based_Indoor_localization_using_Ensemble_Neural_Networks.pdf"
status: active
---

# FILA — CSI-Based Indoor Localization

## Summary

Wu, K., Xiao, J., Yi, Y., Chen, D., Luo, X., and Ni, L.M. (Department of Computer Science and Engineering, Hong Kong University of Science and Technology; Sun Yat-sen University, China). *IEEE Transactions on Parallel and Distributed Systems*, Vol. 24, No. 7, July 2013. DOI: 10.1109/TPDS.2012.214.

The paper introduces **FILA**, a cross-layer WiFi indoor localization system that replaces RSSI with **Channel State Information (CSI)** from OFDM subcarriers. FILA has two components: a **CSI-based propagation model** for range estimation, and a **CSI-based fingerprinting** system for room-level and fine-grained localization. Both components exploit the *frequency diversity* of the 30-group 802.11n subcarrier CSI measurements to overcome the multipath limitations that degrade RSSI-based systems.

Implemented on commercial Intel iwl5300 802.11n NICs, FILA achieves median localization accuracy of **0.45 m (single room)** and **1.2 m (multi-room corridor)** — substantially better than corresponding RSSI-based approaches — with tracking latency of approximately **0.01 s**, compared to 2–3 s for RSSI-based tracking systems.

## Motivation: Why RSSI Fails

The paper identifies two fundamental, physically grounded problems with RSSI-based indoor localization:

**Problem 1 — Temporal variance**: RSSI collected from a fixed, immobile receiver over 1 minute shows variance up to **5 dBm** (Fig. 1 in paper). This instability makes RSSI an unreliable distance estimator even without any movement, as the measurement itself fluctuates.

**Problem 2 — Multipath effect**: In indoor environments, reflections from ceiling, floor, and walls create multiple signal copies arriving via different paths with different time delays. The per-packet RSSI aggregates all these paths — a *closer* receiver can have *lower* RSSI than a more distant one, breaking the fundamental monotonic distance-power assumption. The multipath is worse in offices and corridors than in open spaces.

These are **physical-layer problems**, not algorithmic deficiencies. RSSI-based fingerprinting systems such as [[RADAR-WiFi-Fingerprinting]] and [[Horus-WLAN-System]] cannot overcome these limits regardless of matching algorithm sophistication.

**CSI's key advantages**:
- In OFDM systems, each subcarrier has a distinct channel response. Per-subcarrier CSI variance is within **1 dBm** (vs. 5 dBm for RSSI)
- The multipath effect that corrupts RSSI becomes an *exploitable differentiator* — different locations produce different per-subcarrier patterns due to their unique multipath combinations
- 30 subcarrier groups provide far more information per packet than a single RSSI scalar

## FILA System Architecture

The FILA architecture modifies **only the receiver end** — no hardware or firmware changes to APs (TX side). A new "localization block" is inserted in the receiver NIC processing pipeline to extract CSI after normal demodulation, adding zero overhead to packet transmission.

**OFDM channel model** (frequency domain):

```
y = Hx + n
```
where y = received vector, x = transmitted vector, H = channel matrix, n = AWGN.

**CSI estimation**:
```
Ĥ = y/x
```

Ĥ is a fine-grained value from the PHY layer describing the channel gain from TX to RX baseband. In 802.11n, 30 groups of CSI values are extracted (approximately one group per two subcarriers), each containing amplitude |H_i| and phase ∠H_i.

FILA's localization block runs **concurrently** with normal packet decoding; CSI extraction adds no latency to data transmission.

## Component 1: CSI-Based Propagation Model

The propagation model enables range estimation (distance to each AP) from CSI measurements. It has three steps.

### Step 1: CSI Processing (Multipath Mitigation)

Two stages address multipath fading and shadowing:

**Time-domain multipath mitigation**: The 30-group frequency-domain CSI is converted to the time domain via IFFT. In the time-domain impulse response:
```
h(τ) = Σ αk δ(τ - τk),  k = 0 ... Lp-1
```
the LOS signal and multipath reflections arrive at different delays τk. A truncation window centred on the highest-gain component (threshold at 50% of first peak value) filters out reflections. The LOS (or shortest NLOS) path is retained. FFT converts back to frequency domain. This process leverages the 20 MHz 802.11n bandwidth, which exceeds the channel coherence bandwidth in typical indoor environments and allows the receiver to resolve different reflection paths.

**Frequency-domain fading compensation**: Multiple uncorrelated subcarriers are weighted-averaged to compute the **effective CSI** (CSI_eff):

```
CSI_eff = (1/K) × Σ (f_k/f₀) × |H_k|,   k ∈ (-15, 15)
```
where f₀ is the central frequency, f_k is the k-th subcarrier frequency, and |H_k| is the amplitude of the k-th subcarrier CSI. The weighting factor f_k/f₀ accounts for the fact that radio propagation is frequency-dependent, transferring the channel gain from multiple subcarriers to an equivalent single-subcarrier (central) value. This reduces the variance due to small-scale fading.

Commercial NICs embed hardware FFT/IFFT circuits, so this processing introduces **negligible latency**.

### Step 2: Calibration (Refined Propagation Model)

The standard RSSI free-space propagation model cannot be directly applied to CSI_eff (CSI is measured at baseband, not at the RF antenna). FILA develops a **refined indoor propagation model**:

```
d = (c / (4π f₀ × |CSI_eff|))^(1/n) × σ
```
where:
- c = speed of light (wave velocity)
- f₀ = central frequency
- σ = **environment factor** (captures baseband-to-RF gain, antenna gains, wall penetration loss for NLOS APs)
- n = **path loss fading exponent** (range 2–4 depending on environment; ~2 in corridor/free space, >4 in complex offices with LOS augmented by NLOS)

Both σ and n are **calibrated per-AP** (rather than assuming a single uniform exponent for all APs) because channel propagation varies significantly depending on the relative position of the mobile client with respect to each AP.

**Fast training algorithm**: Supervised learning with 3 anchor points in offline phase:
1. Collect CSIs at 2 anchors → train σ and n for the refined propagation model
2. Test on CSIs at 3rd anchor
3. Iterate until convergence

Convergence takes approximately 16 ms (20 CSI packets × 0.8 ms/packet at 802.11n).

### Step 3: Location Determination

With calibrated σ and n per AP, distance d to each AP is computed from CSI_eff. The AP's coordinate is obtained from the network layer. **Trilateration** over 3+ APs produces the final position estimate. (Details in paper supplemental file.)

## Component 2: CSI-Based Fingerprinting

The fingerprinting approach leverages CSI's frequency diversity — different locations produce unique per-subcarrier amplitude and phase patterns due to their individual multipath combinations.

### Radio Map Construction (Offline Phase)

At each reference point lj = (lj,x, lj,y), two features are recorded for each detected AP:

**Feature 1 — Summational CSI** (captures large-scale signal strength):
```
H_e = Σ |H_i|²,   i ∈ [1, 30]
```

**Feature 2 — Normalised per-subcarrier CSI** (captures fine-grained multipath fingerprint):
```
H = [H₁, H₂, ..., H₃₀]ᵀ
```
where each H_i = |H_i| e^(j sin{∠H_i}) preserves both amplitude and phase.

The combination of large-scale and fine-grained CSI provides a highly discriminative location fingerprint.

### Positioning Algorithm (Online Phase)

For the K best APs (highest H_e values; K=3 in experiments):

**Prior probability** using Pearson correlation between observed normalised CSI H(O) and radio map CSI H(lj):
```
P(lj) = ρ_{H(O), H(lj)} / Σᵢ ρ_{H(O), H(lᵢ)}
```
where:
```
ρ_{H(O), H(lj)} = Σ_k cov(Hk(O), Hk(lj)) / (σ_{Hk(O)} × σ_{Hk(lj)})
```
This exploits the fact that spatial channel correlation decreases with distance — higher Pearson correlation indicates the terminal is closer to reference point lj.

**Likelihood** (Gaussian model for H_e at each reference point):
```
P(H_e,k | lj) = (1 / √(2π σ_e,k)) × exp(-(H_e,k - H̄_e,k)² / (2σ_e,k²))
```

**Posterior** (Bayes' law):
```
P(lj | H_e) ∝ P(lj) × Π_k P(H_e,k | lj)
```

**Location estimate** (weighted average over candidate set):
```
l̂ = Σⱼ P̄(lj | H_e) × lj
```

The fingerprinting approach runs locally on the terminal — no public server required for localisation queries.

## Experimental Evaluation

### Experimental Scenarios (HKUST campus)

| Scenario | Dimensions | Description |
|---|---|---|
| Chamber | 3×4m | Ideal LOS free-space; specialised material absorbs NLOS signals |
| Research Lab | 5×8m | Typical office; 3 APs on shelf tops; students walking around |
| Lecture Theatre | 20×20m | Large single room; assesses scalability |
| Corridor | 32.5×10m | Multiple rooms with offices aside; APs in different rooms |

**Platform**: HP laptop with Intel iwl5300 NIC; 802.11n. 20 CSI packets collected at each position.

### Result 1: CSI Temporal Stability

| Metric | RSSI | CSI_eff |
|---|---|---|
| Temporal variance (1 min, fixed receiver) | **5 dBm** | **< 1 dBm** |

CSI is dramatically more stable than RSSI over time. Even in the ideal chamber environment, RSSI still varies significantly due to inaccurate packet-level measurement; CSI remains stable.

### Result 2: CSI-Based Propagation Model Accuracy

| Environment | FILA improvement over RSSI |
|---|---|
| Best case (some positions) | Up to **10×** |
| Average across single link | **~3×** |

| Scenario | FILA median error | RSSI median error |
|---|---|---|
| Research Lab | 0.45 m | ~1.6 m |
| Lecture Theatre | 1.2 m | — |
| Corridor (multi-room) | 1.2 m (average tracking) | — |
| Research Lab (90th percentile) | 1.0 m | — |
| Lecture Theatre (90th percentile) | 1.8 m | — |

### Result 3: CSI Fingerprinting vs. Horus

| Environment | FILA median | Horus median | FILA gain |
|---|---|---|---|
| Laboratory | **0.65 m** | 0.8 m | ~24% |
| Corridor (6 APs) | **1.07 m** | ~1.42 m | ~25% |
| Lab (90th percentile error) | **1.3 m** | 1.6 m | — |
| Corridor (90th percentile improvement) | **0.55 m better** than Horus | — | — |

Effect of AP density: Both approaches improve with more APs, but FILA consistently reduces mean distance error by ~29% on average across 1–6 APs.

### Result 4: Latency

| Phase | FILA | RSSI-based systems |
|---|---|---|
| Calibration | ~16 ms | — |
| Location determination | ~2 ms | 2–3 s |
| Tracking latency | **~0.01 s** | 2–3 s |

FILA's tracking latency (~0.01 s) is approximately **200–300× faster** than RSSI-based tracking systems. The IFFT/FFT processing leverages NIC hardware circuits with negligible additional latency.

## Conclusions and Future Work

FILA demonstrates that replacing RSSI with CSI at the physical layer resolves the fundamental instability and multipath problems that constrain RSSI-based indoor localisation. The combination of time-domain multipath mitigation, frequency-domain fading compensation, and per-AP propagation model calibration yields both a dramatically more stable metric and a more discriminative fingerprint.

Authors identify three directions for future work:
1. Leverage multiple AP cooperation for further accuracy improvement
2. Exploit **spatial diversity** (MIMO antenna arrays) in addition to frequency diversity
3. Implement FILA on smartphones with 802.11n chipsets

## Limitations

- Requires 802.11n NIC with accessible CSI extraction (Intel iwl5300 + patched Linux driver) — not available on all devices; not on iOS/Android without custom hardware
- Per-environment calibration required: σ and n must be re-estimated if environment changes significantly
- Only 2D localisation; floor identification not addressed
- Trilateration requires at least 3 APs with adequate geometry; sparse AP deployments degrade performance (though FILA degrades more gracefully than RSSI)
- Fingerprint radio map must be collected offline at reference points

## Ensemble Neural Networks for CSI Localization (Sobehy, Renault & Mühlethaler, MLN 2019)

Sobehy, A., Renault, É., Mühlethaler, P. *CSI based Indoor Localization using Ensemble Neural Networks.* 2nd IFIP International Conference on Machine Learning for Networking (MLN 2019), Paris. DOI: 10.1007/978-3-030-45778-5_25. Télécom SudParis / INRIA Rocquencourt.

This work builds directly on FILA's insight that CSI magnitude outperforms RSSI, extending it with magnitude-slope features, data augmentation, and ensemble MLP learning over a MIMO dataset.

### Testbed

Arnold et al. 2019 (SCC) public dataset: robot transmitter traversing a **4 × 2 metre tabletop**; 2×8 MIMO receiver (16 antenna elements); transmission at **1.25 GHz**, 20 MHz bandwidth; **1,024 subcarriers** (10% guard bands → 924 usable); ~15,000 training positions, each with ground truth from a tachymeter (±1 cm precision).

### Input Feature: Magnitude Slope

Of the four CSI components (real, imaginary, magnitude, phase), **magnitude** is the most temporally stable — the same position across multiple transmissions produces highly similar magnitude profiles. This is the same finding as FILA, extended here to MIMO.

The key preprocessing contribution: instead of using raw magnitude values, the authors compute the **slope (finite difference) between consecutive spectral points** along the fitted line. For 66 representative points, this gives 65 slope values per antenna. Using slope rather than absolute magnitude reduces MSE from **4.5 cm → 4.2 cm** (~7% improvement), showing that **spectral variation** is more position-discriminative than absolute amplitude.

**Preprocessing pipeline:**

1. Polynomial fitting on 4 spectral sections of the 924-point magnitude profile (different polynomial degree per section)
2. Resample to 66 representative points along the fitted curve
3. Compute 65 inter-point differences (slopes) as the feature vector
4. Repeat for all 16 antennas → input dimension = 65 × 16 = 1,040 per sample

### Data Augmentation

Two-sided Gaussian augmentation applied to each training sample:

- **Output (position)**: Gaussian(μ=given position, σ=1/3 cm) — models tachymeter measurement uncertainty
- **Input (magnitude)**: Points scattered around fitted line with Gaussian(μ=0, σ=2 × empirical_std_of_residuals) — analogous to image blurring

Result: augmenting each training sample with one synthetic sample reduces MSE from **8 cm → 6.7 cm**. More augmented samples → lower MSE monotonically.

### Ensemble Architecture

Multiple MLPs are trained with varied hyperparameters and/or different training subsets (including k-fold splits). Predictions are combined using one of six mixing strategies:

| Strategy | Description |
|---|---|
| **Mean** | Arithmetic mean of all MLP position predictions |
| **Weighted mean** | Each MLP weighted proportional to its individual accuracy |
| **Weighted power mean** | Weights raised to a power before averaging (amplifies high-accuracy MLPs) |
| **Median** | Position closest to all others — robust to outlier predictions |
| **Random** | Randomly select one MLP prediction (baseline) |
| **Best-pick** | Oracle: select the individual prediction closest to truth (upper bound) |

**MLP hyperparameters** (base configuration):

| Hyperparameter | Value |
|---|---|
| Layers | 5 |
| Units per layer | 512 |
| Activation | ReLU |
| Optimizer | Adam |
| Learning rate | 0.0005 |
| Batch sizes | [128, 256, 512, 1024, 2096] |
| Epochs | 100 |

### Results

Best results achieved with **11 MLPs** using weighted mean / median mixing:

| Method | MSE |
|---|---|
| CNN (Arnold et al. 2019, real+imaginary components) | 32 cm |
| NDR (Sobehy et al., companion paper — MLP on magnitude) | 4.3–4.5 cm |
| Single best MLP (magnitude slope + augmentation) | 3.9 cm |
| **Ensemble NN (11 MLPs, 1× data augmentation)** | **3.1 cm** |
| Best-pick oracle (11 MLPs) | < 3.1 cm (upper bound) |

Key insight: adding MLPs with **higher individual error** can still improve ensemble accuracy if their errors are not correlated with existing ensemble members — diversity is more valuable than per-model accuracy above a threshold.

The ~30% improvement over NDR (4.3 cm → 3.1 cm) is achieved with 11 MLPs; accuracy continues to improve with additional ensemble members.

### Key Findings

- **Phase component is unreliable** for CSI localization (consistent with prior work); magnitude is the preferred input
- **Spectral slope** (inter-point difference) outperforms absolute magnitude values
- **Ensemble diversity** matters more than per-model quality; k-fold trained models capture dataset variance effectively
- **Data augmentation is necessary**: without it, ensemble accuracy degrades substantially
- **CNN underperforms MLP-ensemble** on this task (32cm vs 3.1cm) because real+imaginary CSI components are temporally unstable

### Limitations

- Controlled tabletop testbed only (4×2 m); no multi-room or multi-floor evaluation
- Requires MIMO hardware with many antenna elements; not commodity single-antenna WiFi
- No comparison against model-based (propagation equation) approaches like FILA's range estimation
- Outlier predictions from individual MLPs identified as the next research challenge

---

## Entity Summary

### Hardware
- **Medium**: WiFi 802.11n (OFDM), 2.4 GHz; 30-group per-subcarrier CSI measurements (FILA); 1.25 GHz, 20 MHz bandwidth, 924 usable subcarriers (Sobehy et al.)
- **NIC**: Intel iwl5300 (iwlwifi Linux driver, patched for CSI extraction)
- **Device requirement**: Device-based — tracked user must carry 802.11n WiFi-enabled device
- **No AP modification**: Standard APs; FILA modifications are receiver-side only

### Software
- **CSI extraction**: Modified Intel iwlwifi Linux driver (same platform as [[E-eyes-Device-Free-Localization]])
- **System**: FILA — research prototype; not publicly released as complete system
- **CSI tool**: Intel 5300 CSI Tool is open-source
- **Language**: Not specified; implemented on HP laptop with 2.4 GHz dual-core CPU

### Algorithm
- **CSI_eff computation**: Weighted average of 30 subcarrier groups (Eq. 4)
- **Multipath mitigation**: IFFT → time-domain truncation window → FFT
- **Propagation model**: Revised free-space path loss with per-AP environment factor σ and fading exponent n
- **Fingerprinting prior**: Pearson correlation of per-subcarrier normalised CSI vectors
- **Fingerprinting likelihood**: Gaussian model on summational CSI H_e per AP
- **Location estimate**: Weighted average over posterior probabilities

### Accuracy
- **FILA propagation model** (single room): Median 0.45 m; 90th percentile 1.0 m
- **FILA fingerprinting** (lab): Median 0.65 m vs. Horus 0.80 m (~24% improvement)
- **FILA multi-room corridor**: Median 1.07–1.2 m; improvement over RSSI up to 10× at best positions
- **Sobehy et al. 2019 ensemble (11 MLPs)**: **3.1 cm MSE** on 4×2m MIMO tabletop testbed
- **Sobehy et al. NDR baseline**: 4.3–4.5 cm MSE; CNN baseline: 32 cm MSE

### Evaluation
- **FILA ground truth**: Pre-measured reference point positions at 10 (propagation) and 28 (fingerprinting) positions; 20 CSI/RSSI packets per position
- **FILA baseline**: Corresponding RSSI-based propagation model + trilateration; [[Horus-WLAN-System]] for fingerprinting comparison
- **FILA metrics**: CDF of localization error, mean distance error, temporal variance (dBm), tracking latency
- **FILA venue**: IEEE Transactions on Parallel and Distributed Systems, Vol. 24, No. 7, July 2013
- **Sobehy ground truth**: Arnold et al. 2019 dataset — tachymeter-measured positions (±1 cm); 15k training samples; 10-fold cross-validation
- **Sobehy baseline**: NDR (Sobehy companion paper), CNN (Arnold et al. 2019); metric = MSE in cm²
- **Sobehy venue**: IFIP MLN 2019 (Machine Learning for Networking), Paris

### Ground Truth
- Reference nodes at pre-measured physical positions in each scenario
- Device moved to 10–28 positions per scenario; 20 CSI/RSSI packets collected at each position
- Walking experiments at ~1 m/s for tracking latency evaluation

### Baseline
- RSSI-based propagation model + trilateration (corresponding RSSI approach)
- [[Horus-WLAN-System]] (RSSI-based probabilistic fingerprinting) for fingerprinting comparison

### Metrics
- Median localization error (m)
- CDF of localization error
- 90th-percentile localization error
- Temporal variance of CSI_eff vs. RSSI (dBm)
- Mean distance error (m) vs. number of APs
- Tracking latency (s)

## References

| # | Reference | In raw/ |
|---|---|---|
| [1] | ✅ Bahl, P., Padmanabhan, V.N. *RADAR: An In-Building RF-Based User Location and Tracking System.* INFOCOM 2000. → [[RADAR-WiFi-Fingerprinting]] | ✅ `raw/infocom2000.pdf` |
| [4] | Zhang, D., Ma, J., Chen, Q.B., Ni, L.M. *An RF-Based System for Tracking Transceiver-Free Objects.* IEEE PerCom 2007. | No |
| [6] | Halperin, D., Hu, W.J., Sheth, A., Wetherall, D. *Predictable 802.11 Packet Delivery from Wireless Channel Measurements.* ACM SIGCOMM 2010. | No |
| [7] | Goldsmith, A. *Wireless Communications and Networks: 3G and Beyond.* Tata McGraw Hill, 2010. | No |
| [8] | Fazal, K., Kaiser, S. *Multi-Carrier and Spread Spectrum Systems.* Wiley, 2008. | No |
| [9] | ✅ Youssef, M., Agrawala, A. *The Horus WLAN Location Determination System.* ACM MobiSys 2005. → [[Horus-WLAN-System]] | No |
| [10] | Fang, S., Lin, T., Lee, K. *A Novel Algorithm for Multipath Fingerprinting in Indoor WLAN Environments.* IEEE Trans. Wireless Commun., 2008. | No |
| [11] | Haeberlen, A., Flannery, E., Ladd, A.M., Rudys, A., Wallach, D.S., Kavraki, L.E. *Practical Robust Localization over Large-Scale 802.11 Wireless Networks.* ACM MobiCom 2004. | No |
| [12] | He, T. et al. *VigilNet: An Integrated Sensor Network System for Energy-Efficient Surveillance.* ACM Transactions on Sensor Networks, 2006. | No |

**Sobehy et al. 2019 references** (papers cited in `v1_ CSI_based_Indoor_localization_using_Ensemble_Neural_Networks.pdf`):

| # | Reference | In raw/ |
|---|---|---|
| [9-S] | ✅ Wu, K. et al. *FILA: Fine-Grained Indoor Localization.* IEEE INFOCOM 2012. (preprint of compiled FILA paper) | `raw/CSI_based_Indoor_Localization.pdf` (preprint) |
| [10-S] | Arnold, M., Hoydis, J., Brink, S.T. *Novel Massive MIMO Channel Sounding Data applied to Deep Learning-based Indoor Positioning.* SCC 2019. | No |
| [11-S] | Sobehy, A., Renault, É., Mühlethaler, P. *NDR: Noise and Dimensionality Reduction of CSI for Indoor Positioning using Deep Learning.* GlobeCom 2019, Hawaii. (hal-023149) | No |
| [12-S] | Wang, X., Gao, L., Mao, S., Pandey, S. *DeepFi: Deep Learning for Indoor Fingerprinting using Channel State Information.* IEEE WCNC 2015. | No |
| [6-S] | ✅ Yang, Z., Zhou, Z., Liu, Y. *From RSSI to CSI: Indoor Localization via Channel Response.* ACM CSUR 46(2), 2013. | No |

## Source Versions

This article covers two versions of the same work:
- **IEEE INFOCOM 2012 conference paper** (preprint): `raw/CSI_based_Indoor_Localization.pdf` — shorter, introduces the core FILA system
- **IEEE TPDS 2013 journal paper** (full version): `raw/Sci-Hub. CSI-Based Indoor Localization : IEEE Transactions on Parallel and Distributed Systems, 2013.pdf` — expanded evaluation, additional scenarios

The Sobehy et al. 2019 ensemble paper (`raw/v1_ CSI_based_Indoor_localization_using_Ensemble_Neural_Networks.pdf`) builds on the TPDS version.

## Relationships

- [[RADAR-WiFi-Fingerprinting]] — FILA's CSI fingerprinting directly addresses the RSSI-based fingerprinting that RADAR pioneered; provides quantitative evidence of RSSI's fundamental instability (5 dBm variance)
- [[Horus-WLAN-System]] — explicit comparison baseline for CSI fingerprinting; FILA achieves ~24–25% median accuracy improvement over Horus in both lab and corridor environments
- [[E-eyes-Device-Free-Localization]] — both systems exploit OFDM CSI on the same Intel iwl5300 hardware platform; E-eyes applies CSI to device-free activity recognition; FILA applies it to device-based position estimation
- [[Indoor-Localization-ML-Methods]] — CSI fingerprinting is the signal-side advance; FILA pairs it with Bayesian probabilistic matching (same family as Horus); frequency diversity motivates spatial diversity extensions using MIMO
- [[Indoor-Location-Sensor-Technologies]] — FILA demonstrates WiFi CSI as a more capable sensor than WiFi RSSI; establishes CSI as the preferred PHY-layer observable for high-accuracy WiFi localisation
- [[Kaemarungsi-WiFi-Fingerprinting]] — Kaemarungsi's analytical framework characterises RSSI fingerprint properties; FILA's use of per-subcarrier CSI vectors requires extension of that framework to multi-dimensional features
- [[WiFi-Fingerprinting-Advances]] — FILA represents a fundamental signal-type advance in fingerprinting; subsequent work on deep learning fingerprinting often uses CSI as the input signal type

---
tags: [indoor-localization, compressive-sensing, compressive-sampling, sparse-recovery, l1-minimization, RSS, RF-emitter-localization, wireless-sensor-networks, dictionary, non-uniform-sampling, tensor-completion, low-rank, kernel-learning, 3D-localization]
date-compiled: 2026-08-14
updated: 2026-08-20
source-files:
  - "raw/tabibiazar2011.pdf"
  - "raw/balestrieri2020.pdf"
  - "raw/TIIS Vol 13, No 1-12.pdf"
status: active
---

# Compressive Sensing / Compressive Sampling for Localization

## Summary

Compressive Sensing (CS) exploits the fact that, although a localization problem lives in a high-dimensional space (many candidate grid points, or a wide RF band), the *solution* is **sparse** — a target occupies only one (or a few) of the candidate positions, and an RF spectrum is occupied by only a few emitters. CS theory states that such sparse signals can be recovered exactly from **far fewer measurements than the Nyquist rate** by solving an under-determined linear system with an ℓ₁-norm (sparsity-promoting) constraint. Two distinct uses appear in the sources compiled here:

1. **CS for target/sensor position estimation** — Tabibiazar & Basir (2011) recast wireless-sensor-network localization as sparse signal recovery over a discretized spatial grid, recovering a target's position from a small number of inconsistent RSS/ToA measurements against known anchors.
2. **Compressive *Sampling* for RF-emitter localization** — Balestrieri et al. (2020) apply CS at the *acquisition* stage: Wideband Spectrum Sensors sample a wide RF band far below Nyquist (Non-Uniform Sampling), relaxing throughput/data-transmission requirements while still localizing RF emitters via received-signal energy at spatially distributed receivers.

3. **Tensor completion for a partially-measured fingerprint database** — Lu et al. (2019) exploit the **low-rank** structure that spatial correlation induces in the RSS data of a 3-D grid, and recover the *unmeasured* grid points rather than the target position, then match with a kernel method. This is the low-rank sibling of sparse recovery: same "recover a high-dimensional object from few samples" premise, applied to the training database instead of the position estimate.

The first two share the core CS machinery (sparsifying dictionary Ψ, sensing matrix Φ, dictionary coherence µ(Ψ), and the measurement bound M ≥ O(K·log(N/K)) for K-sparse recovery from N candidates). This article is the dedicated home for the CS approach; the fingerprinting-side sparse-recovery reformulation and closely related radio-map matrix-completion methods are covered in [[WiFi-Fingerprinting-Advances]].

---

## Source 1 — Compressive Sensing Indoor Localization (Tabibiazar & Basir, 2011)

**Citation.** Tabibiazar, A., Basir, O. *Compressive Sensing Indoor Localization.* IEEE International Conference on Systems, Man, and Cybernetics (SMC), 2011. Electrical and Computer Engineering Department, University of Waterloo. Source: `raw/tabibiazar2011.pdf` (6 pages, fully text-extractable).

### Approach
Sensor location estimation is posed as: a target sensor measures inconsistent signals (RSS or ToA) from anchor sensors at known locations; the target's location must be estimated over a discretized spatial grid. Because even large-scale WSNs carry relatively sparse *information* compared with the number of grid points, the localization problem is recast as **sparse signal recovery in the discrete spatial domain from a small number of linear measurements** — an under-determined system solved by **ℓ₁-norm minimization**.

Formally, the position of interest x over the N-point grid is sparse (ideally 1-sparse). With sparsifying basis/dictionary Ψ (N×N) built from signal readings on the grid and a sensing matrix Φ (M×N), recovery requires M ≥ O(K·log(N/K)) measurements for K-sparse x, and the dictionary must have **low coherence** µ(Ψ) (defined as the maximum normalized inner product between distinct columns of Ψ) for reliable recovery.

### Entity Summary
- **Hardware / medium:** Wireless sensor network anchors and a target node; radio measurements at **center frequency 2.443 GHz** (RSS and ToA modalities).
- **Software:** ℓ₁-minimization / sparse-recovery solver (convex program); dictionary Ψ built from grid signal readings.
- **Algorithm:** Compressive sensing; ℓ₁-norm (Basis Pursuit) minimization over a non-symmetric spatial grid; dictionary coherence analysis; a proposed scheme for dynamically **updating the sparsifying matrix Ψ** as an area of future work.
- **Accuracy:** Accuracy depends strongly on tunable parameters — signal-to-noise ratio (SNR) and a detection threshold (thr); higher thresholds trade sensitivity for robustness. Reported as normalized distance-estimation error rather than an absolute metre figure; a worked example uses a **44×44** sparsity matrix Ψ over the grid.
- **Evaluation:** Simulation over a spatial grid; sweep of SNR and threshold parameters; normalized distance estimation error as the figure of merit.
- **Metrics:** Normalized distance estimation error; dictionary coherence µ(Ψ); measurement count M vs. sparsity K.
- **Baseline:** Contrast with conventional anchor-based estimation that degrades sharply under inconsistent/noisy measurements — CS recovers position from far fewer, noisier samples.

---

## Source 2 — A Method Exploiting Compressive Sampling for Localization of RF Emitters (Balestrieri et al., 2020)

**Citation.** Balestrieri, E., De Vito, L., Picariello, F., Tudosa, I. *A Method Exploiting Compressive Sampling for Localization of Radio Frequency Emitters.* IEEE Transactions on Instrumentation and Measurement (accepted 2019, publication 2020). DOI: 10.1109/TIM.2019.2954759. University of Sannio, Benevento, Italy. Source: `raw/balestrieri2020.pdf` (10 pages, fully text-extractable).

### Approach
A novel method for **Radio Frequency (RF) emitter localization** using **Wideband Spectrum Sensors (WSS)** that exploit **Compressive Sampling (CS)**. R receivers with known Cartesian coordinates are deployed in the plane where transmitters are expected. Each WSS uses a **Non-Uniform Sampling (NUS)** scheme to continuously monitor a wide band (up to ~5 GHz) while sampling **below the Nyquist criterion**, relaxing throughput and data-transmission requirements. A hardware receiver was realized from **commercial off-the-shelf (COTS)** components, operating over 1 MHz – 3.8 GHz with a 10 dBi omni-directional antenna around 2.5 GHz.

### Entity Summary
- **Hardware / medium:** COTS wideband receiver front-end (RF range 1 MHz–3.8 GHz), 10 dBi omni-directional antenna (~2.5 GHz); R spatially distributed receivers with known coordinates; RF/electromagnetic medium.
- **Software:** Non-Uniform Sampling acquisition + compressive reconstruction; energy/received-power based multilateration of emitters.
- **Algorithm:** Compressive Sampling with NUS; position estimation compared against classical estimators — **Maximum Likelihood (ML)**, **SDP-DRSS**, **WTLS-URSS/WTLS-DRSS** — and referenced to the **Cramér-Rao Lower Bound (CRLB)**.
- **Accuracy:** With **10 receivers**, localization accuracy **on the order of 1 m**. RMSE stays close to ML and the CRLB, and degrades gracefully as the **Compression Ratio (CR)** increases; the CS-based sensors match uniform-sampling performance up to a **maximum compression ratio of 64**.
- **Evaluation:** Mathematical derivation + numerical/Monte-Carlo simulation, then a hardware implementation. Position accuracy assessed against (i) compression ratio CR and (ii) log-normal shadowing standard deviation, for two emitters at carrier frequencies 2.50 GHz and 2.52 GHz (20 kHz occupied bandwidth).
- **Ground truth:** Known receiver coordinates and simulated/known emitter positions.
- **Metrics:** Root-Mean-Square Error (RMSE, ∆) and standard deviation (σ) of the position estimate; Compression Ratio (CR); comparison to CRLB.
- **Baseline:** Classical ML, SDP-DRSS, and WTLS-based ultrasonic/RSS localization methods; uniform (Nyquist) sampling as the non-compressed reference.

---

## Source 3 — Efficient Kernel Based 3-D Source Localization via Tensor Completion (Lu, Zhang, Ma & Kan, 2019)

**Citation.** Lu, S., Zhang, J., Ma, X., Kan, C. *Efficient Kernel Based 3-D Source Localization via Tensor Completion.* KSII Transactions on Internet and Information Systems, **13**(1), Jan 2019, pp. 206 ff. DOI: 10.3837/tiis.2019.01.012. Capital University of Economics and Business; Heilongjiang International University; Army Engineering University of PLA. Source: `raw/TIIS Vol 13, No 1-12.pdf` (16 pages, fully text-extractable — the filename is a journal-issue label, not a title).

### Approach

The target problem is **source localization in 3-D wireless sensor networks** — UAVs in constrained airspace, sources in venues — where the authors argue two standard routes both fail. Distance-based RSS methods need the propagation model "correctly and concisely described", which varying air-ground topography defeats. Fingerprinting is more robust to complex conditions because it learns rather than models, but a 3-D database is punishing to build: the grid-point count explodes relative to 2-D, denser grids mean better accuracy and more training, some points are simply unmeasurable ("remote corners"), and any environmental change forces recalibration.

Their move is to stop trying to measure every grid point. Three steps:

1. **Establish the low-rank property.** Exploit the *spatial correlation* of RSS across the measurement area to show the RSS data matrix is low-rank — the structural precondition that makes recovery from partial data possible at all.
2. **Tensor completion in the training phase.** Measure RSS at a randomly chosen subset Ω of grid points, then recover the missing entries of the fingerprint tensor. The database ends up complete and the measurement campaign does not.
3. **Kernel-based learning in the matching phase.** Replace nearest-neighbour matching with a kernel method that "clarifies the complicated relationship between the fingerprint and the corresponding position", improving sensitivity and accuracy in the online phase.

### Entity Summary

- **Hardware / medium:** 3-D wireless sensor network; **9 sensing nodes** at known coordinates; one unknown source; RF/RSS medium. Simulated, not built.
- **Software:** Tensor-completion solver over the incomplete RSS tensor; kernel-learning matcher. No implementation or code released.
- **Algorithm:** Low-rank tensor completion for fingerprint-database recovery + kernel-based matching, combined in their Algorithm 1 ("Efficient Kernel-based Localization via Tensor Completion").
- **Accuracy:** ~**0.8 m** position precision for the kernel-matching schemes once the **sampling rate exceeds 0.5**. Across the swept sampling range (0.2–0.8) RMSE spans roughly 1–3.5 m. Their headline efficiency claim: performance comparable to using the complete database "while the overhead was almost cut in a half".
- **Evaluation:** Simulation only. **20 × 20 × 20 m** cube divided into **10 × 10 × 10** grids (each ≈ 2 × 2 × 2 m); source placed at random; transmit power P_S = 0 dBm; path-loss exponent **γ = 3**; zero-mean AWGN; sampling rate **0.5**, samples chosen at random.
- **Ground truth:** Known simulated source positions and known sensing-node coordinates.
- **Metrics:** RMSE of the estimated source position, swept against data sampling rate.
- **Baseline:** Five schemes compared, which is unusually careful for the claim being made — **OD-Kernel** (complete data + kernel, the upper bound), **TC-Kernel** (proposed), **IC-Kernel** (interpolation completion + kernel), **TC-KNN** and **IC-KNN** (both completions with KNN matching instead). The two-factor design separates the contribution of the completion step from that of the kernel step.

### The limitation the authors report themselves

At a **0.2 sampling rate the proposed TC-Kernel scheme performs *worse* than the simpler IC-Kernel interpolation baseline**, and the advantage of tensor completion over interpolation "disappeared gradually as the sampling rate decreased". Their explanation: too little measured data "exceeds the tolerance of tensor completion", and the kernel matcher is more sensitive than KNN in harsh cases. So the method's whole premise — measure less — has a floor, and below it a cruder method wins. Reported plainly by the authors rather than buried, and it is the most useful sentence in the paper for anyone deciding whether to use this.

---

## Contradictions / Scope Notes

- The two works use "compressive" at **different stages**: Tabibiazar & Basir apply sparsity to the **spatial-position** unknown (grid occupancy), whereas Balestrieri et al. apply compression to the **signal-acquisition** stage (sub-Nyquist wideband sampling) and then localize emitters by conventional multilateration. Both are legitimately "compressive sensing for localization" but are not directly comparable head-to-head.
- Accuracy figures are not directly comparable: Tabibiazar reports a *normalized* distance error under simulation, while Balestrieri reports *metric* RMSE (~1 m with 10 receivers). Lu et al. report metric RMSE (~0.8 m) but in a 3-D simulated cube with a 2 m grid, so their figure is bounded by grid resolution rather than by the estimator.
- **Recover-the-gaps and choose-the-points fail in opposite regimes, and the corpus now holds both sides.** Lu et al. sample *at random* and repair the database afterwards, and their method degrades below plain interpolation once the sampling rate drops to 0.2. Li, Al-Tous & Tirkkonen (2026, compiled in [[WiFi-Fingerprinting-Advances]]) attack the same measurement-cost problem from the other end: spend the same budget on *deliberately chosen* locations, guided by Kriging variance, and they report matching a 1000-point random campaign with 300 adaptive points. The two are not in direct conflict — different features (RSS vs high-dimensional CSI), different dimensionality, no shared testbed — but they answer the same question differently. **The combination already exists in the corpus:** Liu et al. (2016), also in [[WiFi-Fingerprinting-Advances]], does informed sampling *and* tensor completion together on RSS, reporting 71% sample reduction at high SNR — and it is precisely the paper Li et al. cite as not generalising to high-dimensional CSI. So the open question is narrower than it first looks: not whether to combine them, but whether Liu's combination survives the move from scalar RSS to CSI features.

---

## References (preserved for deeper research)

- Tabibiazar & Basir cite the Data Compression Conference (DCC 2010, pp. 356–365) among CS foundations; full reference list retained in `raw/tabibiazar2011.pdf`.
- Balestrieri et al. compare against ML, SDP-DRSS, WTLS-URSS/WTLS-DRSS methods (their ref [21]) and ESPRIT-class parameter estimation; full reference list retained in `raw/balestrieri2020.pdf`.
- Lu et al. cite the signature-map-construction literature (their refs [21]–[26]) as the prior art for completing a measurement matrix, and an interpolation-completion method (their ref [29]) which they implement as the IC baseline; full reference list retained in `raw/TIIS Vol 13, No 1-12.pdf`.

## Relationships

- [[WiFi-Fingerprinting-Advances]] — contains the fingerprinting-side "Sparsity-Based Localization (Compressive Sensing)" reformulation (user position over RPs is sparse) and the related radio-map **matrix-completion / low-rank recovery** methods (Liu 2016, Tan 2020, Wang 2021), plus the CDL/CHISEL compression work; this article is the dedicated CS home cross-linked from there.
- [[Indoor-Localization-ML-Methods]] — ℓ₁ / sparse recovery sits alongside the mathematical-methods taxonomy (deterministic, probabilistic, geometric, optimization) surveyed there.
- [[UWSN-Localization]] — Tabibiazar & Basir and Lu et al. both frame their problem as wireless-sensor-network localization; CS and tensor completion are alternatives to the ToA/TDoA/AoA/DV-Hop family used underwater. Lu et al.'s 3-D framing (UAVs, air-ground topography) is the aerial counterpart to the underwater 3-D geometry treated there.
- [[Indoor-IPS-Datasets]] — the measurement-cost problem all three sources attack is the reason public radio-map datasets matter; a completed or adaptively-sampled database is only as good as the campaign behind it.
- [[Indoor-Location-Sensor-Technologies]] — RF/RSS and wideband spectrum sensing are the underlying media exploited by both CS methods.

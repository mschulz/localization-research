---
tags: [indoor-localization, compressive-sensing, compressive-sampling, sparse-recovery, l1-minimization, RSS, RF-emitter-localization, wireless-sensor-networks, dictionary, non-uniform-sampling]
date-compiled: 2026-08-14
source-files:
  - "raw/tabibiazar2011.pdf"
  - "raw/balestrieri2020.pdf"
status: active
---

# Compressive Sensing / Compressive Sampling for Localization

## Summary

Compressive Sensing (CS) exploits the fact that, although a localization problem lives in a high-dimensional space (many candidate grid points, or a wide RF band), the *solution* is **sparse** — a target occupies only one (or a few) of the candidate positions, and an RF spectrum is occupied by only a few emitters. CS theory states that such sparse signals can be recovered exactly from **far fewer measurements than the Nyquist rate** by solving an under-determined linear system with an ℓ₁-norm (sparsity-promoting) constraint. Two distinct uses appear in the sources compiled here:

1. **CS for target/sensor position estimation** — Tabibiazar & Basir (2011) recast wireless-sensor-network localization as sparse signal recovery over a discretized spatial grid, recovering a target's position from a small number of inconsistent RSS/ToA measurements against known anchors.
2. **Compressive *Sampling* for RF-emitter localization** — Balestrieri et al. (2020) apply CS at the *acquisition* stage: Wideband Spectrum Sensors sample a wide RF band far below Nyquist (Non-Uniform Sampling), relaxing throughput/data-transmission requirements while still localizing RF emitters via received-signal energy at spatially distributed receivers.

Both share the core CS machinery (sparsifying dictionary Ψ, sensing matrix Φ, dictionary coherence µ(Ψ), and the measurement bound M ≥ O(K·log(N/K)) for K-sparse recovery from N candidates). This article is the dedicated home for the CS approach; the fingerprinting-side sparse-recovery reformulation and closely related radio-map matrix-completion methods are covered in [[WiFi-Fingerprinting-Advances]].

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

## Contradictions / Scope Notes

- The two works use "compressive" at **different stages**: Tabibiazar & Basir apply sparsity to the **spatial-position** unknown (grid occupancy), whereas Balestrieri et al. apply compression to the **signal-acquisition** stage (sub-Nyquist wideband sampling) and then localize emitters by conventional multilateration. Both are legitimately "compressive sensing for localization" but are not directly comparable head-to-head.
- Accuracy figures are not directly comparable: Tabibiazar reports a *normalized* distance error under simulation, while Balestrieri reports *metric* RMSE (~1 m with 10 receivers).

---

## References (preserved for deeper research)

- Tabibiazar & Basir cite the Data Compression Conference (DCC 2010, pp. 356–365) among CS foundations; full reference list retained in `raw/tabibiazar2011.pdf`.
- Balestrieri et al. compare against ML, SDP-DRSS, WTLS-URSS/WTLS-DRSS methods (their ref [21]) and ESPRIT-class parameter estimation; full reference list retained in `raw/balestrieri2020.pdf`.

## Relationships

- [[WiFi-Fingerprinting-Advances]] — contains the fingerprinting-side "Sparsity-Based Localization (Compressive Sensing)" reformulation (user position over RPs is sparse) and the related radio-map **matrix-completion / low-rank recovery** methods (Liu 2016, Tan 2020, Wang 2021), plus the CDL/CHISEL compression work; this article is the dedicated CS home cross-linked from there.
- [[Indoor-Localization-ML-Methods]] — ℓ₁ / sparse recovery sits alongside the mathematical-methods taxonomy (deterministic, probabilistic, geometric, optimization) surveyed there.
- [[UWSN-Localization]] — Tabibiazar & Basir frame their problem as wireless-sensor-network localization; CS is an alternative to the ToA/TDoA/AoA/DV-Hop family used underwater.
- [[Indoor-Location-Sensor-Technologies]] — RF/RSS and wideband spectrum sensing are the underlying media exploited by both CS methods.

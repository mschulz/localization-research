---
tags: [indoor-localization, UWB, LSTM, deep-learning, TOA, NLOS, ranging]
date-compiled: 2026-06-14
source-files: ["raw/UWB_Indoor_Localization_Using_Deep_Learning_LSTM_N.pdf"]
status: active
---

# UWB Indoor Localization Using Deep Learning LSTM Networks

## Summary

Poulose, A. & Han, D.S. *UWB Indoor Localization Using Deep Learning LSTM Networks.* Applied Sciences 2020, 10, 6290. Kyungpook National University, South Korea.

This paper proposes a deep learning approach to UWB indoor localization that replaces conventional geometric algorithms with a two-layer LSTM network. The LSTM receives anchor-to-tag distance estimates from a TOA-distance model and predicts the user's 2D position directly. Through systematic hyperparameter tuning, the optimized LSTM achieves a **7 cm mean localization error** — superior to all conventional UWB localization methods tested (LLSE, FPE, MLE, WCE) and to other deep learning models (BP, ELM, RNN). This is the source of the 7cm UWB accuracy figure cited in [[Indoor-Localization-ML-Methods]] and [[Indoor-Location-Sensor-Technologies]].

## The UWB Localization Problem

UWB (Ultra-Wide Band) is a short-range radio technology using very short pulses (0.5 ns width in this work) that span a wide frequency band. Its key properties for localization:

- **Penetrating characteristics**: UWB pulses pass through walls and furniture with less multipath distortion than narrowband signals, enabling centimetre-scale ranging
- **TOA ranging**: Anchors measure the time-of-arrival (TOA) of received UWB pulses from the tag; TOA × speed-of-light = distance
- **Error sources**: Multipath, NLOS conditions, clock drift, and channel fading all introduce TOA errors → distance errors → position errors
- **Geometric algorithms** (trilateration, LLSE, MLE) amplify these distance errors; they are sensitive to anchor geometry and do not learn from error patterns

## Why LSTM?

LSTM (Long Short-Term Memory) networks are a recurrent deep learning architecture suited to sequential, time-series data. For UWB localization:

- **Feedback loop / memory**: Unlike feed-forward networks (MLP, BP), LSTM has recurrent connections that act as memory, capturing temporal correlations in anchor-tag distances over successive position estimates
- **Constant error backpropagation**: LSTM avoids the vanishing gradient problem through gated memory cells, enabling learning over long time lags
- **Flexible input control**: LSTM gates (input, forget, output) selectively retain or discard information from previous time steps
- **Non-linear mapping**: Learns the complex mapping from noisy distance vectors to positions without requiring an analytical propagation model

## System Architecture

```
UWB Anchors (×3) → Gaussian pulse transmission
     ↓
TOA-Distance Model → estimated distances d₁, d₂, d₃ from anchors
     ↓
Two-layer LSTM network (input: [d₁, d₂, d₃] → output: [x̂, ŷ])
     ↓
Predicted user position (x̂, ŷ)
```

### Simulation Setup

| Parameter | Value |
|---|---|
| Pulse shape | Gaussian pulse |
| Pulse width | 0.5 ns |
| Sample rate | 20 GHz |
| Number of anchors | 3 (positions: [5,18], [18,5], [18,18] m) |
| Tag starting position | (0, 2) m |
| Tag motion | Rectangular random trajectory |
| SNR range | 3 dB |
| Dataset size | 2,582 samples; 75% train / 25% test |

### LSTM Hyperparameters (optimized)

| Parameter | Optimized Value | Notes |
|---|---|---|
| Learning rate | 0.0001 | Lower = more stable; very low = fails to converge |
| Optimizer | **Nadam** | Best among SGD, RMSProp, Adagrad, Adam, Nadam, Adadelta, Adamax |
| Loss function | **MSE** (Mean Squared Error) | MSLE slightly better but prone to overfitting |
| Batch size | 5 (small) | Lower batch → faster convergence; uses less memory |
| Timesteps | 30 | Length of input sequence |
| Hidden nodes | 256 | Per LSTM layer |
| LSTM layers | **2** | More layers → overfitting; fewer → underpowered |
| Dropout rate | 0.2 | Regularization |
| Epochs | 100 | |

## Evaluation Results

### Comparison with Conventional UWB Algorithms

| Method | Mean Localization Error | Notes |
|---|---|---|
| **LSTM (proposed, 2 layers)** | **7 cm** | Best result |
| RNN | ~15 cm (approx.) | Recurrent but no gated memory |
| ELM (Extreme Learning Machine) | ~20 cm (approx.) | Fast training; generalises poorly |
| BP (Backpropagation NN) | ~25 cm (approx.) | Standard MLP; worst DL method |
| FPE (Fingerprint Estimation) | Less than LLSE | Fingerprinting uncommon for UWB |
| LLSE (Linearized Least Squares) | Higher than DL methods | Most common conventional approach |
| MLE (Maximum Likelihood) | Higher than LLSE | Worse than LLSE here |
| WCE (Weighted Centroid) | Highest error | Not suitable for this scenario |

### Effect of Number of Anchors

Performance improves monotonically with more anchors for all methods. LSTM maintains the best relative performance at all anchor counts (3–5 tested).

### Training and Testing Time

| Model | Training time (s) | Testing time (s) |
|---|---|---|
| BP | 740 | 0.287 |
| ELM | 520 | 0.215 |
| **LSTM** | **1080** | **0.795** |
| RNN | 900 | 0.321 |

LSTM has the highest training time but testing time (0.795s for full test set) is acceptable for real-time localization.

### Error metric

Mean localization error `E = (1/L) Σᵢ √[(xᵢᵗʳᵘᵉ - xᵢᵉˢᵗ)² + (yᵢᵗʳᵘᵉ - yᵢᵉˢᵗ)²]`

## Limitations

- **Simulation only**: Results are from MATLAB simulation, not a physical testbed with real UWB hardware. Real-world multipath, NLOS, and clock errors are more complex than the Gaussian channel model used.
- **Small dataset**: 2,582 samples may be insufficient to generalise across different environments, anchor geometries, or user trajectories.
- **2D only**: Position estimated in x-y plane; 3D extension not addressed.
- **Three anchors**: Minimum for 2D trilateration; real deployments need more anchors to handle NLOS outages on individual links.
- **No hardware implementation**: Authors note intent to implement CNN-LSTM in future work.
- **Propagation model assumptions**: Gaussian channel may not capture real NLOS bias, which is systematic (not symmetric noise).

## Entity Summary

### Hardware
- **Medium**: UWB radio (Gaussian pulses, 0.5 ns, 20 GHz sample rate, 3 dB SNR)
- **Device**: Simulation only — no physical hardware tested
- **Infrastructure**: 3 UWB anchor nodes; 1 UWB tag

### Software
- **Platform**: MATLAB R2019b (MathWorks)
- **DL framework**: Custom LSTM implementation in MATLAB
- **Availability**: Simulation code not released

### Algorithm
- **TOA-distance model**: Convert TOA measurements to anchor-tag distances via speed-of-light scaling
- **Two-layer LSTM**: 256 hidden nodes per layer; timesteps=30; Nadam optimizer; MSE loss; dropout 0.2
- **Position prediction**: LSTM maps distance vector [d₁, d₂, d₃] → position [x̂, ŷ]
- **Hyperparameter tuning**: Grid search over learning rate, optimizer, loss function, batch size, hidden nodes, timesteps, number of layers

### Accuracy
- **7 cm mean error**: Best result; simulation environment; 3 anchors; Gaussian channel
- **Context**: Compared to CNN-based UWB (17.3 cm MAE for NLOS from related work [22])
- **Comparison basis**: Mean Euclidean localization error over 25% test split

## References

| # | Reference | In raw/ |
|---|---|---|
| [4] | Hochreiter, S.; Schmidhuber, J. *Long short-term memory.* Neural Comput. 1997. | — |
| [5] | Joung et al. *CNN-based Tx-Rx distance estimation for UWB.* Electron. Lett. 2019. | — |
| [6] | Bregar, K.; Mohorčič, M. *Improving indoor localization using CNN on restricted devices.* IEEE Access 2018. | — |
| [7] | Luo, J.; Gao, H. *Deep belief networks for UWB fingerprinting.* Int. J. Distrib. Sens. Netw. 2016. | — |
| [9] | Poulose et al. *Localization Error Analysis of UWB-based IPS.* ICUFN 2019. | — |
| [10] | Alsindi et al. *Measurement and modeling of UWB TOA-based ranging.* IEEE TVT 2008. | — |
| [22] | (CNN for UWB NLOS; 17.3 cm MAE) | — |

## Relationships

- [[Indoor-Localization-ML-Methods]] — this paper is the primary source for the 7cm UWB+LSTM accuracy figure; LSTM theory and UWB ML methods covered in depth there
- [[Indoor-Location-Sensor-Technologies]] — UWB technology overview (hardware, TOA, RTOF, multipath characteristics) covered there
- [[Indoor-IPS-Datasets]] — UWB datasets for benchmarking covered there
- [[Indoor-Localization-Applications-by-Sector]] — UWB industrial applications (7cm accuracy) cited from this paper
- [[UWSN-Localization]] — TOA/TDOA ranging methods also used in UWSN context

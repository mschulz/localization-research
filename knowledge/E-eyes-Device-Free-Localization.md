---
tags: [device-free, CSI, WiFi, activity-recognition, indoor-localization, fingerprinting, OFDM]
date-compiled: 2026-06-14
source-files: ["raw/Eeyes mobicom14 wang.pdf", "raw/Through-the-wall.pdf"]
status: active
---

# E-eyes — Device-Free Location-Oriented Activity Identification

## Summary

E-eyes is a device-free indoor activity identification system developed by Wang, Liu, Chen, Gruteser, Yang, and Liu at Stevens Institute of Technology / Rutgers University (published at ACM MobiCom 2014). It uses **Channel State Information (CSI)** from off-the-shelf 802.11n WiFi devices — rather than RSSI — to distinguish in-place daily activities and walking trajectories without requiring the tracked person to carry any device. It achieves >96% average true positive rate and <1% false positive rate for 8 walking activities and 9 daily activities using only a single WiFi access point. E-eyes is the canonical paper establishing CSI-based device-free localization as a practical approach and is directly cited in [[Indoor-Localization-ML-Methods]] as the foundational device-free paper.

## Core Insight: CSI vs. RSSI

Traditional indoor localization uses **RSSI** (Received Signal Strength Indication) — a single scalar per packet representing average SINR across the channel bandwidth. RSSI is insufficiently sensitive to fine-grained movements:

- **CSI** (Channel State Information) provides amplitude and phase measurements **per OFDM subcarrier**
- 802.11n at 20MHz: 52 subcarriers; at 40MHz: 128 subcarriers; 802.11ac (80MHz): 242 subcarriers
- Adjacent subcarriers experience *different* multipath fading (subcarrier width ≈ coherence bandwidth 1/D, where D = multipath delay spread)
- A small body movement alters the multipath environment — visible in individual subcarrier CSI even when averaged RSSI is unchanged

Key demonstration: two activities at the same physical location (talking on phone vs. washing dishes) produce nearly identical RSSI histograms but clearly distinct per-subcarrier CSI amplitude distributions.

## Activity Types and Location-Oriented Approach

E-eyes classifies activities into two categories:

1. **In-place activities**: Cooking, eating, exercising, working at a desk, sleeping — occur at a fixed location; produce stable CSI amplitude distributions with activity-specific patterns
2. **Walking activities**: Movements between rooms, passing doorways — produce time-varying CSI amplitude sequences; trajectory is the distinguishing feature

Key principle: many daily activities are **location-oriented** — cooking occurs in the kitchen, brushing teeth at the sink. The activity profile therefore encodes both location and activity signature. The same activity at different locations can be recognised with separate profiles.

## System Architecture

### Data Collection
- Intel WiFi Link 5300 NIC at the access point measures CSI
- Collects amplitude time series per subcarrier per link, over links to stationary WiFi devices (TVs, appliances, desktops)
- Can use existing network traffic or generate periodic probe traffic if insufficient traffic available

### Preprocessing
- **Low-pass filter** (Dynamic Exponential Smoothing Filter, DESF): removes high-frequency noise not attributable to human movement
- **MCS index filter**: removes packets where rate adaptation changed the Modulation and Coding Scheme (which would alter CSI values artifactually)

### Activity Identification Pipeline

**Step 1 — Activity Type Discrimination**: Moving variance of CSI across all subcarriers over a sliding window distinguishes walking (high variance, sustained) from in-place (low variance, stable patterns).

**Step 2a — In-place Activity Identification**:
- CSI amplitude distribution (histogram over time window) per subcarrier per link is computed
- Compared against known activity profiles using **Earth Mover's Distance (EMD)** — measures minimum cost to transform one distribution into another
- Classification: assign to activity with minimum EMD; confirm if EMD < threshold; else label "unknown"
- Multi-link fusion: predictions from multiple WiFi device links are fused for robustness

**Step 2b — Walking Activity Tracking**:
- CSI time series for walking paths compared using **Multi-Dimensional Dynamic Time Warping (MD-DTW)**
- DTW handles variation in walking speed across trials by elastic alignment
- Vector norm distance matrix: d(cᵢ, c'ⱼ) = Σₚ (cᵢ(p) − c'ⱼ(p))²
- **Doorway discrimination**: when path is unknown, identify which doorway was traversed (using EMD on doorway CSI profiles) to constrain the activity set

### Profile Construction and Adaptation
- **Offline**: Manual labelled collection of CSI for each known activity
- **Semi-supervised / adaptive**: Non-profiling clustering (K-means on EMD distance vectors) discovers activity instances from unlabelled CSI data; user labels clusters; profiles auto-update when significant changes detected (e.g., device moved)

## Experimental Evaluation

**Testbeds**: Two apartments of different sizes over a 4-month period
- 1-bedroom apartment
- 2-bedroom apartment

**Results** (single AP with 3 connected devices):
- 8 walking activities (20 rounds each): **>96% average detection rate**
- 9 daily in-place activities (50 rounds each): **>96% average detection rate**
- False positive rate: **<1%**
- Single device (1 connected device): still achieves ~92% detection

**Doorway detection accuracy** (prerequisite for walking recognition): **>96.25%** average across 8 trajectories

**802.11ac (wider band) extension**: Using two 40MHz channels to emulate 80MHz provides more subcarriers → higher in-place activity accuracy, particularly for activities with subtle movement differences.

## Limitations

- Designed and tested for **single occupant** — multiple persons require combinatorial profile expansion
- **No pets or furniture movement** assumed during profiling; environment changes trigger profile re-build
- **Static environment assumption** (no furniture rearrangement)
- System is **location-specific** — profiles must be rebuilt for different homes
- CSI tool (iwlwifi with kernel patches) not universally available on all WiFi chipsets

## Entity Summary

### Hardware
- **Medium**: WiFi 2.4/5GHz radio; specifically 802.11n CSI (amplitude + phase per OFDM subcarrier)
- **Access point NIC**: Intel WiFi Link 5300 (IWL5300) — supports CSI extraction via modified driver; standard commodity hardware
- **Transmitting devices**: Stationary home WiFi devices (TVs, desktops, appliances) — no special hardware required; use existing home network
- **No device on tracked person** — fully device-free

### Software
- **CSI extraction**: Modified Intel iwlwifi Linux driver (Linux Wireless tool) to expose per-subcarrier CSI — research community tool, open-source patch available
- **Algorithms implemented**: DESF filter, EMD classifier, MD-DTW walking matcher, K-means adaptive clustering
- **Language/platform**: Linux, C/C++ (inferred from driver work); research prototype
- **Availability**: Research prototype; Intel 5300 CSI tool publicly available; full E-eyes system not released as open-source product

### Algorithm
- **Activity type**: Moving variance thresholding over CSI subcarrier amplitude time series
- **In-place recognition**: Earth Mover's Distance (EMD) on per-subcarrier CSI amplitude histograms vs. stored profiles; minimum-EMD classification with threshold confirmation
- **Walking recognition**: Multi-Dimensional Dynamic Time Warping (MD-DTW) on CSI time series; elastic alignment for speed variation
- **Doorway detection**: EMD on CSI profiles of doorway crossings; supplements path discrimination
- **Profile adaptation**: K-means clustering on EMD distance vectors → semi-supervised profile construction; adaptive update on profile drift detection
- **Multi-link fusion**: Data from multiple WiFi device links fused for robustness

### Accuracy
- **In-place activities**: >96% true positive rate, <1% false positive rate (9 activities, 50 rounds each)
- **Walking activities**: >96% detection rate (8 trajectories, 20 rounds each)
- **Doorway detection**: >96.25% average
- **Single device**: ~92% true positive rate
- **Comparison**: Outperforms RSSI-based device-free systems (DfP: several metres accuracy, can't distinguish activities at same location); uses far fewer transceivers than RTI (requires tens/hundreds of sensors)
- **Limitation**: Evaluated in residential environments with single occupant; performance in complex multi-person scenarios not characterised

## References

| # | Reference | Priority |
|---|---|---|
| [5] | Adib, F., Katabi, D. *See Through Walls with Wi-Fi.* ACM SIGCOMM 2013 (Wi-Vi). | High — related gesture detection |
| [25] | Pu, Q. et al. *Whole-Home Gesture Recognition Using Wireless Signals.* MobiCom 2013 (WiSee). | High — gesture recognition comparison |
| [4] | Adib, F., Katabi, D. *3D Tracking via Body Radio Reflections.* NSDI 2014 (WiTrack). | High — USRP-based tracking |
| [35] | Wilson, J., Patwari, N. *Radio Tomographic Imaging with Wireless Networks.* IEEE Trans. Mobile Comput. 2010 (RTI). | ⭐ High — foundational device-free |
| [19] | Moussa, M., Youssef, M. *Smart Devices for Smart Environments: Device-free Passive Localization.* PerCom 2009. | High — DfP localization |
| [7] | Youssef, M. et al. *The Horus WLAN Location Determination System.* MobiSys 2005. | ⭐ High — fingerprinting predecessor |
| [6] | Wang, Y., Liu, J., Chen, Y. et al. *E-eyes.* MobiCom 2014. | — (self-reference) |

## Related Work: Through-Wall Doppler Radar (Narayanan et al., Penn State 2010)

Narayanan, R.M., et al. *Through-Wall Doppler Radar for Human Presence Detection.* Penn State University, 2010. Source: `raw/Through-the-wall.pdf`.

A device-free sensing approach predating E-eyes that uses **continuous-wave Doppler radar at 750 MHz** to detect human presence and motion through walls. Unlike E-eyes (which exploits WiFi CSI), this system uses a dedicated radar transmitter and receiver.

**Key techniques**:
- **Medium**: 750 MHz continuous-wave RF (penetrates standard building walls)
- **Signal processing**: Empirical Mode Decomposition (EMD) applied to the Doppler return signal to extract micro-Doppler signatures — the subtle frequency shifts caused by breathing motion (~0.3 Hz) and arm/body movements
- **Feature extraction**: Breathing signatures produce very low-frequency Doppler modulation; arm movements produce higher-frequency modulation; EMD separates these components without assuming a fixed frequency band
- **Classification**: Breathing vs. arm movement vs. absence detected from micro-Doppler feature vectors

**Comparison with E-eyes / CSI approaches**:
- Requires dedicated radar hardware (not passive reuse of existing WiFi)
- Works through opaque walls (E-eyes also penetrates walls via WiFi, but has lower structural penetration)
- Frequency resolution of 750 MHz enables wall penetration; higher frequency radars (e.g., mmWave in Milli-RIO) have shorter penetration depth
- EMD-based feature extraction is parameter-free compared to STFT/wavelet approaches requiring fixed window sizes

**Source**: `raw/Through-the-wall.pdf`

## Relationships

- [[Indoor-Localization-ML-Methods]] — E-eyes is the canonical device-free localization paper; CSI + EMD/DTW methods described there
- [[Indoor-Location-Sensor-Technologies]] — CSI (WiFi) as a sensor; E-eyes demonstrates device-free use of existing WiFi infrastructure
- [[Horus-WLAN-System]] — device-based predecessor; Horus requires tracked person to carry WiFi device
- [[RADAR-WiFi-Fingerprinting]] — foundational WiFi localization (RSSI-based); E-eyes extends to CSI for device-free recognition
- [[Indoor-Localization-Applications-by-Sector]] — elder care, well-being monitoring, child safety — application sectors for device-free HAR

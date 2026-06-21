---
tags: [magnetic-field, geomagnetic, fingerprinting, SLAM, DTW, indoor-localization, smartphone, PDR, dead-reckoning, infrastructure-free, neural-network, particle-filter]
date-compiled: 2026-06-14
source-files:
  - "raw/electronics-11-00864.pdf"
status: active
---

# Magnetic-Field-Based Indoor Localization — Survey

## Summary

Ouyang, G. and Abed-Meraim, K. (Université d'Orléans, PRISME Laboratory) published a comprehensive 47-page survey of magnetic-field-based indoor localization in *Electronics* 2022, 11, 864. The survey covers the full pipeline from physical principles and sensor modelling through coordinate systems, calibration, map construction, and six families of localization algorithms (magnetic landmarks, DTW, machine learning, filters, SLAM, neural networks), concluding with smartphone-based pedestrian dead reckoning. Magnetic field localization is uniquely infrastructure-free — it exploits the Earth's geomagnetic field distorted by ferromagnetic building materials (reinforced concrete, metal doors, HVAC ductwork) to produce location-dependent magnetic "fingerprints" without any installed beacons. The global indoor positioning market was growing at 22.5% CAGR towards USD 17 billion by 2025 at the time of writing; magnetic field approaches compete with WiFi, BLE, and UWB but require no additional infrastructure.

## Why Magnetic Fields for Localization

### Physical Basis
The Earth's geomagnetic field has a global magnitude of 25–65 µT and a relatively uniform direction at large scales. Inside buildings, ferromagnetic materials (structural steel, reinforced concrete, metal furniture, HVAC ducts, lifts) locally distort the ambient magnetic field, creating **location-specific magnetic anomalies** that are:
- **Stable over time** — building structure changes rarely; anomalies persist for months to years
- **Unique** — different points in the same building exhibit distinctive magnetic signatures
- **Infrastructure-free** — no beacons, access points, or installed hardware required
- **Penetrating** — magnetic fields pass through walls, unaffected by glass, wood, and non-ferromagnetic partitions

### Challenges
- **Smartphone heterogeneity**: Different smartphone models have different magnetometer sensitivities and orientations; hard iron and soft iron effects vary by device
- **Low discernibility**: Corridors or large open areas with similar structural materials may have similar magnetic signatures, making discrimination difficult
- **Orientation dependence**: Magnetometer readings change with phone orientation; systems must handle or normalise for user orientation
- **Environmental change**: Moving large ferromagnetic objects (furniture, machinery) alters local magnetic fingerprints; maintenance of fingerprint databases required

## Sensor Model and Calibration

### Magnetometer Measurement Model
The raw magnetometer output is affected by:
- **Hard iron effect**: Constant offset caused by permanently magnetised components in the device (magnets in speakers, camera modules)
- **Soft iron effect**: Anisotropic scaling/rotation of the magnetic field due to nearby soft-magnetic materials that become magnetised in the Earth's field
- **Scale factor and misalignment errors**: Manufacturing imperfections in the tri-axis sensor

Combined measurement model:
```
mb = A · Rbn · mn + b + ε
```
where mn is the local magnetic field (world frame), Rbn is the device orientation rotation matrix, A is the combined scale/misalignment/soft-iron distortion matrix, b is the hard iron + sensor bias offset, and ε is Gaussian noise.

### Calibration Algorithms
| Algorithm | Approach | Accuracy | Cost |
|---|---|---|---|
| Unconstrained least squares | Ellipsoid fitting — collect magnetometer data in all orientations | Moderate | Low |
| Constrained least squares | Constrains A to satisfy physical properties | Higher | Moderate |
| Maximum likelihood estimation (MLE) | Optimal under Gaussian noise assumption | Highest | High |
| Extended Kalman filter | Online calibration, updates during use | Moderate-high | Real-time capable |

The EKF approach is best for embedded/smartphone deployment as it calibrates continuously without a separate calibration phase.

## Coordinate Systems

The survey reviews five coordinate systems relevant to magnetic positioning:
1. **ECEF** (Earth-Centered Earth-Fixed): global Cartesian system rotating with Earth
2. **Geodetic** (WGS 84): latitude/longitude/altitude — standard GNSS system
3. **Local ENU** (East-North-Up): local Cartesian system aligned to compass headings
4. **Smartphone coordinate system**: body-fixed frame aligned to phone screen
5. **9-DOF sensor frame**: IMU-specific frame; differs from smartphone frame by a calibration rotation

Accurate transformation between frames is essential when fusing magnetometer data with accelerometer and gyroscope data for PDR + magnetic positioning.

## Benchmark Datasets

| Dataset | Area | Sensors | Notes |
|---|---|---|---|
| MagWi (2021) | Multi-room | Mag, WiFi | 5 smartphones (Galaxy S8, LG G6, etc.); device heterogeneity study |
| UJIIndoorLoc-Mag (2015) | Multi-building | Mag, WiFi | Combined magnetic+WiFi fingerprints |
| Barsocchi et al. IPIN 2016 | 185 m² | Mag, Accel, Gyro | 36,795 samples; corridors and turns |
| IPIN Competition datasets | 960 m² | Mag, Accel, Gyro | 3 buildings; fixed + moving object effects |

The survey notes that Microsoft Indoor Localization Competition datasets also contain magnetic field data and are widely used as benchmarks. See [[Indoor-IPS-Datasets]] for the competition overview.

## Magnetic Map Construction

Three approaches to building the fingerprint database:

**Expert site survey**: Manual collection at fixed grid points by a trained surveyor — most accurate but labour-intensive. Accuracy directly proportional to density of measurement points.

**Crowdsourcing**: User contributions during normal building use, aligned with PDR trajectories. Accuracy 70–95% within 2–4m; lower than expert survey but scalable. Key challenge: trajectory alignment across multiple users with differing walking speeds.

**SLAM-based (PFSurvey, MagSLAM)**: Use particle filters + inertial sensing to rapidly survey an entire building by walking through it once. PFSurvey achieves trajectories within 1.1m (90th percentile) compared to ground truth. Reduces survey cost dramatically while maintaining usable accuracy.

**Gaussian Process interpolation**: Mathematical interpolation of magnetic field values between measured points using GP regression; exploits the divergence-free and curl-free properties (Maxwell's equations) to impose physical constraints on the interpolated field. Enables finer-grained maps without proportionally more measurements.

## Localisation Methods

### 8.1 Magnetic Landmarks

Ferromagnetic objects (lifts, metal doors, HVAC) create distinctive, repeatable magnetic anomalies that can be used as **landmarks** — fixed reference points that anchor a trajectory:

- **[[UnLoc-Unsupervised-Localization]]**: uses magnetic anomalies as one landmark type; achieves 1.69m median error with PDR + landmark fusion
- **SemanticSLAM**: extends UnLoc with SLAM framework; magnetic + accelerometer stair signatures + WiFi anomalies; 0.53m median error
- **ALIMC**: activity-landmark-based mapping; 0.8–1.5m within 80th percentile

Landmark limitations: detection requires per-site threshold tuning; multiple nearby landmarks cause data association ambiguity; open doors remove door-based landmarks.

### 8.2 Dynamic Time Warping (DTW)

DTW matches a measured magnetic field *sequence* to stored trajectory sequences, elastically warping the time axis to align trajectories taken at different speeds:

- Particularly suited for corridor navigation: match the magnetic field trace of a corridor walk to known corridor fingerprint sequences
- **3DDTW** (Chen et al.): extends 1D DTW to use 3D magnetic vector (magnitude + inclination + azimuth); hybrid with weighted least squares achieves ~3.3m accuracy
- **CSMS** (Li et al.): fuses CSI (WiFi) + magnetic field; initial KNN localisation, then Local DTW for trajectory tracking → improved accuracy over either modality alone

### 8.3 Machine Learning

Two-phase (offline training + online prediction) system:

**Feature engineering**: Raw 3D magnetic vector → magnitude, inclination, azimuth, plus derived statistics (mean, variance over window). PCA or SVD for dimensionality reduction.

**Classifiers used**:
- KNN (k-nearest neighbour) — most common baseline
- Fuzzy KNN — assigns membership confidence rather than hard label; lower error rate
- SVM — effective for non-linear decision boundaries in magnetic feature space
- Naive Bayes — fast, works well when features are approximately independent
- Decision trees and discriminant analysis — interpretable but lower accuracy than SVM/KNN

**Performance**: 1–3m typical for machine-learning-based magnetic fingerprinting in office/corridor environments with density ~1m grid.

### 8.4 Filter-Based Methods

**Kalman filter (EKF)**: State vector = (x, y, heading); motion model from PDR (accelerometer + gyroscope); measurement model from magnetic fingerprint database. EKF linearises the non-linear magnetic map lookup. Suitable for real-time tracking.

**Particle filter**: Represents posterior position distribution as a set of N weighted particles; motion model propagates particles; magnetic measurement reweights particles by likelihood of observed magnetic field given particle location; resampling concentrates particles near likely positions. Fusion of PF + EKF for PDR + magnetic fingerprinting achieves 1–2m accuracy in large buildings for slow-walking users (Wang et al.).

### 8.5 SLAM

SLAM simultaneously estimates the user's trajectory and builds or updates the magnetic map:

- **eSLAM**: exponentially weighted particle filter for pose; Kriging interpolation for map update; reveals position shift artefacts after sharp turns
- **MagSLAM**: pedestrian SLAM using magnetic + stride measurements without a priori map; scalable to large buildings
- **Rao-Blackwellized particle filter + Gaussian process**: tractable 3D magnetic SLAM; Kok et al. 2018
- **SemanticSLAM**: magnetic + structural landmarks (lifts, stairs, WiFi marks); 0.53m median, fast convergence

### 8.6 Neural Networks

Survey covers standard deep learning approaches applied to magnetic fingerprinting — CNNs for feature extraction from 2D magnetic field maps, LSTMs for sequential magnetic trajectory matching, autoencoders for representation learning. Performance generally superior to classical ML classifiers when training data is sufficient.

## Smartphone PDR Integration

The survey dedicates Section 9 to smartphone-based [[Pedestrian-Dead-Reckoning]] (PDR) as a complementary technique fused with magnetic localization:
- PDR provides continuous position updates between discrete magnetic fingerprint matches
- Drift accumulation from PDR is corrected at magnetic landmark detections or fingerprint matches
- Typical fusion architecture: particle filter with PDR motion model + magnetic field measurement model
- Crowdsourcing magnetic map construction relies on PDR to align user trajectories

## Entity Summary

### Hardware
- **Medium**: Earth's geomagnetic field (25–65 µT); local anomalies from building ferromagnetic materials
- **Sensor**: Tri-axis magnetometer (3-DOF); commonly integrated in smartphones alongside 6-DOF IMU
- **Infrastructure requirement**: None — fully passive sensing of ambient magnetic field
- **Cost**: Zero additional hardware beyond the user's smartphone

### Software
- **Calibration**: EKF-based online calibration; ellipsoid fitting for offline calibration
- **Localisation**: DTW, KNN/SVM/Bayes classifiers, particle filter, SLAM — all applicable
- **Public datasets**: MagWi, UJIIndoorLoc-Mag, Barsocchi IPIN 2016, IPIN Competition
- **Availability**: Most approaches use standard signal processing and ML libraries (scikit-learn, TensorFlow); no specialised hardware SDK required

### Algorithm
- **Map construction**: Expert survey, crowdsourcing + PDR alignment, SLAM, Gaussian process interpolation
- **Matching**: DTW for trajectory matching; KNN/SVM/Bayes for point-based fingerprint matching; particle filter for sequential tracking; SLAM for simultaneous map building and localisation
- **Fusion**: PDR + magnetic landmark (UnLoc-style); EKF fusion of PDR + fingerprint; PF + EKF hybrid

### Accuracy
- **Magnetic landmarks + PDR (UnLoc)**: 1.69m median
- **SemanticSLAM (magnetic + structural)**: 0.53m median
- **DTW-based (3DDTW)**: ~3.3m hybrid
- **PF + EKF PDR fusion**: 1–2m (large buildings, slow walking)
- **Expert-surveyed fingerprinting (KNN)**: 1–3m typical
- **Crowdsourced**: 70% within 2m, 95% within 4m

### Evaluation
- **Ground truth methods**: Surveyed reference points; motion capture; manual position annotation
- **Metrics**: Mean error, RMSE, CDF (90th percentile error), percentage within distance thresholds
- **Benchmark datasets**: IPIN Competition, UJIIndoorLoc-Mag, MagWi, Barsocchi 2016

### Baseline
- The survey compares against WiFi and BLE fingerprinting as the primary alternatives; magnetic field is complementary (infrastructure-free) rather than superior in all metrics. CSI-magnetic fusion outperforms either modality alone (CSMS system).

## Advantages vs. Challenges

| Advantage | Challenge |
|---|---|
| No infrastructure required | Smartphone heterogeneity (device-specific calibration needed) |
| Stable fingerprints (months–years) | Low discernibility in large open/structurally uniform areas |
| Works with existing smartphone hardware | Orientation sensitivity — must normalise for phone orientation |
| Immune to radio interference (WiFi congestion, etc.) | Ferromagnetic objects moved by occupants alter fingerprints |
| Natural fusion with PDR/IMU | Dense survey grid required for high accuracy |
| Good for multi-building/multi-floor scale | SLAM approaches computationally intensive |

## References

| # | Reference | In raw/ |
|---|---|---|
| [OuY22] | Ouyang & Abed-Meraim, *Electronics* 2022, 11, 864 — **primary source** | ✅ `raw/electronics-11-00864.pdf` |
| [UnLoc] | Wang et al., MobiSys 2012 — UnLoc (magnetic + WiFi + IMU landmarks) | ✅ `raw/unloc.pdf` |
| [PDR] | Woodman, Cambridge PhD 2010 — PDR + ZUPT + WiFi | ✅ `raw/ojw28_thesis.pdf` |
| [Barsocchi16] | Barsocchi et al., IPIN 2016 — WiFi+magnetic+IMU dataset | ✅ `raw/Barsocchi et al. - 2016 - A multisource and multivariate dataset for indoor .pdf \| My Library \| Zote.pdf` |

## Relationships

- [[Indoor-Location-Sensor-Technologies]] — magnetic field is listed as one positioning technology in the broad survey; this article provides the detailed treatment
- [[Pedestrian-Dead-Reckoning]] — PDR (IMU-based dead reckoning) is a key fusion partner for magnetic field localisation; both rely on smartphone inertial sensors
- [[UnLoc-Unsupervised-Localization]] — UnLoc uses magnetic anomalies as one of its organic landmark types; cited directly in this survey as foundational magnetic landmark work
- [[Indoor-IPS-Datasets]] — magnetic field benchmark datasets (MagWi, UJIIndoorLoc-Mag, Barsocchi 2016, IPIN Competition) overlap with the IPS datasets article; Barsocchi 2016 dataset is also in raw/
- [[Indoor-Localization-ML-Methods]] — ML methods applied to magnetic fingerprinting (KNN, SVM, Bayes, neural networks) mirror the ML taxonomy in the general ML-methods article
- [[E-eyes-Device-Free-Localization]] — CSMS system (Li et al.) fuses CSI + magnetic field strength — a direct connection between WiFi CSI and magnetic modalities

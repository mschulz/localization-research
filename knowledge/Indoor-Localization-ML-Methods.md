---
tags: [indoor-localization, machine-learning, fingerprinting, kalman-filter, RSSI, LOS, NLOS, device-free, UWB, BLE, transfer-learning, reinforcement-learning, deep-learning, SLAM, mathematical-methods, bluetooth, neural-networks]
date-compiled: 2026-06-15
source-files:
  - "raw/sensors-21-08086.pdf"
  - "raw/2403.04333v1.pdf"
  - "raw/infocom2000.pdf"
  - "raw/youssef.pdf"
  - "raw/A_Survey_of_Machine_Learning_for_Indoor_Positionin.pdf"
  - "raw/UWB_Indoor_Localization_Using_Deep_Learning_LSTM_N.pdf"
  - "raw/Alhomayani and Mahoor - 2021 - Oversampling Highly Imbalanced Indoor Positioning .pdf | My Library |.pdf"
  - "raw/PDF | My Library | Zotero.pdf"
  - "raw/Sci-Hub. A Survey of Machine Learning Techniques for Indoor Localization and Navigation Systems : Jo.pdf"
  - "raw/Survey_of_Indoor_Localization_Based_on_Deep_Learni.pdf"
  - "raw/singh-machinelearningbased-2021.pdf"
  - "raw/Sci-Hub. A survey of mathematical methods for indoor localization : 2009 IEEE International Symposiu.pdf"
  - "raw/Sci-Hub. Bluetooth indoor localization with multiple neural networks : IEEE 5th International Sympos.pdf"
  - "raw/v1_ CSI_based_Indoor_localization_using_Ensemble_Neural_Networks.pdf"
status: active
---

# Indoor Localization — Machine Learning Methods

## Summary

A survey of machine learning and signal-processing methods applied to indoor localization, drawn from two primary sources:

1. Yang, Cabani & Chafouk, *"A Survey of Recent Indoor Localization Scenarios and Methodologies"*, Sensors 2021, 21, 8086 — classifies approaches into ML-based and filter-based branches.
2. Nessa, Adhikari, Hussain & Fernando, *"A Survey of Machine Learning for Indoor Positioning"*, IEEE Access 2020, vol. 8 — focuses specifically on how ML addresses IPS challenges: NLOS mitigation, fingerprinting enhancement, RSSI fluctuation, trajectory learning, and robot navigation.

Both papers address the fundamental problem that raw RSSI and other signal measurements are corrupted by multipath, interference, and dynamic environmental conditions. Nessa et al. additionally survey transfer learning and reinforcement learning as emerging approaches for scalable and autonomous indoor positioning.

## Why ML Is Needed for Indoor Localization

Classical trilateration (converting RSSI to distance, then computing position geometrically) works well in static, controlled environments, but degrades in realistic indoor spaces because:

- **Multipath effect**: Signals reflect off walls, furniture, people — measured RSSI does not map cleanly to distance
- **Multiple interference**: Competing transmitters and electronic noise corrupt measurements
- **Dynamic environments**: Furniture, people movement, and environmental changes shift the channel conditions over time
- **NLOS (Non-Line of Sight)**: Blocked direct paths cause systematic overestimation of distance

ML methods address these issues by learning mappings from observed (noisy) measurements to positions, either via offline training or online adaptation. See [[Indoor-Location-Sensor-Technologies]] for how LOS/NLOS conditions arise.

## Measurement Inputs

| Input | Description |
|---|---|
| RSSI | Received Signal Strength Indication — most common; cheap, ubiquitous, noisy |
| CSI | Channel State Information — richer descriptor of the channel; complements RSSI |
| TDOA | Time Difference of Arrival — removes need to synchronise transmitter clocks |
| TOA/TOF | Time of Arrival / Time of Flight — requires synchronisation |
| DOA/AOA | Direction/Angle of Arrival — requires directional antennas |
| RTT | Round Trip Time — used in WiFi RTT (IEEE 802.11mc) |

## Branch 1: Supervised Machine Learning

### Fingerprinting (RSSI Radio Maps)

The most widely adopted ML-based indoor localization technique:

1. **Offline training phase**: A "fingerprint database" is built by recording RSSI vectors (one value per access point) at known positions throughout the space
2. **Online testing phase**: The measured RSSI vector at an unknown position is matched to the closest fingerprint(s) in the database to estimate position

Fingerprinting outperforms pure trilateration in complex environments where interference, reflection, and refraction cause RSSI to not map cleanly to distance. A virtual fingerprinting variant uses RSSI prediction (radio map simulation) rather than physical measurement to build the database.

### Classical Supervised Methods

- **SVM (Support Vector Machine)**: Effective for position classification; finds maximum-margin decision boundaries
- **KNN (K-Nearest Neighbors)**: Matches observed fingerprint to K most similar training examples; simple but computationally efficient for moderate database sizes
- **Naive Bayes**: Probabilistic classifier; assumes feature independence
- **Decision Trees**: Interpretable; partition the feature space by RSSI thresholds

### Neural Network Methods

More complex supervised approaches for when classical methods are insufficient:

- **ANN (Artificial Neural Network)**: General-purpose function approximation for position regression or classification
- **CNN (Convolutional Neural Network)**: Applies spatial convolution; used for radio map image processing
- **DNN (Deep Neural Network)**: Multiple hidden layers; captures complex nonlinear relationships between RSSI patterns and positions
- **RNN (Recurrent Neural Network)**: Suitable for sequential/temporal data; handles pedestrian trajectories

The offline training phase learns the mapping between RSSI input vectors and position output labels; the trained model is then used for fast online inference.

## Branch 2: Unsupervised Methods

Unsupervised methods update network parameters without labelled training data, making them more autonomous and suited for the online phase:

- **Isolation Forest**: Anomaly detection; identifies outlier RSSI measurements (useful for NLOS detection)
- **K-Means**: Clusters fingerprint data without labels; can discover spatial zones
- **Expectation Maximisation (EM)**: Models the underlying data distribution; useful for soft cluster assignment

Advantage: no human intervention needed to supervise parameter updates; can adapt in real time as environments change.

## Branch 3: Filter-Based Methods

Filter-based methods model position as a dynamic state that is predicted and updated over time.

### Kalman Filter (KF) and Variants

All KF variants follow three steps: **Prediction → Measurement → Assimilation**

| Filter | Model Assumption | Notes |
|---|---|---|
| **LKF** (Linear KF) | Linear system, unimodal Gaussian | Baseline; handles simple linear motion |
| **EKF** (Extended KF) | Nonlinear system, approximated as linear via Taylor expansion | Most widely applied for indoor localization; approximates Gaussian distribution for nonlinear systems; drops second- and higher-order derivative terms |
| **UKF** (Unscented KF) | Nonlinear; sub-optimal but avoids Jacobian matrix | Overcomes EKF complexity; better accuracy for moderately nonlinear systems |
| **CKF** (Cubature KF) | Nonlinear; derivative-free, Bayesian | Preserves complete second-order state information; inherits linear properties for integral computation; closely approximates Bayesian filter |

EKF is the most common choice for indoor localization. UKF and CKF improve on EKF for more strongly nonlinear systems.

### Particle Filter (PF)

- Uses **Sequential Monte Carlo** methods; represents the probability density function as a set of weighted particles
- Models position using **Gaussian Mixture Models (GMM)** — multimodal distributions — unlike KF's single Gaussian
- **Rao-Blackwellized PF**: Combines analytical and particle-based estimation for efficiency
- **Likelihood PF / Genetic PF**: Variants that improve particle diversity and convergence

PF is more flexible than KF for highly non-Gaussian, multimodal distributions but is computationally more expensive.

## Performance Evaluation

Standard metrics per ISO/IEC 18305:

- **RMSE** (Root Mean Square Error) — most common; sensitive to large errors
- **MAE** (Mean Absolute Error) — arithmetic mean of absolute errors; more robust to outliers
- **CDF / eCDF** — cumulative distribution of errors at percentile levels (50th, 75th, 95th); commonly reported as "X% of readings within Y metres"
- **EAG** (Error Accumulation Gradient) — error growth rate in pedestrian dead reckoning
- **CE / VE / SEP** (Circular / Vertical / Spherical Error Probable) — 2D and 3D error magnitude

The ISO/IEC 18305 ideal absolute position accuracy target is **10mm**, though practical systems typically achieve 0.5–1m for WiFi RSSI and sub-30cm for UWB.

Other performance dimensions beyond accuracy:
- **Scalability**: Does accuracy/cost hold as the area or number of users grows?
- **Stability**: Does performance hold under noisy or incomplete input?
- **Reliability**: Consistent performance across dynamic environments
- **Complexity**: Computational cost of training and inference

## Device-Free Localization

*(Source: Sonny et al. 2024, 2403.04333)*

Traditional indoor localization requires the tracked subject to carry a wireless device. Device-free localization instead exploits the fact that human presence and movement perturb wireless signals passing through the environment — via reflection, refraction, and scattering — to infer position without any device on the person.

**Key signals used:**
- Wi-Fi CSI (Channel State Information) — fine-grained channel response captures body-induced signal changes
- FMCW RADAR / mmWave — directly detects reflected signals from the body; provides range, velocity, AoA
- Acoustic / sound — microphone arrays detect presence via reflections
- Magnetic field perturbations

**Advantages:** No device required; invisible to subject; useful for healthcare monitoring, security surveillance, intrusion detection, fall detection in elderly care.

**Challenges:** Environmental factors (furniture, other people, HVAC) also perturb signals; multipath complicates interpretation; requires dense infrastructure; generalisation across environments is poor without retraining.

**ML methods applied:** CNNs for RADAR point cloud processing; RNNs/LSTMs for time-series CSI; DNNs for activity classification from signal perturbations.

## Updated Accuracy Data by Technology

*(Source: Sonny et al. 2024, 2403.04333 — represents state-of-art at time of publication)*

| Technology | Method | Best Reported Accuracy |
|---|---|---|
| UWB | LSTM deep learning | **7 cm mean error** |
| UWB | RBF neural network (fingerprinting) | 15.24 cm |
| UWB | MLP neural network (fingerprinting) | 50 cm |
| BLE (Bluetooth 5.1) | DL fusion of RSSI + AoA (CNN + KF) | Sub-metre |
| BLE (gradient boosting) | EIL with RSSI | 98% accuracy within 1.5m |
| BLE iBeacon + Wi-Fi (WNN) | Weighted Nearest Neighbours | Comparable to individual methods |
| Magnetic sensors | Various DNNs | Room-level to ~1m |

**Note on UWB + LSTM (7 cm):** This is among the best published accuracy figures for wireless indoor localization. It outperforms conventional TOA-based UWB methods significantly. Weighted LSTM+DNN on five UWB signal features further improves performance under severe NLoS.

## BLE Bluetooth 5.1 — AoA Positioning

Bluetooth 5.1 introduced native **Angle of Arrival (AoA)** support, enabling direction-finding without relying solely on RSSI. This is a significant advancement over earlier BLE versions:

- Transmitter emits a signal with known phase switching pattern across antenna array
- Receiver estimates the angle of the incoming signal from phase differences
- Combined with RSSI: DL fusion (PCA + KF + CNN) extracts deep features from both RSSI and AoA
- Results in more robust positioning than RSSI-only, particularly in environments with high RSSI variability

This partially addresses the long-standing weakness of BLE (1–3m RSSI-only accuracy) noted in [[Indoor-Location-Sensor-Technologies]].

## Entity Summary

### Hardware
- **WiFi APs**: Standard 802.11 access points providing RSSI/RTT/CSI; existing infrastructure — no special hardware
- **BLE beacons**: iBeacons or generic BLE transmitters for RSSI fingerprinting or BT 5.1 AoA; low-cost
- **BLE 5.1 antenna arrays**: Multi-element receiver antenna arrays for Angle of Arrival measurement; specialized hardware
- **UWB anchors**: Dedicated UWB modules (e.g., Decawave/Qorvo); medium = UWB radio; required for 7cm accuracy
- **IMUs / MEMS**: Accelerometers, gyroscopes for PDR (Pedestrian Dead Reckoning) — often embedded in smartphones
- **FMCW RADAR**: mmWave modules (device-free); medium = mm-wave RF; see [[Indoor-Location-Sensor-Technologies]]
- **Microphone arrays**: For device-free acoustic-based localization (CSI perturbation sensing)

### Software
- **Fingerprint database tools**: Custom offline survey tools; some open-source (e.g., IndoorGML, OpenStreetMap Indoor)
- **ML frameworks**: TensorFlow, PyTorch, Keras (DNN/CNN/RNN training); scikit-learn (SVM, KNN, naive Bayes); freely available
- **Kalman filter libraries**: FilterPy (Python, open-source); implementations in MATLAB; embedded C for EKF in IoT devices
- **Particle filter**: OpenCV, FilterPy, custom C++ implementations
- **UWB SDK**: Vendor-specific (Decawave/Qorvo); partial open-source
- **Evaluation**: ISO/IEC 18305 standard metrics (RMSE, MAE, CDF); custom evaluation scripts typically in Python/MATLAB

### Algorithm
- **Fingerprinting**: Offline RSSI map construction → online KNN or DNN matching
- **Classical supervised**: SVM (max-margin classification), KNN (nearest-neighbour matching), Naive Bayes, Decision Trees, Random Forest
- **Deep learning**: ANN/DNN (regression/classification), CNN (radio map image processing), RNN/LSTM (temporal sequences / trajectories, 0.75m avg)
- **DBN (Deep Belief Network)**: Unsupervised pre-training + supervised fine-tuning; effective with only 10% labelled data
- **DeepFi**: CSI + Restricted Boltzmann Machine (RBM) autoencoder; unsupervised feature learning from channel response
- **Unsupervised**: Isolation Forest (NLOS anomaly detection), K-Means (zone/floor clustering), EM-GMM (NLOS mitigation, 44% faster than supervised)
- **NLOS identification**: SVM 91% accuracy; RVM (probabilistic); CNN-LSTM (temporal UWB CIR)
- **Dimension reduction**: PCA (−70% complexity), KPCA (nonlinear), GPMKDR (semi-supervised, outperforms PCA with labels)
- **Transfer learning**: Domain adaptation, fine-tuning, instance transfer — reduces war-driving to small target-domain samples
- **Reinforcement learning**: DQN (kidnapped-robot re-localisation), DRL (path planning in partially mapped environments)
- **Trajectory learning**: Particle filter + Gaussian Process; LDA feature extraction; SLAM + crowdsourcing fusion
- **LKF**: Linear Kalman Filter — predict → measure → assimilate; linear system assumption
- **EKF**: Extended KF — Taylor-linearised nonlinear system; most common for indoor localization
- **UKF**: Unscented KF — derivative-free sigma-point transform; better than EKF for moderately nonlinear systems
- **CKF**: Cubature KF — third-order spherical-radial rule; best accuracy for strongly nonlinear; derivative-free
- **Particle Filter**: Sequential Monte Carlo; GMM position representation; Rao-Blackwellized, Likelihood, Genetic variants
- **BLE 5.1 AoA fusion**: PCA + KF + CNN fusing RSSI and AoA into deep feature representation

### Accuracy
- **WiFi RSSI (fingerprinting + ML)**: Typically 0.5–1m; DNN can improve on KNN
- **WiFi RSSI (LSTM temporal)**: **0.75m average** (Nessa et al. 2020 — LSTM on RSSI sequence)
- **Random Forest (coarse zone)**: 98% zone-classification accuracy before fine-grained regression (Nessa et al. 2020)
- **SVM NLOS identification**: 91% LOS/NLOS classification accuracy (UWB CIR features)
- **EM-GMM NLOS mitigation**: Unsupervised; 44% faster convergence than supervised NLOS correction
- **UWB + LSTM**: **7 cm mean error** (Poulose & Han 2020 — state-of-art at 2024 survey)
- **UWB + RBF fingerprinting**: 15.24 cm
- **UWB + MLP fingerprinting**: 50 cm
- **BLE 5.1 AoA + DL fusion**: Sub-metre
- **BLE gradient boosting (EIL)**: 98% accuracy within 1.5m
- **Magnetic (various DNNs)**: Room-level to ~1m
- **ISO/IEC 18305 ideal target**: 10mm (rarely achieved by wireless systems)
- **Comparison basis**: RMSE, MAE, CDF at 50th/75th/95th percentile; EAG, CE/VE/SEP

## References (Nessa et al. 2020)

| # | Reference | In raw/ |
|---|---|---|
| [1] | Wang, G. et al. *On RSSI-based localization with unknown transmit power.* IEEE WCL 2012. | — |
| [5] | Yang, Z. et al. *From RSSI to CSI: Indoor localization via channel response.* ACM CSUR 2013. | — |
| [12] | Constandache et al. *Towards mobile phone localization without war-driving.* INFOCOM 2010. | — |
| [21] | Liu, H. et al. *Survey of wireless indoor positioning techniques.* IEEE TSMCC 2007. | — |
| [120] | Laoudias et al. *A survey of enabling technologies for network localization.* IEEE COMST 2018. | — |
| [154] | Zafari et al. *A survey of indoor localization systems.* IEEE COMST 2019. | ✅ [[Indoor-Location-Sensor-Technologies]] |

## ML for NLOS Identification and Mitigation

*(Source: Nessa et al. 2020, IEEE Access)*

NLOS (Non-Line-of-Sight) conditions — where the direct path between transmitter and receiver is blocked — are a primary cause of ranging errors, especially in UWB and TOA-based systems. ML offers both identification (is this reading NLOS?) and mitigation (correcting or discarding bad readings).

### Supervised NLOS Identification

- **SVM**: Applied to UWB CIR (Channel Impulse Response) features. Achieved **91% classification accuracy** for LOS vs. NLOS with SVM using RBF kernel. Common features: mean excess delay, RMS delay spread, kurtosis of CIR.
- **RVM (Relevance Vector Machine)**: Sparse Bayesian classifier; outperforms SVM in NLOS identification by producing probability estimates rather than hard decisions, enabling soft weighting of ranges.
- **CNN-LSTM hybrid**: Applied to UWB CIR time-series; CNN extracts spatial features from the CIR waveform, LSTM captures temporal dynamics. Effective for dynamic NLOS environments where channel conditions change over time.

### Unsupervised NLOS Mitigation

- **EM-GMM (Expectation-Maximisation on Gaussian Mixture Models)**: No labelled LOS/NLOS data required. Models the ranging error distribution as a mixture of Gaussians; automatically separates the LOS and NLOS populations. Reported to converge **44% faster** than supervised methods, making it advantageous when labelled training data is unavailable.

### General Principle

NLOS mitigation strategies fall into three categories: (a) identification + exclusion (discard NLOS ranges), (b) range correction (estimate and subtract the NLOS bias), or (c) robust estimators (down-weight NLOS ranges during position computation). ML enables all three in a data-driven way.

---

## Enhanced Fingerprinting via ML

*(Source: Nessa et al. 2020, IEEE Access)*

### Floor and Zone Classification

In multi-floor buildings, a two-tier strategy is common:
- **K-means clustering** on RSSI vectors, restricted floor-by-floor, reduces the search space before fine-grained position estimation
- **Random Forest** for coarse floor/zone classification: achieved **98% coarse accuracy** in reported experiments, before fine-grained regression

### Dimension Reduction for High-Dimensional Fingerprints

Radio fingerprint vectors can have hundreds of dimensions (one per visible AP). ML dimension reduction makes matching faster and more robust:

| Method | Description | Notes |
|---|---|---|
| **PCA** (Principal Component Analysis) | Linear projection to principal components | Reduces complexity by **~70%** with minimal accuracy loss |
| **KPCA** (Kernel PCA) | Nonlinear extension of PCA using kernel trick | Better for non-linear RSSI distributions |
| **GPMKDR** (Gaussian Process with Multiple Kernel Dimension Reduction) | Semi-supervised; preserves label information during reduction | Outperforms PCA when position labels are available |

### Semi-Supervised and Deep Fingerprinting

- **DBN (Deep Belief Network)**: Pre-trained unsupervised on abundant unlabelled RSSI data, then fine-tuned with a small labelled set. In reported experiments, **10% labelled data** was sufficient to achieve accuracy competitive with fully supervised methods — dramatically reducing the war-driving burden.
- **DeepFi**: Uses **CSI (Channel State Information)** instead of RSSI, combined with a **Restricted Boltzmann Machine (RBM)** autoencoder for unsupervised feature learning. Achieves significantly better fingerprinting accuracy than RSSI-based methods because CSI captures the full multipath profile, not just signal strength.

### RSSI Fluctuation and Temporal Instability

RSSI readings at the same location can vary significantly over time (people walking by, environmental changes). Approaches to handle this:

- **LSTM/RNN for temporal RSSI modelling**: Train on sequences of RSSI measurements; LSTM captures temporal correlations. Reported mean positioning error: **0.75m average** across tested environments — strong improvement over single-snapshot methods.
- **Data augmentation**: Generate synthetic fingerprints by perturbing existing ones; improves robustness to RSSI variability without additional site surveys.

---

## Transfer Learning for Scalability

*(Source: Nessa et al. 2020, IEEE Access)*

A core barrier to fingerprinting at scale is the cost of collecting labelled training data (war-driving) — especially when environments change or new floors/buildings must be covered.

**Transfer learning** addresses this by transferring knowledge from a well-surveyed **source domain** to a new **target domain** with little or no target-domain labelled data:

- A model trained on one floor or building captures general RSSI-position mapping structure
- This is adapted to a new environment using only a small target-domain dataset
- Reduces site survey effort from O(full space) to O(small sample)

Approaches reported:
- **Domain adaptation**: Align source and target feature distributions (e.g., via Maximum Mean Discrepancy minimisation)
- **Fine-tuning**: Pre-train DNN on source domain; fine-tune final layers on small target-domain labelled set
- **Instance transfer**: Reweight source-domain samples by their relevance to the target domain

**Limitation**: Transfer may fail when source and target domains are sufficiently dissimilar (e.g., very different building layouts or radio environments). Nessa et al. flag this as an open research challenge.

---

## Reinforcement Learning for Robot and Autonomous Navigation

*(Source: Nessa et al. 2020, IEEE Access)*

Reinforcement Learning (RL) is distinct from supervised and unsupervised ML: an **agent** learns a **policy** (mapping from observed state to action) by receiving **reward signals** from interacting with an environment. For indoor localization and navigation:

- **DQN (Deep Q-Network)**: Combines Q-learning (RL) with a deep neural network function approximator. Applied to the **kidnapped-robot problem** — a robot that loses track of its position must re-localise without external help. DQN learns a navigation policy that actively drives the robot toward distinguishing features of the environment.
- **DRL (Deep Reinforcement Learning)**: General term; broader class of RL + DNN approaches. Used for robot path planning in partially mapped environments where the agent must balance exploration (mapping unknown areas) and exploitation (using known areas for efficient navigation).

**Comparison with supervised localization**: Supervised methods (fingerprinting, Kalman) produce a position estimate given a measurement; RL produces an action given a position estimate — the two are complementary. RL handles the *what to do next* question; supervised ML handles the *where am I* question.

---

## Trajectory and Crowdsourcing-Based Learning

*(Source: Nessa et al. 2020, IEEE Access)*

Rather than localising individual snapshots, trajectory learning uses the continuity of pedestrian motion to improve position estimates:

- **Particle filter + Gaussian Process (GP)**: Particle filter maintains a probability distribution over positions; GP models the spatial correlation of RSSI observations. The combined approach smooths trajectory estimates and captures non-stationary RSSI behaviour.
- **LDA (Linear Discriminant Analysis)**: Used to extract discriminative features from sequential RSSI traces for trajectory segmentation and classification.
- **SLAM with crowdsourcing**: Multiple users' trajectories are fused server-side (as in [[Walkie-Markie-Indoor-Mapping]]); each user's path contributes to refining the shared radio map. Related to the UnLoc approach ([[UnLoc-Unsupervised-Localization]]).

**Key advantage**: Trajectory methods implicitly enforce physical constraints (a user cannot teleport; motion is smooth and bounded by walls). This dramatically reduces position estimate noise compared to frame-by-frame methods.

---

## ML Deployment Challenges and Future Directions

*(Source: Nessa et al. 2020, IEEE Access — Section VI)*

Despite substantial progress, ML-based IPS faces several open challenges:

**Application specificity**: ML models are tightly coupled to the measurement modality they were trained on. A DNN trained on RSSI fingerprints cannot be directly applied to CSI-based fingerprinting. Generalisation across modalities and environments requires significant re-engineering.

**Training data availability and standardisation**: ML performance is data-dependent. No standardised framework exists for collecting, labelling, or sharing IPS training datasets across different device models and environments. Device heterogeneity causes pattern mismatch when training and test devices differ (see [[Indoor-IPS-Datasets]]).

**Training and inference time**: RL-based methods can require very long training times (trial-and-error with the environment). DL models require significant compute for training; on-device inference on smartphones with limited storage and compute is a challenge for device-based IPS.

**DL on mobile devices**: Most IPS are device-based (the user's phone computes the location). DL models are difficult to deploy on resource-constrained phones — they require computational and storage overhead not available on commodity hardware. This limits DL to server-side computation with associated latency.

**Transfer learning reliability**: Even versatile transfer learning approaches may fail to reliably transfer knowledge when source and target domains are sufficiently dissimilar.

**Future outlook**: Nessa et al. note that advances in edge computing, 5G (ultra-low latency, high bandwidth), and cloud infrastructure are expected to alleviate compute and latency constraints, enabling more sophisticated ML models in production IPS deployments.

---

## RADAR and Horus — Foundational Fingerprinting Systems

### RADAR (Bahl & Padmanabhan, INFOCOM 2000)

The system that established WiFi RSSI fingerprinting as a practical approach. See [[RADAR-WiFi-Fingerprinting]] for full details.

- **Method**: Nearest Neighbours in Signal Space (NNSS) — deterministic KNN on Euclidean distance in RSSI space
- **Accuracy**: Median ~2.94m (empirical method), ~4.3m (WAF propagation model)
- **Key insight**: RSSI varies systematically with position; offline radio map + online matching is viable

### Horus (Youssef & Agrawala, MobiSys 2005)

The probabilistic successor to RADAR. See [[Horus-WLAN-System]] for full details.

- **Method**: Bayesian MAP inference over stored RSSI distributions; explicitly models temporal autocorrelation (AR(1), α≈0.9) and small-scale spatial variation (perturbation technique); Incremental Triangulation clustering reduces compute by >10×
- **Accuracy**: Median ~42cm — 89% better than RADAR on the same testbed
- **Key innovations**: Full distribution (not just mean) in radio map; correlation-aware sample averaging; continuous-space estimator (center of mass, time averaging)

## Dataset Augmentation for Fingerprinting

A practical challenge underlying all supervised fingerprinting methods is that real-world datasets are frequently **imbalanced** — some reference locations have far more training samples than others. Two papers address this directly:

### VAE/CVAE Oversampling for Imbalanced Fingerprinting (Alhomayani & Mahoor, 2021)

Alhomayani, F. & Mahoor, M.H. *Oversampling Highly Imbalanced Indoor Positioning Data using Deep Generative Models.* IEEE SENSORS 2021. University of Denver.

Class imbalance in fingerprinting datasets (see [[Indoor-IPS-Datasets]] for examples ranging from 1:2 to 1:398 minority-to-majority ratios) causes supervised classifiers to be biased toward majority-class locations, degrading accuracy for under-surveyed areas.

**Proposed approach**: Use a **Variational Autoencoder (VAE)** or **Conditional VAE (CVAE)** to generate synthetic fingerprint samples for minority-class locations. The generative model learns the distribution of BLE RSSI values for each location and produces new realistic samples to balance the training set.

- **Evaluated on**: BLE fingerprint dataset with ~1:7 imbalance
- **Compared against**: SMOTE and ADASYN (standard oversampling baselines)
- **Result**: VAE/CVAE outperforms SMOTE and ADASYN in minority-class precision and overall precision
- **Code**: Publicly available

**Contradiction note**: SMOTE (synthetic minority oversampling by interpolation between real samples) performs worse than VAE/CVAE on fingerprinting data — likely because RSS distributions are not linearly interpolable in signal space, whereas a learned generative model captures the true underlying distribution.

### Bayesian Optimisation for NN Hyperparameter Tuning in Radio Localisation (Liu et al., ~2022)

Liu, X., Li, P., Zhu, Z. *Bayesian Optimisation-Assisted Neural Network Training Technique for Radio Localisation.* University of Sheffield / University of Bristol / Toshiba Europe.

Manual hyperparameter selection (number of layers, neurons per layer, activation functions, dropout, normalisation) for NN-based localisation is time-consuming and expert-dependent. Different radio protocols (WiFi, BLE, UWB) have different signal characteristics requiring different optimal architectures.

**Proposed approach**: Apply **Bayesian optimisation (BO)** to automatically search the NN hyperparameter space:
- Models the relationship between hyperparameters and validation localisation error as a Gaussian process surrogate
- Selects next hyperparameter configuration to evaluate using an acquisition function (e.g., Expected Improvement)
- Requires far fewer model training runs than grid search or random search

**Applications**: WiFi, BLE, UWB radio signals for IoT factory and warehouse localisation. Particularly valuable when the deployment environment changes and the model must be re-trained or adapted.

## Mathematical Methods Taxonomy for Indoor Localization (Seco et al., IEEE ISWCS 2009)

Seco, F., Jiménez, A.R., Prieto, C., Roa, J., Koutsou, K. *A Survey of Mathematical Methods for Indoor Localization.* IEEE International Symposium on Intelligent Signal Processing (WISP), 2009. Instituto de Automática Industrial – CSIC, Madrid, Spain. Source: `raw/Sci-Hub. A survey of mathematical methods for indoor localization : 2009 IEEE International Symposiu.pdf`

A foundational taxonomy of the **mathematical techniques** underlying all indoor positioning algorithms — complementing surveys that focus on hardware/signal media by focusing on the computational methods used once measurements have been made. Specifically addresses radio-frequency-based local positioning systems (LPS).

**Four method categories** (with notes on applicability, requirements, and NLOS immunity):

| Category | Description | NLOS immunity | Notes |
|---|---|---|---|
| **Geometry-based** | Lateration (TOA/TDOA/RTOF) and angulation (AOA); solve geometric constraints on positions given measured signal properties | Low | Requires accurate ranging; sensitive to NLOS bias; multilateration is the canonical approach ([[ORL-Ultrasonic-Location-System]], [[Cricket-Location-Support-System]]) |
| **Cost function minimisation** | Formulate position estimation as an optimisation problem (e.g., NLS — Nonlinear Least Squares, weighted least squares); solve numerically | Moderate | More flexible than closed-form geometry; can incorporate noise models and constraints; used in [[EZ-Indoor-Localization]] (genetic algorithm minimisation) and Chen 2006 (LLS/NLS) |
| **Fingerprinting** | Compare observed measurements to a pre-built radio map; nearest-neighbour (RADAR) or probabilistic matching (Horus) | High | Does not require accurate ranging; instead learns the empirical RSSI-to-position mapping; well-suited to multipath-rich indoor environments; see [[RADAR-WiFi-Fingerprinting]] and [[Horus-WLAN-System]] |
| **Bayesian techniques** | Probabilistic estimation: MAP, MMSE, particle filters, Kalman filters; incorporate prior knowledge of motion model and environment | High | Most principled approach; subsumes fingerprinting (when fingerprint is used as likelihood); enables trajectory-based estimation; see filter-based methods below |

**LPS architecture**: Seco et al. define the standard LPS structure as a set of base stations (BS) at known locations and a mobile station (MS) being localised. Signals exchanged between BS and MS provide ranging/direction inputs to the mathematical methods above. This architecture is common across WiFi ([[RADAR-WiFi-Fingerprinting]]), Bluetooth ([[Indoor-Location-Sensor-Technologies]]), UWB ([[UWB-LSTM-Localization]]), and acoustic ([[ORL-Ultrasonic-Location-System]]) systems.

**NLOS considerations**: Geometry-based methods are most sensitive to NLOS bias (which adds positive range errors). Fingerprinting effectively bypasses this by learning NLOS-corrupted patterns as part of the fingerprint map. Bayesian methods can model NLOS as a heavy-tailed noise distribution, enabling soft NLOS mitigation. Cost function minimisation methods can incorporate NLOS constraints but require some knowledge of which measurements are NLOS.

**Significance**: The Seco 2009 taxonomy provides a clean conceptual structure for the methods surveyed in this article. The four categories map directly to the major branches of indoor localization ML: geometry methods → classical trilateration (pre-ML baseline), cost minimisation → non-linear optimisation (EZ, NLS), fingerprinting → supervised ML (KNN, DNN), Bayesian → probabilistic and filter-based methods (KF, PF, Horus).

---

## Bluetooth Indoor Localization with Multiple Neural Networks (Altini et al., IEEE ISSNIP 2010)

Altini, M., Brunelli, D., Farella, E., Benini, L. *Bluetooth Indoor Localization with Multiple Neural Networks.* IEEE 5th International Symposium on Intelligent Signal Processing (ISSNIP), 2010. University of Bologna / University of Trento. Source: `raw/Sci-Hub. Bluetooth indoor localization with multiple neural networks : IEEE 5th International Sympos.pdf`

A Bluetooth RSSI localization system that addresses the central problem of RSSI variability through a **per-orientation neural network** approach — recognising that user orientation (which way the device is held) is a primary source of RSSI inconsistency, and training separate neural networks for each orientation rather than treating orientation as noise.

**Motivation**: Standard RSSI-based localization methods (triangulation, single fingerprinting NN) achieve low accuracy primarily due to RSSI variability. This variability has two main sources: (a) multipath and interference from the environment, and (b) systematic variation due to user orientation (the body acts as an RF absorber/reflector, causing different RSSI patterns depending on how the device is held). Most prior work ignores the orientation component or treats it as noise.

**Architecture — Multiple Neural Networks**:
- One neural network is trained per user orientation (e.g., 4 orientations: N/S/E/W, or phone face-up/face-down, etc.)
- Each NN is trained on RSSI fingerprints collected at that specific orientation
- At inference time, the NN corresponding to the current user orientation is selected and activated
- This "multiple specialist" approach outperforms a single NN trained on all orientations combined

**Technology**: Bluetooth RSSI (short-range; inexpensive; widespread in mobile devices). The architecture uses a cost-effective BT infrastructure — multiple BT reference nodes (beacons) at known positions; mobile device measures RSSI from each beacon.

**Key result**: By training and activating orientation-specific NNs, the system achieves high-definition accuracy enabling **indoor navigation** with a cost-effective BT architecture. The orientation-aware approach substantially outperforms standard single-NN fingerprinting, particularly in environments where user orientation varies significantly (which is always the case in real-world navigation).

**Relationship to RADAR's orientation finding**: RADAR (Bahl & Padmanabhan 2000) separately noted that user body orientation introduces systematic error (up to 5 dBm RSSI variation), and recommended including all 4 orientations in offline survey data. Altini et al. take this further: rather than averaging over orientations, they train specialists that exploit orientation as a discriminative signal.

**Significance for this knowledge base**: Demonstrates that **structured decomposition of the localisation problem** (separate models per orientation rather than one model for all) can significantly improve BT-RSSI accuracy. This principle (divide-and-conquer with specialist models) re-emerges in later deep learning approaches (e.g., floor classification before precise positioning in multi-floor buildings).

---

## ML Techniques Survey (Roy & Chowdhury, 2021)

Roy, P. and Chowdhury, C. *A Survey of Machine Learning Techniques for Indoor Localization and Navigation Systems.* Journal of Intelligent and Robotic Systems, 2021. DOI: 10.1007/s10846-021-01327-z. Jadavpur University, India. Source: `raw/Sci-Hub. A Survey of Machine Learning Techniques for Indoor Localization and Navigation Systems : Jo.pdf`

A comprehensive 2021 survey covering the application of ML to indoor localization across WiFi, Bluetooth, and other technologies. Covers:

**Traditional ML approaches**: Feature-based methods using hand-crafted features (RSS variance, AP selection, RSSI statistics). Classifiers: KNN, SVM, Naive Bayes, decision trees, LDA/QDA. Feature selection: information gain, mutual information. Challenge: feature engineering requires domain expertise and is environment-specific.

**Deep Learning approaches**: End-to-end learned representations that automatically extract features from raw RSS data. Covered architectures include:
- Autoencoders and stacked denoising autoencoders for robust fingerprint representation (same architecture as [[WiFi-Fingerprinting-Advances]] WiDeep)
- CNNs for spatial fingerprint pattern recognition
- RNNs/LSTMs for sequential position tracking (related to [[UWB-LSTM-Localization]])
- Transfer learning: pre-train on one building/floor, fine-tune on new environment with minimal labelled data — addresses the costly re-survey problem

**Key themes**:
- **Device heterogeneity**: Transient and chaotic nature of WiFi/BLE fingerprint data; different device configurations produce different RSSI; ML normalisation and calibration strategies
- **Transfer learning**: Cited as important future direction — avoids full re-survey when deploying in a new building
- **Extreme Learning Machine (ELM)**: Fast single-layer neural network with random hidden weights; achieves fast training with competitive accuracy; relevant for resource-constrained embedded positioning systems
- **SLAM for robot navigation**: ML-based SLAM extensions for mobile robot indoor navigation context; directly related to [[UnLoc-Unsupervised-Localization]] and [[Magnetic-Field-Localization]]

The survey provides a taxonomy of 50+ approaches, identifying a clear trend from hand-crafted features (pre-2015) to end-to-end deep learning (2015–2021) as the dominant paradigm for high-accuracy indoor positioning.

## Deep Learning Survey for Indoor Localization (Kordi et al., CMC 2024)

Kordi, K.A., Roslee, M., Alias, M.Y., Alhammadi, A., Waseem, A., and Osman, A.F. *Survey of Indoor Localization Based on Deep Learning.* Computers, Materials & Continua (CMC), vol. 79, no. 2, pp. 3261–3298, May 2024. DOI: 10.32604/cmc.2024.044890. Multimedia University (MMU), Malaysia; Universiti Teknologi Malaysia (UTM); International Islamic University, Pakistan; Rohde & Schwarz, Malaysia. Source: `raw/Survey_of_Indoor_Localization_Based_on_Deep_Learni.pdf`

A 38-page, 135-reference survey reviewing deep learning approaches for indoor positioning across five wireless technologies (WiFi, BLE, ZigBee, UWB, RFID) with a unified DL taxonomy. Unlike single-modality surveys, explicitly covers hybrid multi-modal DL fusion and both device-based and device-free localization paradigms.

### DL Taxonomy: Device-Based vs. Device-Free

**Device-based**: The tracked entity carries a wireless device (smartphone, tag) that measures signals from fixed infrastructure.

- Multi-task learning on WiFi RSSI vectors — addresses similarity inconsistency between fingerprint regions
- LSTM + WiFi RSSI sequences — L-shaped corridor scenario; exploits temporal motion continuity
- DNN with MAC address labels for AP identification
- Deep Belief Network for RFID fingerprinting
- LSTM + geomagnetic field fingerprinting — no RF infrastructure required
- Cellular-based DL (CellinDeep, MonoDCell) — median 0.78m; best suited to outdoor/hybrid scenarios
- WiDeep (stacked autoencoders) — WiFi fingerprinting with unsupervised pre-training; see [[WiFi-Fingerprinting-Advances]]
- CNNLoc — multi-floor localization: stacked autoencoder (SAE) + 1D CNN + Mixture Density Network (MDN)
- LC-DNN (Local Connection DNN) — position-dependent local (PDL) CSI feature extraction
- SDR-Fi — Software-Defined Radio + CSI features fed to 1D-CNN and feed-forward NN
- **Ensemble MLP on CSI magnitude slope** (Sobehy, Renault & Mühlethaler, MLN 2019): Preprocessing extracts 65-point slope (finite difference) vector from 66-point polynomial-fitted magnitude spectrum per antenna; data augmentation (Gaussian noise on both position and magnitude); ensemble of 11 MLPs with different hyperparameters/training subsets; weighted mean/median mixing achieves **3.1 cm MSE** on 4×2m MIMO testbed — vs 4.3cm NDR baseline and 32cm CNN baseline. Key insight: spectral slope is more position-discriminative than absolute magnitude values; ensemble diversity matters more than per-model accuracy. Source: `raw/v1_ CSI_based_Indoor_localization_using_Ensemble_Neural_Networks.pdf` → [[CSI-Indoor-Localization]]

**Device-free**: Human presence and motion perturb passing wireless signals; position inferred without any device on the subject.

- ResLoc — bimodal deep residual sharing: fuses AoA and CSI amplitude (ResNet architecture)
- CNN classification on CSI amplitude envelopes — time-varying CSI degrades accuracy; environmental retraining required
- Two-phase DNN fingerprinting on combined RSS + CSI — offline phase builds joint fingerprint, online phase does DNN matching
- Visualization-guided DNN — information visualization and visual analysis (dimensionality reduction) for DNN diagnostic

### Technology Coverage

**WiFi (IEEE 802.11)**: Primary technology. 802.11a/b/g/n/ac operating in ISM band; 802.11ah extends range to ~1 km for IoT applications. Localization via RSSI fingerprinting or CSI. Advantage: existing infrastructure; no deployment cost. Disadvantage: dynamic multipath; RSSI variability.

**Bluetooth Low Energy (BLE)**: Range 70–100m; peak 24 Mbps; low power. Two localization paradigms: proximity-based (nearest-beacon) and triangulation/fingerprinting. BT 5.1 AoA (Angle of Arrival) is a significant advancement enabling native direction-finding; see also [[Indoor-Location-Sensor-Technologies]].

**ZigBee (IEEE 802.15.4)**: Three frequency bands (2.4 GHz, 915 MHz, 868 MHz); mesh networking topology; frequency-hopping spread spectrum for interference resistance. Lower throughput than WiFi or BLE; suited to low-data-rate IoT sensor networks. Localization uses RSSI-based fingerprinting or RSSI+time-of-arrival hybrid.

**Ultra-Wideband (UWB)**: Transmits 2 ns pulses across 500 MHz bandwidth. Centimetre-level accuracy via time-of-flight (TOF) measurement. NLOS conditions reduce accuracy but UWB is more resilient than narrowband signals due to pulse resolution. See [[UWB-LSTM-Localization]] for DL approaches achieving 7 cm.

**RFID**: Active tags (battery-powered; longer range; continuous transmission) and passive tags (no battery; powered by reader field; shorter range). Fingerprinting on received signal strength from multiple readers. Proximity sensing is simplest deployment; triangulation improves accuracy.

### Signal Measurement Techniques

**RSS Localization**: The received signal strength at distance d is modelled as:

```
RSS(dBm) = 10 × log(Pr / Pref)
```

where Pr is received power given by the Friis transmission equation:

```
Pr = Pt × Gt × Gr × (λ / 4πd)²
```

(Pt = transmitted power, Gt/Gr = transmitter/receiver antenna gains, λ = wavelength.)

**CSI Localization**: The Channel Impulse Response (CIR) at the receiver is:

```
h(τ) = Σᵢ aᵢ · e^(−jθᵢ) · δ(τ − τᵢ) + n(τ)
```

where aᵢ, θᵢ, τᵢ are the amplitude, phase, and delay of the i-th multipath component. CSI captures this full multipath profile per subcarrier, providing far richer channel information than scalar RSS. See [[CSI-Indoor-Localization]] for FILA's use of CSI across 30 OFDM subcarrier groups.

### CSI Algorithm Comparison Table (Kordi et al. Table 1)

| Algorithm | Localization method | CSI parameter used | Accuracy |
|---|---|---|---|
| CSI-MIMO | Fingerprint | Phase + amplitude | Lab 1.53m; Meeting 1.78m; Corridor 3.15m |
| PhaseFi | Range + angle-of-arrival fusion | Phase | Bedroom/Lab ≤2m |
| ConFi | Fingerprint | Amplitude | Living room/Lab 1.37m |
| Pilot | Range + angle-of-arrival fusion | Phase | Living room/Lab ≤2m |
| LiFS | Range (IFFT-based) | Phase + amplitude | LOS 0.5m; NLOS 1.1m |
| S-Phaser | Range | Phase + amplitude | Lab 1.5m |
| FIFS | Fingerprint | Phase + amplitude | Lab/Corridor ≤1m |
| FILA | Range (refined propagation model) | Amplitude | Lab/Reading/Corridor ≤3m |

**Notes**: FILA (Source 38, [[CSI-Indoor-Localization]]) uses amplitude only — avoiding phase calibration overhead — but achieves only ≤3m, ranking below FIFS and LiFS. PhaseFi and Pilot achieve ≤2m using phase-based AoA fusion. LiFS achieves the best LOS result (0.5m) but degrades to 1.1m under NLOS.

### DL Approaches Summary (Kordi et al. Tables 2 and 3)

| Approach | Method | Key limitation |
|---|---|---|
| Cellular-based DL | CNN on cellular RSS features | Best for outdoor; indoor accuracy degrades |
| WiFi RSSI + ML | Fingerprint + shallow ML or DNN | High RSSI variability; fingerprint aging |
| SDR-Fi | 1D-CNN on SDR CSI features | Requires SDR hardware; complex deployment |
| Low-overhead cellular DL | DNN for GSM/LTE RSSI | Limited to areas with cellular coverage |
| WiDeep | Stacked autoencoders on WiFi RSSI | Training complexity; needs labelled data |
| LC-DNN (PDL feature) | Local connection DNN on CSI | Position-dependent feature extraction overhead |
| DL + RSS + CSI (proposed) | Multi-modal CNN/DNN | Data collection burden for joint fingerprints |

**DL advantages**: automatic feature extraction; handles nonlinear RSSI/CSI-position mappings; scalable with data; outperforms classical ML when training data is abundant.

**DL disadvantages**: black-box; requires large labelled datasets; computationally expensive to train; poor generalisation across environments without retraining or transfer learning.

### Challenges Identified (as of 2024)

Kordi et al. identify seven challenge categories for DL-based indoor localization:

1. **Multipath effect and noise**: Indoor signals reflect off walls, furniture, people. Multipath causes RSSI and CSI variance that DL must learn to distinguish from position variation. The dominant accuracy-limiting factor across all technologies.

2. **Radio environment dynamics**: LOS conditions assumed by propagation models are rarely met indoors. Human movement, furniture rearrangement, and HVAC affect the radio map over time — fingerprint databases age, requiring periodic re-survey.

3. **Energy efficiency**: Continuous RF scanning drains mobile device batteries. Always-on localization is impractical for wearables and IoT nodes. Trade-off between update rate and power consumption.

4. **Cost**: Dense infrastructure (many APs, BLE beacons, UWB anchors) required for high accuracy raises deployment cost. RFID and ZigBee reduce hardware cost but limit accuracy.

5. **Privacy and security**: 4 spatio-temporal data points suffice to uniquely identify 95% of individuals (de-identification failure). GDPR imposes legal obligations on indoor positioning data. Side-channel inference attacks can extract location from radio metadata even without explicit positioning.

6. **Negative perception of adopted technology**: Users associate passive localization with surveillance; concerns reduce willingness to adopt localization-enabled services. Perceived privacy invasion can undermine technology acceptance even when privacy safeguards are present.

7. **Lack of standardization**: No single dominant technology or protocol for indoor positioning (contrast with GPS outdoors). Fragmented ecosystem prevents interoperability; benchmarking across papers is difficult due to heterogeneous hardware, environments, and evaluation metrics. No universal benchmark dataset for DL-based IPS.

### Emerging DL Architectures (Section 13)

- **WiDeep**: Stacked denoising autoencoders for WiFi RSSI; outperforms single-layer NNs; see [[WiFi-Fingerprinting-Advances]]
- **SDR-Fi**: Software-Defined Radio captures wideband CSI; 1D-CNN extracts spatial features from the raw channel snapshot; demonstrated in office environments
- **CNN multi-floor (CNNLoc)**: Stacked Autoencoder (SAE) reduces dimensionality → 1D CNN classifies floor → Mixture Density Network (MDN) outputs a probability distribution over positions rather than a point estimate; handles ambiguous multi-floor radio environments
- **LC-DNN**: Local Connection DNN with position-dependent local (PDL) CSI features; local connections (not fully connected) reduce parameter count while preserving spatial structure in the CSI fingerprint
- **DL + RSS + CSI**: Authors' proposed contribution — joint multi-modal fingerprint combining RSS and CSI; CNN/DNN trained on the combined feature vector; claimed to outperform single-modality approaches

## WiFi RSSI ML Pipeline: Singh, Choe & Punmiya (IEEE Access 2021)

Singh, N., Choe, S. & Punmiya, R. *Machine Learning Based Indoor Localization Using Wi-Fi RSSI Fingerprints: An Overview.* IEEE Access, vol. 9, pp. 127150–127169, September 2021. DOI: 10.1109/ACCESS.2021.3111083. Catholic University of Korea. Source: `raw/singh-machinelearningbased-2021.pdf`

A focused survey on ML-based indoor localization specifically using Wi-Fi RSSI fingerprints. Contributes a detailed decomposition of the full ML pipeline — from radio map construction through preprocessing, augmentation, model training, postprocessing, and transfer learning — with coverage of six public benchmark datasets and seven open challenge categories. Complements broader DL surveys (Kordi 2024, Roy & Chowdhury 2021) by concentrating on the RSSI+Wi-Fi combination and its practical engineering pipeline.

### Radio Map Construction

A **radio map** (fingerprint database) maps observed RSSI vectors to physical positions. The RSSI fingerprint of a Wi-Fi signal is defined as:

```
fᵢ = [(x, y), AP_ID, RSSI₁, RSSI₂, ...]
```

The database `rdn = (f₁, f₂, ..., fₙ)` is built at n reference points (RPs) in the offline phase. Beyond manual site surveys, several automated construction approaches reduce labor overhead:

- **UAV-based collection**: Drones collect 3D fingerprints autonomously (AuF system); limited by energy and coverage scalability
- **WiGAN**: Robot collects RSS data; GPR-conditioned least-squares GAN generates dense fine-grained radio maps from sparse measurements
- **SLAM-based (Wi-Fi SLAM)**: Gaussian process latent variable model links fingerprints to motion model; no location tags required; GraphSLAM extension improves computational efficiency
- **Crowdsourcing** (OIL, Zee, LiFS): Users passively or actively contribute fingerprints during normal movement — see [[Zee-Zero-Effort-Crowdsourcing]] and [[Walkie-Markie-Indoor-Mapping]]
- **GIFT (Gradient Fingerprinting)**: Uses differential RSSI between adjacent locations rather than absolute values; independent of AP transmission power and device type; backward compatible with existing construction methods
- **Sparse recovery (LASSO)**: AP selection + augmented sparse recovery on sparse grid; compressive sensing with SVD reduces survey density requirements

**Radio map enrichment** addresses noise, sparsity, and AP change:
- Stacked denoising autoencoders (DAE) for RSSI noise removal
- RecTrack-GAN for synthetic fingerprint generation and database updating
- Nonparametric Gaussian process regression for automatic fingerprint update when AP signals change
- Linear interpolation + extrapolation with inverse distance weighting for coverage gap filling
- RSS quantisation (4-bit) for consistency and memory efficiency

### Data Preprocessing Pipeline

Singh et al. define five sequential preprocessing sub-tasks applied to raw RSSI before ML training:

| Sub-task | Description | Techniques |
|---|---|---|
| **Data cleansing / denoising** | Remove/correct false or missing values; denoise RSSI signals | Autoencoders, GANs; missing AP values replaced with sentinel (e.g., 100) |
| **Selection & partitioning** | Random sample selection; split into train/validation/test sets | — |
| **Feature tuning** | Normalise, clip outliers, adjust skewed distributions | Exponential, zero-to-one, and powered normalisation; min-max scaling |
| **Feature extraction** | Create new, reduced features from raw inputs; discard originals | PCA, LDA, autoencoders |
| **Dimensionality reduction** | Reduce input dimension for efficiency; remove redundancy | SVD, PCA, KPCA, LLE, t-SNE (see detail below) |

**Dimensionality reduction algorithms** (commonly used for RSSI fingerprint inputs):

- **SVD**: Factorises M into UΣVᵀ; retains top singular values; reduces radio map dimension while preserving variance structure
- **PCA**: Eigenvalue decomposition of data covariance matrix; projects to n principal components corresponding to n largest eigenvalues; R_PCA = Uₙᵀ R; reduces complexity by ~70% (Nessa et al. 2020)
- **KPCA**: Kernel PCA for nonlinear data; projects to high-dimensional kernel space where data are linearly separable, then applies PCA
- **LLE (Locally Linear Embedding)**: Nonlinear manifold learning; preserves local structure so nearby points in input space stay nearby in embedding; 3-step: kNN graph → local reconstruction weights → embed using same weights
- **LDA**: Maximises between-class scatter relative to within-class scatter; linear and discriminative; inapplicable to nonlinear distributions
- **t-SNE**: Nonlinear unsupervised manifold method; primarily for data exploration and visualisation of high-dimensional RSSI structure
- **Autoencoders**: Encoder compresses input to bottleneck latent space; decoder reconstructs; goal is minimising reconstruction error; unsupervised; produces features richer than PCA for non-linear data

### Data Augmentation

When fingerprint data is scarce (collecting is costly and labour-intensive), data augmentation generates synthetic training samples to improve ML model performance and reduce overfitting. Approaches include:

- **DataLoc+**: Data augmentation for room-level indoor localization
- **Conditional adversarial networks (cGAN)**: Generate synthetic fingerprints conditioned on RP location to fill sparse grid positions
- **VAE/CVAE oversampling**: Handles class imbalance — see dedicated section above (Alhomayani & Mahoor 2021)
- **DNN-based augmentation**: Demonstrates feasibility of DNN-generated fingerprints that reduce required site surveys and improve location accuracy

### ML Model Classes for Indoor Localization

**KNN** — Nonparametric; classifies or regresses by finding the k training points nearest in RSSI space; estimated location:

```
Loc = (1/k) × Σₓ Locₓ   (x = 1 to k)
```

Values of k are critical: lower k risks noise sensitivity; higher k risks smoothing over spatial detail. KNN is the baseline method (established by RADAR 2000 — see [[RADAR-WiFi-Fingerprinting]]).

**SVM** — Supervised; finds a maximum-margin hyperplane separating RSSI fingerprint classes:

```
minimise  ½‖w‖² + C × Σξᵢ
subject to  yᵢ(w·xᵢ + b) ≥ 1 − ξᵢ,  ξᵢ ≥ 0
```

(C = regularisation trade-off, ξᵢ = slack variables.) Uses kernel trick for nonlinear separation. NR-SVM achieves 93.75% test case accuracy at 98.75% overall. Pose-aware SVM achieves 97.16% pose recognition with 0.43m positioning error.

**Decision Tree (DT)** — Nonparametric; splits data by thresholding RSSI features; splits evaluated by Gini diversity index:

```
Ginᵢ = 1 − Σₖ (pᵢ,ₖ)²
```

(pᵢ,ₖ = fraction of class k instances in node i.) Applied in two stages: (1) build radio map with DT model; (2) classify fingerprints at test time for location prediction.

**ANN/DL** — The standard ANN output for a fully connected layer:

```
h_{W,b}(X) = φ(XW + b)
```

(φ = activation function, W = weight matrix, b = bias.) DL variants for indoor localization:

- **MLP**: Feedforward; backpropagation training; nonlinear activation (ReLU, softmax) separates MLP from linear perceptron; arbitrary hidden layers; ≤ 0.98m error with LDA preprocessing
- **CNN**: Local connections, weight sharing, pooling-based downsampling; processes radio map as 2D image; 95.41%+ building/floor accuracy on UJIIndoorLoc; handles time-varying RSSI by treating fingerprints as time-series images
- **RNN/LSTM**: Directed graph allows information to flow back to prior layers; LSTM learns long-term dependencies; path and location prediction via convolutional mixture density RNN; WAF-smoothed LSTM achieves 0.75m mean error
- **DQN (Deep Q-Network)**: Approximates Q-function in Q-learning; uses experience replay (off-policy); handles unreliable fingerprints via agent-based hierarchical search; models localization as Markov decision process (MDP); achieves 75% of devices within 0.55m
- **SAE (Stacked Autoencoder)**: Unsupervised pre-training + supervised fine-tuning (softmax); feature reduction followed by DNN; used in CNNLoc and WiDeep

**Transfer Learning (TL)**: Transfers knowledge from one indoor environment (source domain) to another with limited labelled data (target domain). Addresses fingerprint aging and deployment cost. Key mechanism: domain adaptation reduces distribution mismatch between source and target RSSI distributions. Reduces re-survey requirement from O(full area) to O(small sample).

**Postprocessing**: Additional filtering/smoothing applied to raw ML output positions; includes pruning, quality filtering, sorting rules. Retraining needed for dynamic environments; TL reduces retraining overhead.

### Open Challenges (Singh et al. 2021)

Seven engineering challenges for ML-based Wi-Fi RSSI IPS, distinct from Kordi et al.'s 2024 DL-focused challenge taxonomy:

| Challenge | Description | Potential solutions |
|---|---|---|
| **Privacy** | IPS always knows user location; 4 spatio-temporal points re-identify 95% of users (see also Kordi 2024); structural maps inferable from radio map | Federated learning; secure data collection; lightweight blockchain; GDPR compliance frameworks |
| **Lack of standardisation** | No standard ML algorithms for IPS; no standardised benchmark databases; heterogeneous metrics prevent cross-paper comparison | Universal datasets via collaboration; global organisation for standards; dedicated IPS ML algorithms |
| **Adaptive radio map construction** | Radio maps age as environments change; re-survey is expensive and labour-intensive | Crowdsourcing + semisupervised learning for automatic incremental updates |
| **Device heterogeneity** | Different vendors represent RSSI on different scales; fingerprints from one device do not match another | Platform-independent hardware/software standards; universal calibration methods |
| **High energy consumption** | Continuous WiFi scanning drains battery; localization competes with primary device function | Energy-efficient sensors and algorithms; parallel processing; remote localization architectures |
| **WiFi not designed for localization** | WiFi APs built for connectivity; additional localization load creates resource management conflicts | Dedicated localization channels in future WiFi standards (Wi-Fi 6 and beyond) |
| **Handover delay during roaming** | Handoff between APs introduces lag; disrupts continuous tracking | Behaviour pattern analysis and ML-based location prediction to pre-empt handoff |

⚠️ **Contradiction note**: Singh et al. 2021 cite BLE/iBeacon achieves 12cm accuracy in healthcare settings (referencing Aziz & Koo 2025). This optimistic figure conflicts with the general 1–3m BLE RSSI accuracy documented in Yang et al. 2021 and throughout [[Indoor-Location-Sensor-Technologies]]. The 12cm result reflects controlled, algorithm-optimised conditions; general deployment accuracy is significantly higher.

### Public Datasets (Singh et al. 2021 — Table 4)

Singh et al. survey six public Wi-Fi RSSI fingerprint databases:

| Dataset | Coverage | Devices/Users | Training | Test | APs |
|---|---|---|---|---|---|
| **UJIIndoorLoc** (2013) | 110,000 m², 3 buildings, 4+ floors | 20 people, 25 Android devices | 19,937 | 1,111 | 529 features |
| **KTH/RSS** (2016) | 400 m² (indoor hallway + rooms + steel factory) | Mobile robot | 1,689 total | — | — |
| **Minho** (2017) | 1,000 m², University of Minho | Raspberry Pi 3 + USB WiFi × 4 | 4,973 | 810 | 11 APs |
| **Tampere** (2017) | 22,570 m², 4-floor university building | 21 devices | 687 | 3,951 | — |
| **Library / Mendoza-Silva** (25 months) | 308 m², university library | — | 576 | 3,120 | 620 APs |
| **JUIndoorLoc** (2019) | 882 m² × 5 floors, Jadavpur University | 4 Android devices | 23,904 | 1,460 | 172 APs |

*See [[Indoor-IPS-Datasets]] for a more complete dataset catalogue.*

---

## Relationships

- [[Indoor-Location-Sensor-Technologies]] — signal sources (WiFi, BT, UWB, RADAR, magnetic) that provide inputs to these methods; also covers NLOS/multipath causes
- [[Indoor-IPS-Datasets]] — public datasets used to train and benchmark these methods; device heterogeneity discussed there affects fingerprint mismatch
- [[ORL-Ultrasonic-Location-System]] — geometric (non-ML) approach; complementary to ML methods
- [[UWSN-Localization]] — ML methods also applied in underwater localization contexts
- [[UWB-LSTM-Localization]] — primary source for the 7cm UWB+LSTM result; two-layer LSTM with Nadam optimizer outperforms LLSE, MLE, WCE, RNN, ELM, and BP on UWB ranging data
- [[UnLoc-Unsupervised-Localization]] — unsupervised landmark detection + dead-reckoning; related to K-means OLM discovery and SLAM-style trajectory fusion covered here
- [[Walkie-Markie-Indoor-Mapping]] — crowdsourced trajectory fusion for map building; trajectory learning and Gaussian Process approaches here are closely related
- [[RADAR-WiFi-Fingerprinting]] — foundational fingerprinting system; KNN matching is the baseline this article's methods improve upon
- [[Horus-WLAN-System]] — Bayesian probabilistic fingerprinting; motivates DBN and probabilistic methods surveyed here
- [[Indoor-IPS-Datasets]] — class imbalance problem (Alhomayani & Mahoor) motivates the VAE/CVAE oversampling methods covered in the Dataset Augmentation section above; imbalance statistics sourced from public datasets catalogued there
- [[WiFi-Fingerprinting-Advances]] — WiDeep (deep learning WiFi fingerprinting) and zero-config SVD (Lim et al.) are closely related to the DL approaches surveyed by Roy & Chowdhury and Kordi et al.
- [[Magnetic-Field-Localization]] — SLAM-based ML methods covered in Roy & Chowdhury survey overlap with magnetic SLAM approaches; neural networks for magnetic fingerprinting also covered in Kordi 2024
- [[CSI-Indoor-Localization]] — Kordi 2024 explicitly covers CSI as a DL input modality alongside RSS; CSI+DL is an emerging high-accuracy combination

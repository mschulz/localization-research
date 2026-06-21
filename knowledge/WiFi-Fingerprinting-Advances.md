---
tags: [WiFi, fingerprinting, indoor-localization, survey, crowdsourcing, collaborative-localization, sparse-recovery, deployment-challenges, semisupervised, trajectory-learning, node-placement]
date-compiled: 2026-06-15
source-files:
  - "raw/COMST16_IP.pdf"
  - "raw/1610.05424v1.pdf"
  - "raw/Liu et al. - 2016 - Adaptive Sampling of RF Fingerprints for Fine-grai.pdf | My Library | Zotero.pdf"
  - "raw/Tan et al. - 2020 - An Efficient Fingerprint Database Construction App.pdf | My Library | Zotero.pdf"
  - "raw/fast-construction-of-the-radio-map-based-on-the-improved-low-115csk6gui.pdf"
  - "raw/Data Cleansing for Indoor Positioning Wi-Fi  Fingerprinting Dataset.pdf"
  - "raw/Sci-Hub. A comparison of deterministic and probabilistic methods for indoor localization : Journal o.pdf"
  - "raw/Sci-Hub. WiDeep: WiFi-based Accurate and Robust Indoor Localization System using Deep Learning : 201.pdf"
  - "raw/Zero-Configuration, Robust Indoor Localization Theory and Experimentation.pdf"
  - "raw/601bbfbdb639558b07eb4aabffc00e184315.pdf"
  - "raw/Placement_optimization_positioning_nodes.pdf"
status: active
---

# WiFi Fingerprinting — Advances and Deployment Challenges

## Summary

A synthesis of two complementary IEEE Communications Surveys & Tutorials papers on WiFi fingerprinting, covering state-of-the-art advances from the mid-2010s beyond the foundational [[RADAR-WiFi-Fingerprinting]] and [[Horus-WLAN-System]] systems:

1. **He & Chan** — *"Wi-Fi Fingerprint-Based Indoor Positioning: Recent Advances and Comparisons"*, IEEE COMST 2016 (Vol. 18, No. 1). Focuses on advanced localization techniques (temporal/spatial patterns, collaborative localization, motion-assisted) and deployment efficiency.

2. **Khalajmehrabadi, Gatsis & Akopian** — *"Modern WLAN Fingerprinting Indoor Positioning Methods and Deployment Challenges"*, arXiv 1610.05424, University of Texas at San Antonio, 2016. Covers problem formulation, clustering, AP selection, sparse recovery, assisted localization, radio-map construction, outlier detection, and heterogeneous devices.

Together these surveys define the complete problem space of WiFi fingerprinting from basic positioning through practical deployment.

## Problem Formulation (Khalajmehrabadi framework)

The fingerprinting problem: given a set of Reference Points P = {pⱼ = (xⱼ, yⱼ)}, offline radio maps R (RSSI from L APs at each RP, M samples each), and an online measurement vector y = (y¹, ..., yᴸ)ᵀ, find the user's position:

> ˆp = f(R, y)

The time-averaged radio map Ψ stores mean RSSI per AP per RP; online query compares y against Ψ.

## Conventional Approaches (Taxonomy)

### Deterministic
- **KNN**: Select K nearest RPs in signal space (Euclidean distance); estimate position as centroid
- **Weighted KNN**: Weight each neighbour by inverse distance or inverse RSS variance; closer signal-space match → higher weight
- **Median filtering**: Use median (not mean) as representative fingerprint; more robust to outliers

### Probabilistic
- **MAP (Maximum A Posteriori)**: argmax P(pⱼ|y) via Bayes — equivalent to **ML (Maximum Likelihood)** under uniform prior
- **Weighted ML**: Position as weighted sum of all RPs, weights ∝ P(y|pⱼ) — outperforms single-RP ML when user is between RPs
- **Horus** (full distribution): Stores per-AP RSSI histograms; probabilistic inference with correlation handling — see [[Horus-WLAN-System]]

### Pattern Recognition
- **SVM**: Classifies position; effective but computationally expensive
- **Neural Networks**: ANN, DNN — learn complex nonlinear fingerprint-to-position mappings
- **Linear Discriminant Analysis (LDA)**, **CCA**: Dimensionality reduction before classification

## Advanced Techniques (He & Chan taxonomy)

### Temporal and Spatial Signal Patterns

**Temporal patterns** (signal sequences during walking):
- **PWF (Peak-based WiFi Fingerprinting)**: Detects when RSSI from an AP peaks (user passes directly under it); peaks act as position anchors; requires user to be walking
- **Walkie-Markie**: Records RSSI trend reversals (WiFi-Marks) along corridors — the point where RSS from an AP switches from increasing to decreasing as the user passes closest to it. Device-independent and time-stable (90% within 2.5m across device models and times of day). Used to build a crowdsourced pathway map without any prior floor plan. See [[Walkie-Markie-Indoor-Mapping]] for full treatment.

**Spatial patterns** (geographic signal structure):
- **UnLoc**: Zero-calibration indoor localization using automatically detected ambient sensor landmarks (WiFi AP set uniqueness, magnetic anomalies, inertial turn signatures). Dead-reckoning runs between landmarks; error resets at each landmark encounter. Achieves 1.69m median across office buildings and shopping mall with no war-driving. See [[UnLoc-Unsupervised-Localization]] for full treatment.
- **MapCraft**: Leverages signal landmarks (WiFi + magnetic) to constrain position estimates; combines inertial and WiFi sensing
- **HALLWAY**: Exploits the *ordering* of signal strengths from different APs as location-dependent features (e.g., s₁ < s₂ < s₃ in room A vs. s₁ < s₃ < s₂ in room B); reduces device heterogeneity and noise sensitivity; granularity limited to room-level
- **Coverage intersection**: For each detected AP, divide its coverage into distance zones by signal level; intersect zones from multiple APs to constrain target; more reliable than single-AP approaches

### Collaborative Localization

Multiple users share location information to collectively improve individual estimates:

**Distance-based** (accurate pairwise distance available):
- **Virtual Compass (VC)**: Uses WiFi Direct / Bluetooth to measure pairwise distances between neighbouring users; forms network topology graph; rotate/translate to best fit radio map fingerprints
- **Peer-Assisted (PA)**: M users simultaneously measure WiFi fingerprints; pairwise distances constrain joint optimisation: argmin Σ‖rₙ − sₘ‖² over reference point set

**Proximity-based** (coarse mutual detection):
- Users detect nearby peers via Bluetooth, WiFi Aware, NFC, sound
- Probabilistic inference using proximity constraints; more robust to noisy distance measurement

### Motion-Assisted Localization

Fuses WiFi fingerprinting with inertial sensors (PDR — Pedestrian Dead Reckoning):
- IMU (accelerometer, gyroscope, magnetometer) in smartphones tracks step count, heading, step length → dead-reckoning trajectory
- WiFi fingerprint provides absolute position fix when dead-reckoning drifts
- Kalman filter or particle filter fuses the two streams
- Reduces required survey density; trajectory continuity constrains online estimation

## Deployment Challenges (Khalajmehrabadi taxonomy)

### Radio Map Construction
The offline survey phase is the primary deployment barrier: a surveyor must physically visit every reference point and collect ~20–100 RSSI samples. Three strategies to reduce cost:

- **Crowdsourcing**: Users passively contribute RSSI measurements during normal usage; labels obtained opportunistically (e.g., from user-declared landmarks, GPS at doorways); noisy but scalable
- **Implicit / unlabelled data collection**: Treat unlabelled data with EM or semi-supervised learning to auto-label positions
- **Radio map interpolation**: Collect sparse samples; predict fingerprints at unsampled locations using Gaussian processes, kriging, or neural network regression

### RP Clustering (Pre-selection)
Before online matching, narrow the search to a sub-region:
- **K-means / hierarchical clustering**: Group RPs by AP coverage; online query consults only the relevant cluster → reduces computation ≥10× (also used in Horus IT clustering)
- **Kernel-based**: Project fingerprints into RKHS for nonlinear clustering

### AP Selection
Not all APs contribute equally; using all APs increases noise and computation:
- **RSS variance-based**: Select APs with high RSS variance across RPs (high discriminating power)
- **Mutual information**: Select APs providing most information about position
- **Outlier AP rejection**: Remove APs with faulty/intermittent readings

### Advanced Density Estimation
- **Kernel Density Estimation (KDE)**: Non-parametric PDF estimation per AP per RP; more flexible than Gaussian
- **EM-based distributions**: Gaussian Mixture Models fit to multi-modal RSSI distributions
- **Weight estimation**: Adaptive weighting of APs based on reliability

### Sparsity-Based Localization (Compressive Sensing)
Novel reformulation: the user's position vector over all N RPs is **sparse** (user is at one or a few RPs). The problem becomes:

> ˆp = argmin ‖p‖₀  s.t.  y = Ψp + noise

Solved with Lasso / Basis Pursuit (ℓ₁ relaxation), LASSO, OMP (Orthogonal Matching Pursuit), CoSaMP. Enables positioning with fewer APs and fewer survey points than traditional methods.

### Outlier Detection
Online RSSI measurements may be corrupted by AP failures, device faults, or anomalous propagation:
- **Distance-based outlier detection**: Flag online measurements far from all stored fingerprints
- **Isolation Forest** (referenced in [[Indoor-Localization-ML-Methods]])
- **Robust KNN / RKNN**: Assigns zero weight to outlier APs before position estimation
- Detected outliers are excluded from the matching computation; reduces "jump" errors

### Heterogeneous Devices
Different smartphones have different NIC chipsets and antenna orientations — RSSI from device A at location X ≠ RSSI from device B at the same location:
- **Linear calibration**: A linear mapping between NIC RSSI offsets (Haeberlen et al. showed this is feasible)
- **Kernel-based mapping**: More flexible device transformation
- **Device-independent features**: Use RSSI ordering/rank instead of absolute values; less sensitive to device differences (links to HALLWAY spatial pattern approach)

## Performance Context

He & Chan deployed and compared approaches in a real building; key findings:
- Temporal + spatial patterns improve accuracy in specific environments (corridors, spaces with good AP coverage) but are not universally applicable
- Collaborative localization provides meaningful improvement when pairwise distance is accurate (Bluetooth/ultrasound ranging); proximity-based is more robust but less accurate
- Motion-assisted systems reduce survey burden significantly while maintaining comparable accuracy
- No single algorithm dominates across all environments; selection should depend on site properties

Khalajmehrabadi ran a controlled evaluation at UTSA on the same fingerprint dataset for all methods:
- Sparse recovery (ℓ₁) outperforms KNN and probabilistic methods in low-AP-density scenarios
- Device heterogeneity is the leading practical deployment barrier; linear calibration partially compensates
- Crowdsourced radio maps degrade accuracy by 20–40% vs. dedicated survey in their experiments

## Entity Summary

### Hardware
- **Survey device**: Smartphone or laptop with WiFi NIC — standard commodity hardware; no specialised devices
- **Collaborative localization extras**: Bluetooth, WiFi Direct, or ultrasound ranging modules — all standard in modern smartphones
- **Motion-assisted**: MEMS IMU (accelerometer + gyroscope) — standard in modern smartphones; no additional hardware
- **APs**: Standard 802.11 access points; existing infrastructure; no modifications

### Software
- **Crowdsourcing platforms**: Custom apps (Android/iOS) capturing RSSI + GPS/landmark labels; research prototypes; no dominant open-source standard
- **Sparse recovery**: CVXPY (Python), CVX (MATLAB) — open-source convex solvers implementing Lasso/OMP
- **Probabilistic inference**: scikit-learn (Python), MATLAB Statistics Toolbox — standard tools
- **Motion fusion**: FilterPy (Python Kalman filter), MATLAB — open-source; also proprietary SDKs
- **Availability**: Both papers are survey/tutorial papers; no single system release; reviewed methods have varying code availability

### Algorithm
- **KNN / Weighted KNN**: Euclidean / weighted Euclidean nearest neighbour; O(NK) per query
- **MAP/ML fingerprinting**: Bayesian maximum likelihood; Gaussian or KDE per AP per RP
- **Temporal patterns**: DTW sequence matching (Walkie-Markie); peak detection (PWF)
- **Spatial patterns**: Landmark detection (UnLoc); RSSI ordering (HALLWAY); coverage intersection
- **Collaborative**: Graph optimisation (PA); pairwise distance constraints (VC)
- **PDR fusion**: Step detection + heading from IMU → dead-reckoning; Kalman/PF fusion with WiFi fix
- **Sparse recovery**: ℓ₁ minimisation (Basis Pursuit / Lasso) or greedy (OMP, CoSaMP)
- **Outlier detection**: Distance-based; Isolation Forest; RKNN
- **Crowdsourced radio map**: EM-based semi-supervised labelling; GP interpolation

### Accuracy
- **Baseline KNN (2.4GHz WiFi, typical office)**: 2–5m depending on AP density and survey grid spacing
- **Probabilistic (Horus)**: ~0.5m median — see [[Horus-WLAN-System]]
- **Motion-assisted (PDR + WiFi)**: Comparable to standalone WiFi but reduced survey burden
- **Collaborative (distance-based)**: 20–40% improvement over individual WiFi-only
- **Sparse recovery**: Outperforms KNN in low-density AP scenarios
- **Crowdsourced radio map vs. dedicated survey**: 20–40% accuracy degradation depending on label quality
- **Key deployment insight**: Accuracy of state-of-art WiFi fingerprinting (sub-metre) requires dedicated survey; sub-2m accuracy is achievable with crowdsourcing

## Radio Map Construction: Sparse Sampling and Matrix Completion

A major practical bottleneck in WiFi fingerprinting is the **offline site survey** — a surveyor must collect RSS measurements at every reference point, which is labour-intensive and must be repeated whenever the environment changes. Four papers in this batch address this directly:

### Adaptive Sampling via Tensor Completion (Liu et al., 2016)

Liu, X.-Y. et al. *Adaptive Sampling of RF Fingerprints for Fine-grained Indoor Localization.* IEEE Transactions on Network Science and Engineering, 2016.

The fingerprint dataset (location × AP) is treated as a **3D tensor** (x, y, AP-index). The core insight: this tensor has a low-dimensional structure exploitable via **tubal-sampling** (tensor completion). The proposed scheme adaptively identifies which reference points are most *informative* for recovering the entire tensor, reducing survey effort while preserving localization accuracy.

Key results:
- **71% sample reduction** at high SNR while maintaining the same localization accuracy
- **55% sample reduction** at low SNR
- Validated on both ray-tracing indoor model (accounting for walls/floors) and real-world data
- Provably achieves bounded recovery error under incoherency conditions

Compared to random sampling (the standard baseline for compressed sensing), adaptive sampling achieves significantly better localization accuracy for the same number of survey points.

### Matrix Completion for Fingerprint Database Construction (Tan et al., 2020)

Tan, T., Zhang, L., Li, Q. *An Efficient Fingerprint Database Construction Approach Based on Matrix Completion for Indoor Localization.* IEEE Access, 2020. Beijing Jiaotong University.

Exploits the **high spatial correlation** of RSS values across adjacent reference points. A partially-collected fingerprint matrix can be completed using low-rank matrix completion. The paper improves on the standard nuclear norm minimisation model by:

- **Weighted nuclear norm**: Penalises rank components non-uniformly, better recovering the true low-rank structure
- **ℓ₁-norm**: Handles sparse outlier corruptions (e.g., occasional deep fading)
- **Frobenius norm**: Controls Gaussian noise
- **KNN fallback**: Matrix completion cannot fill rows/columns with *no* measurements at all; KNN is used for those positions

Result: the fingerprint database can be recovered from **a small fraction of measured RSS values**, with accuracy comparable to a full survey. Evaluated in office and corridor scenarios.

### Fast Radio Map Construction via Low-Rank Matrix Recovery (Wang et al., 2021)

Wang, Z., Zhang, L., Kong, Q., Wang, K. *Fast Construction of the Radio Map Based on the Improved Low-Rank Matrix Completion and Recovery Method.* Journal of Sensors, 2021. Shandong University of Technology.

Similar matrix-completion approach but adds an explicit **noise removal** step using a **low-rank matrix recovery** algorithm. The Frobenius parameter (F-parameter) is introduced to stabilise the optimisation when filling sparse data. Workflow:

1. Deploy a small number of evenly-spaced reference points (rather than dense survey)
2. Collect RSS at those points → partial radio map
3. Low-rank matrix completion → complete radio map
4. Low-rank matrix recovery → noise removal
5. Feed completed, denoised radio map to standard KNN or probabilistic localization

Achieves expected accuracy with far fewer initial measurements than a full survey.

### Data Cleansing for WiFi Fingerprinting Datasets (Quezada-Gaibor et al., ~2022)

Quezada-Gaibor, D. et al. *Data Cleansing for Indoor Positioning Wi-Fi Fingerprinting Datasets.* Universitat Jaume I / Tampere University.

Addresses a different dimension of radio map quality: **outlier removal** from already-collected datasets. The method:

1. Computes **pairwise correlation** between fingerprints (based on detected APs and their RSS values)
2. Fingerprints with low correlation to the rest of the dataset are flagged as outliers
3. Flagged fingerprints are removed from the radio map

Evaluated on **14 independent publicly-available datasets**:
- Average **14% of fingerprints removed**
- **2D positioning error reduced by 2.7%** on average
- **3D positioning error reduced by 5.3%** on average
- **Floor hit rate improved by 1.2%**
- **Position prediction speed improved by 14%** (smaller dataset → faster nearest-neighbour search)

Key insight: a cleaner, smaller radio map outperforms a larger but noisy one, both in accuracy and inference speed.

## Algorithm Comparison: Deterministic vs. Probabilistic (Dawes & Chin, 2011)

Dawes, B. and Chin, K.-W. *A comparison of deterministic and probabilistic methods for indoor localization.* The Journal of Systems and Software, 84(3):442–451, 2011. University of Wollongong. Source: `raw/Sci-Hub. A comparison of deterministic and probabilistic methods for indoor localization : Journal o.pdf`

Prior work compared algorithms only within the same paradigm (deterministic vs. deterministic, or probabilistic vs. probabilistic). Dawes & Chin provide a **cross-paradigm comparison** on a common testbed, the first systematic study of this kind in WiFi RSSIF:

**Key findings**:
- Methods that **average the distance to the k nearest neighbours** in signal space (KNN averaging) perform well even with reduced dimensions — consistent with Kaemarungsi's analytical findings (see [[Kaemarungsi-WiFi-Fingerprinting]])
- Using the **standard deviation of RSSI values** across samples to identify and **exclude transient APs** significantly improves robustness — transient APs (intermittently visible) introduce noise in both training and testing
- **Dense APs**: High AP density improves accuracy but introduces dimensionality issues; AP selection by variance is recommended
- No single method dominates; the paper quantifies the conditions under which probabilistic methods outperform deterministic ones (high RSS variance environments) and vice versa (clean indoor environments)

The paper directly tests both RADAR-style (deterministic KNN) and Horus-style (probabilistic Bayesian) approaches, with actionable implementation recommendations.

## Deep Learning for WiFi Fingerprinting: WiDeep (Abbas et al., PerCom 2019)

Abbas, M., Elhamshary, M., Rizk, H., Torki, M., and Youssef, M. *WiDeep: WiFi-based Accurate and Robust Indoor Localization System using Deep Learning.* IEEE PerCom 2019. Alexandria University / EJUST. Source: `raw/Sci-Hub. WiDeep: WiFi-based Accurate and Robust Indoor Localization System using Deep Learning : 201.pdf`

WiDeep is a deep learning WiFi fingerprinting system from Moustafa Youssef's group (previously responsible for [[Horus-WLAN-System]]). It addresses two core limitations of classical fingerprinting:

1. **Noise and signal instability**: RSSI exhibits inherent variance that degrades accuracy in dynamic environments
2. **Device heterogeneity**: RSSI readings from different smartphone models at the same location differ systematically

**Architecture**:
- **Stacked Denoising Autoencoders (SDA)**: Deep learning model that learns robust WiFi fingerprint representations by training to reconstruct clean inputs from noisy versions; captures complex non-linear correlations between signals from different APs at each location
- **Probabilistic framework** on top of the learned representations: produces a probability distribution over locations rather than a hard nearest-neighbour match — handles uncertainty in noisy environments
- **Anti-overfitting modules**: Dropout regularisation; early stopping based on validation loss
- **Device heterogeneity handling**: Data augmentation and normalisation modules to reduce cross-device RSSI offset

**Results** (two testbeds, different sizes and AP densities):
- **Larger testbed**: 2.64m mean localization accuracy
- **Smaller testbed**: 1.21m mean localization accuracy
- Outperforms state-of-the-art techniques (classical KNN, Horus probabilistic, sparse recovery) in all test scenarios
- Robust to heterogeneous devices — validated across multiple smartphone models

WiDeep is notable as a direct descendant of Horus (same research group), showing the trajectory from manual statistical modelling (Horus, 2005) to end-to-end learned representations (WiDeep, 2019).

## Zero-Configuration Fingerprinting: SVD-Based Approach (Lim et al., UIUC)

Lim, H., Kung, L.-C., Hou, J.C., and Luo, H. *Zero-Configuration, Robust Indoor Localization: Theory and Experimentation.* University of Illinois at Urbana-Champaign (UIUC). Source: `raw/Zero-Configuration, Robust Indoor Localization Theory and Experimentation.pdf`

This paper establishes the theoretical basis and practical algorithm for indoor localisation with **zero configuration** — no site survey, no prior training, no floor plan required:

**Core technique**: **Truncated Singular Value Decomposition (SVD)** on AP-to-AP RSS measurements:
- Instead of measuring RSS from client to AP (which requires a labelled survey), the system uses **RSS measurements between pairs of 802.11 APs** — infrastructure-to-infrastructure measurements that can be collected automatically without any human involvement
- The AP-to-AP RSS matrix captures the multipath and distance structure of the environment without any client participation
- SVD of this matrix identifies the dominant geometric structure; the client's position is estimated by projecting its to-AP measurements onto this SVD basis, creating a mapping between RSS and geographical distance without explicit site survey

**Environmental adaptation**:
- Because AP-to-AP measurements can be collected continuously and automatically, the mapping is updated in real-time as environmental conditions change (doors opening, furniture moving, temperature/humidity variations, human mobility patterns)
- The system is therefore robust to temporal RSS drift that defeats pre-surveyed fingerprint databases

**Zero-configuration** is achieved because:
1. No surveyor needs to collect labelled measurements at known locations
2. No initial training data from users required
3. The SVD mapping bootstraps purely from infrastructure measurements

**Implementation**: inexpensive off-the-shelf WiFi hardware; leverages standard 802.11 sensory functions; no hardware modifications. Validated in a multi-room division building at UIUC.

This approach is complementary to [[EZ-Indoor-Localization]] (zero-calibration via propagation model) and [[UnLoc-Unsupervised-Localization]] (zero-calibration via landmark detection) — different mechanisms to eliminate the offline survey phase.

## Mobile Fingerprinting and Trajectory Learning (Yoo & Park, Applied Sciences 2019)

Yoo, J. & Park, J. *Indoor Localization Based on Wi-Fi Received Signal Strength Indicators: Feature Extraction, Mobile Fingerprinting, and Trajectory Learning.* Applied Sciences, 2019, 9(18), 3930. DOI: 10.3390/app9183930. Hankyong National University / Ajou University, South Korea. Source: `raw/601bbfbdb639558b07eb4aabffc00e184315.pdf`

A 21-page open-access paper presenting three interconnected advances for WiFi RSSI fingerprinting, all built on a **semisupervised learning** framework that leverages large quantities of easily-collected unlabeled RSSI data alongside small amounts of labelled data.

### Part 1 — Semisupervised Feature Extraction (Floor and Landmark Classification)

The raw RSSI vector from d APs is high-dimensional (d can be 100+), with many missing values where APs are out of range. Direct KNN on this raw vector is slow and inaccurate. This paper combines:

- **Fisher Discriminant Analysis (FDA)** — supervised; finds projection directions that maximise between-class separation relative to within-class variance; overfits when labelled data is small
- **PCA** — unsupervised; finds directions of maximum variance from all data (labelled + unlabeled)
- **Semisupervised combination**: balances FDA (uses label information) and PCA (uses unlabeled mass) to get a low-dimensional discriminative embedding — outperforms either alone when labelled data is scarce

The embedded feature vectors are used for:
- **Floor classification**: different floors produce visibly separable clusters in the FDA+PCA space; single RSSI measurement suffices for floor-level classification
- **Landmark detection**: functional areas (elevator, toilet, corridors) also produce distinctive RSSI signatures; embedded features can detect these without floor plan data

### Part 2 — Mobile Fingerprinting (Semisupervised Site Survey)

Conventional static fingerprinting requires the surveyor to stop at each grid point, manually record location, and measure RSSI. This is time-consuming and restricts grid density. Mobile fingerprinting allows the surveyor to **walk continuously** while collecting RSSI data — converting the temporal sequence of unlabeled measurements into a pseudolabeled training set.

**Core technique**: Laplacian Embedded Regression Least Squares (LapERLS) with Hodrick–Prescott (H–P) temporal smoothing:
- A small set of labelled measurements anchors the spatial mapping
- The H–P filter captures the temporal dynamics of the walking trajectory, adding a smoothness constraint: sequential positions form a smooth path, not random jumps
- The H–P objective adds term γT Σ (f(yᵢ) + f(yᵢ₋₂) − 2f(yᵢ₋₁))² to penalise acceleration in the position estimate — i.e., enforces trajectory smoothness
- Pseudolabels are generated for unlabeled data by solving the resulting regularised system

**Advantage over static collection**: faster, requires fewer training stops; the unlabeled stream provides dense spatial coverage without manual labelling at each point.

### Part 3 — Trajectory Learning for Mapless Localization

When the floor plan is unavailable, standard particle filter localization cannot use wall constraints to prune impossible paths. Trajectory learning from crowdsourced data fills this gap:
- Multiple users' RSSI sequences are collected crowdsourced; each is a trajectory sample
- The learned trajectories encode the walkable paths within the building (halls, corridors, accessible zones) without requiring an explicit floor plan
- The particle filter uses these learned trajectories as a **prior distribution** — particles that stray into learned-inaccessible areas are down-weighted
- Position likelihood is computed via **Gaussian Process (GP)** modelling of the RSSI–position relationship — probabilistic, accounts for RSSI uncertainty

**Result**: Trajectory learning improved average positioning accuracy by **up to 1.2m** in multi-floor building experiments, compared to the same system without trajectory learning. Accuracy improved as more crowdsourced participants contributed — the learned map converged toward the true walkable space.

**Significance**: Addresses three distinct bottlenecks in WiFi fingerprinting deployment simultaneously — the survey burden (mobile collection), feature quality (semisupervised dimensionality reduction), and the map-dependency problem (trajectory learning). The semisupervised thread connects all three components: unlabeled data benefits feature extraction, mobile fingerprinting, and map learning.

---

## Placement Optimization of Positioning Nodes (Xenakis et al., ISPRS Geospatial Week 2019)

Xenakis, D., Meijers, B.M., & Verbree, E. *Placement Optimization of Positioning Nodes: Maximizing the Distinction of Indoor Zones.* International Archives of Photogrammetry, Remote Sensing and Spatial Information Sciences (ISPRS Geospatial Week 2019), Enschede, Netherlands, June 2019. Delft University of Technology, GIS Technology group. Source: `raw/Placement_optimization_positioning_nodes.pdf`

A methodology for **optimal deployment of wireless positioning nodes** (access points or BLE beacons) to maximise the fingerprinting performance of zone-based indoor positioning systems, using a genetic algorithm.

**Problem**: The arrangement of transmitting nodes strongly determines how distinguishable the RSS fingerprints of different zones are. Prior work typically placed nodes heuristically (uniform grid, coverage-maximising). This paper proposes an automated optimisation framework that finds node placements maximising *zone discrimination* in the RSS fingerprint space.

**Four-component methodology**:

1. **Indoor environment modelling**: Rooms and zones are modelled geometrically (IndoorGML-compatible); obstructions (walls, partitions) are represented to enable accurate signal simulation

2. **Performance metric — Zone Distinction**: Defined as the separation area and distances between RSS fingerprint clusters of different zones in the RSS vector space. High zone distinction means zones are easily discriminated by any pattern classifier (KNN, SVM, DNN); low distinction means the RSS patterns of different zones overlap, causing misclassification. This is closely related to the analytical fingerprint discrimination framework of [[Kaemarungsi-WiFi-Fingerprinting]] (which quantifies discrimination probability in terms of fingerprint signal separation)

3. **Radio propagation simulation via ray tracing**: RSS values at candidate reference locations are simulated using a ray-tracing model, which accounts for reflections, diffractions, and obstructions. This avoids the need for physical measurement in candidate deployments — the optimisation operates entirely in simulation

4. **Genetic Algorithm (GA) optimisation**: Iteratively searches the space of all possible node deployments. Each candidate deployment is evaluated by simulating RSS values with the ray-tracing model, computing the zone distinction metric, and feeding the result back to the GA. The GA explores the deployment space efficiently (vs brute-force enumeration), selecting and recombining high-performing candidate placements

**Application**: Zone-based positioning — determining which room or zone a user occupies, rather than exact (x,y) coordinates. Common in logistics, healthcare, and smart building applications.

**Key factors for optimisation success** (expected by authors): (a) accuracy of the indoor geometric and propagation model; (b) implementation of the objective function in the GA. The approach's evaluation was outside this paper's scope — it is a methodology proposal.

**Relationship to Kaemarungsi research group**: Kaemarungsi's Source 59 (still scanned: `Sci-Hub. Optimal placement of reference nodes for wireless indoor positioning systems : 2014 11th In.pdf`) addresses the same AP placement problem from the fingerprinting analytical framework perspective. Xenakis 2019 provides a complementary computational optimisation approach that explicitly uses zone-level performance metrics and genetic algorithms.

---

## References

| # | Reference | Priority | In raw/ |
|---|---|---|---|
| Liu et al. 2016 | Liu, X.-Y. et al. *Adaptive Sampling of RF Fingerprints for Fine-grained Indoor Localization.* IEEE Trans. Netw. Sci. Eng. 2016. | High — tensor completion | ✅ |
| Tan et al. 2020 | Tan, T. et al. *Fingerprint Database Construction Based on Matrix Completion.* IEEE Access, 2020. | High — matrix completion | ✅ |
| Wang et al. 2021 | Wang, Z. et al. *Fast Construction of the Radio Map Based on Low-Rank Matrix Completion.* J. Sensors, 2021. | High — low-rank matrix recovery | ✅ |
| Quezada-Gaibor et al. | Quezada-Gaibor, D. et al. *Data Cleansing for Indoor Positioning Wi-Fi Fingerprinting Datasets.* Universitat Jaume I. | High — data quality | ✅ |
| Dawes & Chin 2011 | Dawes, B., Chin, K.-W. *A comparison of deterministic and probabilistic methods.* J. Syst. Softw. 84(3):442–451, 2011. | High — cross-paradigm comparison | ✅ |
| Abbas et al. 2019 | Abbas, M. et al. *WiDeep.* IEEE PerCom 2019. | High — deep learning fingerprinting | ✅ |
| Lim et al. UIUC | Lim, H. et al. *Zero-Configuration, Robust Indoor Localization.* UIUC. | High — zero-config SVD | ✅ |
| He & Chan [2] | Bahl, P., Padmanabhan, V.N. *RADAR.* INFOCOM 2000. | ⭐ High — foundational | ✅ `raw/infocom2000.pdf` |
| He & Chan [52] | Youssef, M. *Horus.* MobiSys 2005. | ⭐ High — Horus reviewed | ✅ `raw/youssef.pdf` |
| He & Chan [44] | Wang, H. et al. *No Need to War-Drive: Unsupervised Indoor Localization (UnLoc).* MobiSys 2012. | ✅ [[UnLoc-Unsupervised-Localization]] | ✅ `raw/unloc.pdf` |
| He & Chan [45] | Shen, G. et al. *Walkie-Markie: Indoor Pathway Mapping Made Easy.* NSDI 2013. | ✅ [[Walkie-Markie-Indoor-Mapping]] | ✅ `raw/nsdi13-final130.pdf` |
| He & Chan [23] | Xiong, J. et al. *ArrayTrack: A Fine-Grained Indoor Location System.* NSDI 2013. | High — collaborative PA | — |
| Yoo & Park 2019 | Yoo, J., Park, J. *Indoor Localization: Feature Extraction, Mobile Fingerprinting, Trajectory Learning.* Appl. Sci. 2019, 9(18), 3930. | High — semisupervised site survey; trajectory learning | ✅ `raw/601bbfbdb639558b07eb4aabffc00e184315.pdf` |
| Xenakis et al. 2019 | Xenakis, D. et al. *Placement Optimization of Positioning Nodes.* ISPRS Geospatial Week 2019. | Medium — GA-based AP placement; zone distinction metric | ✅ `raw/Placement_optimization_positioning_nodes.pdf` |

## Relationships

- [[RADAR-WiFi-Fingerprinting]] — foundational system; all advances described here build on RADAR's KNN + radio map concept
- [[Horus-WLAN-System]] — probabilistic fingerprinting system; explicitly reviewed and extended in He & Chan
- [[E-eyes-Device-Free-Localization]] — device-free CSI approach; Khalajmehrabadi covers passive/device-free as a category
- [[Indoor-Localization-ML-Methods]] — DNN/CNN/RNN methods are modern extensions of the advanced fingerprinting approaches catalogued here
- [[Indoor-Location-Sensor-Technologies]] — WiFi signal characteristics (RSSI, CSI, RTT) underpinning all fingerprinting
- [[Indoor-IPS-Datasets]] — public datasets used to benchmark the methods surveyed in both papers
- [[Indoor-Localization-Applications-by-Sector]] — crowdsourcing and collaborative methods directly relevant to scalable healthcare/retail deployment
- [[UnLoc-Unsupervised-Localization]] — spatial pattern landmark approach reviewed here under He & Chan taxonomy; full system details in dedicated article
- [[Walkie-Markie-Indoor-Mapping]] — temporal pattern fingerprinting approach reviewed here; builds pathway maps using WiFi-Mark (RSS trend reversal) landmarks
- [[Indoor-IPS-Datasets]] — the 14 public datasets used to evaluate the data cleansing method (Quezada-Gaibor et al.) overlap with the datasets catalogued there
- [[Kaemarungsi-WiFi-Fingerprinting]] — Dawes & Chin results directly complement Kaemarungsi's analytical framework; both characterise when KNN outperforms probabilistic methods
- [[EZ-Indoor-Localization]] — zero-config propagation-model approach; Lim et al. SVD provides an alternative zero-config mechanism
- [[CSI-Indoor-Localization]] — WiDeep (Abbas et al.) explicitly mentions CSI-based systems as a related category; WiDeep achieves comparable accuracy using only standard RSSI on commodity devices
- [[Indoor-Localization-ML-Methods]] — Yoo & Park's semisupervised + particle filter + GP framework is closely related to the trajectory and filter-based methods surveyed there; Xenakis placement optimisation is addressed in the ML survey's "deployment challenges" discussion
- [[Pedestrian-Dead-Reckoning]] — Yoo & Park's trajectory learning and H-P filter approach complements IMU-based dead reckoning for mapless localization

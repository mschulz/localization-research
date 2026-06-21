---
tags: [indoor-localization, sensor-technologies, comparative-survey, WiFi, UWB, ultrasonic, RFID, Bluetooth, RADAR, VLC, LiDAR, acoustic, magnetic, security, energy-efficiency, 5G, LANDMARC, BIPS]
date-compiled: 2026-06-15
source-files:
  - "raw/ORL New Location Technique tr.97.10.pdf"
  - "raw/sensors-21-08086.pdf"
  - "raw/applsci-15-01544-v2.pdf"
  - "raw/A_Comprehensive_Review_of_Indoor_Localization_Tech.pdf"
  - "raw/2403.04333v1.pdf"
  - "raw/077620951.pdf"
  - "raw/A_Survey_of_Indoor_Localization_Systems_and_Techno.pdf"
  - "raw/RFID Localisation.pdf"
  - "raw/Xiao et al. - 2016 - A Survey on Wireless Indoor Localization from the .pdf | My Library | Zotero.pdf"
  - "raw/Almalioglu et al_2020_Milli-RIO.pdf | My Library | Zotero.pdf"
  - "raw/Papaioannou et al. - 2017 - Tracking People in Highly Dynamic Industrial Envir.pdf | My Library | Zo.pdf"
  - "raw/sensors-20-01382-v2.pdf"
  - "raw/sensors-22-09380.pdf"
  - "raw/s11277-021-08209-5.pdf"
  - "raw/Sci-Hub. An Indoor Localization Mechanism Using Active RFID Tag : IEEE International Conference on S.pdf"
  - "raw/Sci-Hub. Design and Analysis of a Bluetooth-Based Indoor Localization System : Lecture Notes in Comp.pdf"
  - "raw/1-s2.0-S1389128625004943-main.pdf"
status: active
---

# Indoor Location Sensor Technologies

## Summary

A comparative overview of sensor technologies used for indoor positioning, drawing from: the ORL/Cambridge 1997 paper on ultrasonic location (Ward, Jones, Hopper); the 2021 survey by Yang, Cabani & Chafouk (*Sensors 2021, 21, 8086*); the 2024 ML survey by Sonny et al. (2403.04333); the 2025 sector applications review by Aziz & Koo; the IEEE COMST 2017 survey by Yassin et al. (*"Recent Advances in Indoor Localization: A Survey on Theoretical Approaches and Applications"*, Vol. 19, No. 2); and the IEEE Comm. Surveys 2019 survey by Zafari, Gkelias & Leung (*"A Survey of Indoor Localization Systems and Technologies"*). Technologies range from early IR-based systems to modern ML-enhanced radio approaches. No single technology dominates across all scenarios; choice depends on accuracy requirements, coverage, cost, and deployment constraints.

## Localization Mode Taxonomy (Zafari et al. 2019)

Before comparing technologies, it is useful to distinguish the *mode* of localization:

- **Device-Based Localization (DBL)**: The user's device uses Reference Nodes (RNs) to obtain its own location. Primarily used for navigation — the user needs assistance moving around a space. Requires battery-constrained user device to perform or offload computation.
- **Monitor-Based Localization (MBL)**: A set of anchor/RNs passively computes the user's position. Primarily used for tracking — the user is located by the infrastructure rather than locating themselves. Infrastructure nodes typically have more power and processing capability.
- **Proximity Detection**: Estimating relative distance between user and a Point of Interest (PoI), rather than absolute position. Simpler and more cost-effective than full localization; sufficient for context-aware services.

The choice of mode affects energy efficiency, scalability, privacy, and system complexity requirements.

## Key Measurement Parameters

Modern indoor localization systems measure one or more of the following signal properties:

- **RSSI** (Received Signal Strength Indication) — most common; susceptible to multipath and interference
- **TOA/TOF** (Time of Arrival / Time of Flight) — requires tight time synchronisation
- **RTOF** (Return Time of Flight) — round-trip ToF; eliminates transmitter/receiver clock synchronisation by measuring signal round-trip at a single node; more practical than one-way ToF for commodity hardware (Zafari et al. 2019)
- **TDOA** (Time Difference of Arrival) — eliminates need to synchronise transmitter clocks
- **DOA/AOA** (Direction/Angle of Arrival) — requires directional antennas; sensitive to angular multipath; preferred in open areas over ToA which is preferred in urban multipath environments
- **RTT** (Round Trip Time) — used in WiFi RTT (IEEE 802.11mc)
- **CSI** (Channel State Information) — richer signal descriptor used alongside RSSI for higher accuracy

**Fundamental accuracy limits (Yassin et al. 2017):** The Cramér-Rao Lower Bound (CRLB) defines the theoretical floor on positioning accuracy given noisy ToA, AoA, or RSS measurements. With map-aware prior knowledge, accuracies of ~2m are achievable. Commodity hardware can reach ~0.2m in ideal conditions; worst-case maximum error can be reduced to ~1.6m with map-based algorithms.

## LOS vs. NLOS

All indoor localization technologies must contend with the distinction between:

- **LOS (Line of Sight)**: Direct signal path between transmitter and receiver; more predictable attenuation and transit time
- **NLOS (Non-Line of Sight)**: Signal reflected or obstructed by walls, furniture, people; causes overestimated distance measurements, increased error

The number, thickness, and material of obstacles determines the severity of NLOS effects. The [[ORL-Ultrasonic-Location-System]] addresses this with geometric and statistical outlier-rejection tests. Modern systems use ML methods (see [[Indoor-Localization-ML-Methods]]) to classify and compensate for NLOS conditions.

## Technology Comparison

### Infrared (IR) — Active Badge / ParcTab (1990s)

- **Accuracy**: Room-level (natural IR containment by walls)
- **Range**: Within a room
- **Pros**: Robust, relatively cheap, integrates into working environments
- **Cons**: Room-level granularity only; line-of-sight required for detection (though reflections help flood rooms); cannot distinguish positions within a room
- **Representative systems**: [[Active-Badge]] (ORL), ParcTab (Xerox PARC)
- **Source**: ORL paper

---

### Electromagnetic Trackers

- **Accuracy**: ~1mm position, ~0.2° orientation
- **Range**: A few metres
- **Pros**: Very high accuracy and resolution
- **Cons**: Expensive; require physical tethers to control units; sensitive to metallic objects; short range
- **Source**: ORL paper (citing Raab et al., 1979; Ascension Technology Corp., 1994)

---

### Optical Trackers

- **Accuracy**: Similar to electromagnetic (~mm-level)
- **Range**: Constrained environments
- **Pros**: Very robust; high accuracy
- **Cons**: Expensive; mechanically complex; require well-constrained, uncluttered environments
- **Examples**: Head tracker for augmented reality (Wang et al., 1990); Minnesota Scanner for body motion tracking (Sorensen et al., 1989)
- **Source**: ORL paper

---

### Radio Positioning — GPS / LORAN

- **Accuracy**: ~50cm or worse indoors
- **Range**: Wide-area (designed for outdoors)
- **Pros**: Very successful outdoors
- **Cons**: Ineffective indoors due to radio signal reflections; in-building implementations exist but offer only modest accuracy (~50cm, Feuerstein & Pratt, 1989)
- **Source**: ORL paper

---

### Video / Computer Vision — MIT Smart Rooms

- **Accuracy**: Can be high using cheap hardware
- **Pros**: Relatively affordable hardware
- **Cons**: Requires large amounts of computer processing; current techniques (as of 1997) could only handle simple scenes; unsuitable for many objects in cluttered indoor environments
- **Source**: ORL paper (citing Pentland, 1995)

---

### Ultrasonic — ORL System

- **Accuracy**: 95% of readings within 14cm (raw); 8cm (averaged over 10 cycles)
- **Range**: ~75m³ (16 receivers); up to ~1100m³ (256 receivers)
- **Pros**: Accurate; scalable; wireless; low power; minimal environmental impact; works in typical office environments
- **Cons**: Limited by ultrasonic reverberation (~20ms decay, caps update rate at ~50Hz); finite transmitter array size; reflections require active error-correction
- **Source**: ORL paper — see [[ORL-Ultrasonic-Location-System]] for full details

---

### WiFi (WLAN)

- **Signal**: RSSI, RTT (IEEE 802.11mc), CSI
- **Accuracy**: Typically 0.5–1m with RSSI fingerprinting + KNN; ~42cm median with probabilistic (Horus); sub-10cm possible with deep learning on dense deployments
- **Pros**: Infrastructure already deployed in most buildings; no additional hardware required; most widely studied indoor positioning technology
- **Cons**: RSSI highly susceptible to multipath, interference, and environmental change; offline survey is labour-intensive; accuracy degrades in dynamic environments without adaptation
- **Foundational systems**: [[RADAR-WiFi-Fingerprinting]] (KNN, INFOCOM 2000); [[Horus-WLAN-System]] (probabilistic, MobiSys 2005); [[E-eyes-Device-Free-Localization]] (CSI device-free, MobiCom 2014)
- **Survey**: [[WiFi-Fingerprinting-Advances]] covers advances beyond RADAR/Horus
- **Source**: Yang et al. 2021 (sensors-21-08086); Bahl & Padmanabhan 2000 (infocom2000.pdf); Youssef & Agrawala 2005 (youssef.pdf); He & Chan 2016 (COMST16_IP.pdf)

---

### Bluetooth / iBeacons (BLE)

- **Signal**: RSSI
- **Accuracy**: 1–3m typical; sub-metre with advanced methods
- **Pros**: Low power; widespread in mobile devices; iBeacons provide simple infrastructure
- **Cons**: RSSI variability; shorter range than WiFi; limited accuracy without fingerprinting
- **Source**: Yang et al. 2021 (sensors-21-08086)

⚠️ **Contradiction**: Aziz & Koo 2025 (applsci-15-01544) claim BLE/iBeacon accuracy of 12cm in healthcare settings. Yang et al. 2021 states 1–3m typical. The 12cm figure is from a specific optimised study; 1–3m is representative of general deployment. Both may be valid depending on algorithm sophistication and environment.

#### BIPS — Bluetooth Indoor Positioning System (Bruno & Delmastro, WMAN 2003)

Bruno, R. and Delmastro, F. *Design and Analysis of a Bluetooth-Based Indoor Localization System.* Lecture Notes in Computer Science, 2003. IIT Institute CNR, Pisa, Italy. Source: `raw/Sci-Hub. Design and Analysis of a Bluetooth-Based Indoor Localization System : Lecture Notes in Comp.pdf`

An early (2003) Bluetooth indoor localization system that takes a distinctive design approach: rather than adding dedicated localization infrastructure, BIPS **integrates device discovery directly into the existing infrastructure-based Bluetooth AP network** that provides wireless connectivity.

**Key design insight**: Bluetooth access points already perform device discovery (the standard BT inquiry procedure that identifies nearby devices). BIPS exploits these existing discovery procedures to determine which BT APs can detect the mobile user — providing proximity-based localization with **negligible overhead** on the existing BT infrastructure.

**Architecture**:
- Infrastructure: Bluetooth APs deployed for wireless connectivity (not specifically for localization)
- Localization: When a mobile device is within BT range of one or more APs, those APs detect it during normal discovery cycles
- Position estimation: Proximity/cell-of-origin based on which APs can detect the device; finer-grained positioning possible with signal strength from multiple APs
- **No dedicated localization hardware** beyond the existing BT AP network

**Evaluation**: Simulative analysis investigating localization update timeliness and system overhead. Results show BIPS achieves timely location updates with negligible impact on BT AP throughput — confirming the viability of piggybacking localization onto existing BT infrastructure discovery.

**Significance**: BIPS represents the integration approach to indoor positioning — exploiting existing network procedures rather than adding dedicated sensor infrastructure. This philosophy anticipates the WiFi passive fingerprinting approach (RADAR, Horus) and later BLE iBeacon-based systems (where the BLE advertising mechanism is similarly re-purposed for localization). The simulative (rather than experimental) evaluation is a limitation noted in the context of the Adler 2015 evaluation methodology survey.

**Relationship to later BT work**: Altini et al. 2010 ([[Indoor-Localization-ML-Methods]]) build on BT infrastructure with neural networks for improved accuracy; the BIPS infrastructure model (standard BT APs) is compatible with the Altini multiple-NN approach.

---

### UWB (Ultra-Wideband)

- **Signal**: TOA, TDOA, AOA
- **Accuracy**: 10–30cm; sub-10cm in controlled environments
- **Pros**: High accuracy; resistant to multipath compared to narrowband radio; good for real-time tracking
- **Cons**: Dedicated infrastructure required; higher cost than WiFi/BT; regulatory constraints on power levels
- **Source**: Yang et al. 2021 (sensors-21-08086)

---

### Zigbee

- **Signal**: RSSI
- **Accuracy**: ~1–3m
- **Pros**: Low power; suitable for IoT sensor networks
- **Cons**: Limited accuracy; lower data rate than WiFi
- **Source**: Yang et al. 2021 (sensors-21-08086)

---

### RFID (Radio-Frequency Identification)

- **Signal**: Presence/RSSI (passive tags); TOA/RSSI (active tags)
- **Accuracy**: Varies widely — proximity-only for passive tags; 1–2m for active
- **Pros**: Low cost (passive tags); no battery in passive tags; long tag lifespan
- **Cons**: Short read range for passive; active RFID adds cost; limited accuracy
- **Source**: Yang et al. 2021 (sensors-21-08086)

#### RFID Localisation for IoT Smart Homes (Alsinglawi et al., IJCNC 2017)

Alsinglawi, B., Nguyen, Q.V., Simoff, S., Ghinea, G., Maher, M. *RFID Localisation in Internet of Things Smart Homes: A Survey.* International Journal of Computer Networks & Communications (IJCNC), 2017.

A detailed survey of RFID localisation algorithms specifically targeting IoT smart home deployments. Three main algorithmic families:

| Algorithm | Principle | Notes |
|---|---|---|
| **LANDMARC** | K-nearest-reference-tag matching using RSSI | Foundational RFID localisation; reference tags on known positions; read RSSI of target vs reference tags |
| **Proximity** | Identify closest reader/reference tag | Coarse; room-level; low computational cost |
| **VIRE** (Virtual Reference Elimination) | Remove virtual reference tags to reduce multipath error | Extension of LANDMARC; improves accuracy by filtering spurious RSSI readings |

**Passive vs active tags**:
- **Passive tags**: No battery; powered by reader RF field; very low cost; read range ~10cm–1m; suitable for item tracking but limited for real-time room-level positioning
- **Active tags**: Battery-powered; transmit independently; read range 10–100m; better for real-time positioning but higher cost and maintenance

**Smart home application focus**: RFID enables ambient assisted living (AAL) — monitoring patient/resident location and activity without requiring the person to carry a separate device (tags embedded in clothing or furniture). Key challenge: multipath in home environments causes RSSI fluctuations that degrade LANDMARC accuracy.

**Source**: `raw/RFID Localisation.pdf`

#### Active RFID Enhancement — LANDMARC Accuracy Improvement (Jin et al., IEEE ICSNS 2006)

Jin, G., Lu, X., Park, M.-S. *An Indoor Localization Mechanism Using Active RFID Tag.* IEEE International Conference on Sensor Networks, Ubiquitous, and Trustworthy Computing (SUTC), 2006. Korea University, Seoul. Source: `raw/Sci-Hub. An Indoor Localization Mechanism Using Active RFID Tag : IEEE International Conference on S.pdf`

A study of the existing LANDMARC active RFID localization system followed by a proposed enhancement to address its accuracy limitations. LANDMARC is the foundational RFID localization approach (using reference tags to reduce reader count and improve accuracy via K-nearest-reference-tag matching); Jin et al. identify specific failure modes and propose algorithmic improvements.

**LANDMARC background**: LANDMARC uses active RFID tags at known positions (reference tags) alongside the mobile target tags. When the target is read by RFID readers, its signal strength relative to the reference tags is used to estimate position via K-nearest-neighbour matching on RSSI. This reduces the number of readers needed (expensive hardware) and provides good coarse-grained localization.

**Problems identified in LANDMARC**:
- Signal strength readings are noisy and non-monotonic at very short ranges (active RFID readers can "over-read" nearby tags)
- Reference tags placed at the boundary of the coverage area introduce edge-case errors in KNN matching
- The fixed selection of K neighbours ignores the heterogeneous RSSI landscape

**Proposed mechanism**: An enhanced reference tag selection algorithm that:
- Applies adaptive weighting to RSSI-based distance estimates
- Filters boundary reference tags that are unlikely to contribute valid position information
- Improves the KNN selection to be more robust to signal noise

**Technology**: Active RFID tags (battery-powered; range 10–100m; transmit periodically); RFID readers at known fixed positions. Lower cost than UWB; longer range than passive RFID; suitable for room-level and sub-room localization in ubiquitous computing environments.

**Significance**: Jin 2006 is part of a line of work improving LANDMARC's practical accuracy for indoor environments, complementing the purely infrastructure-focused work (number and placement of readers) with algorithmic improvements to the tag-matching step.

---

### Ultrasound (modern implementations)

Listed separately from the ORL 1997 system — referenced in Yang et al. as a current technology alongside WiFi, BT, UWB, etc. Used in both indoor and specialised environments. See [[ORL-Ultrasonic-Location-System]] for the canonical historical implementation.

---

### mmWave Radar + IMU — Milli-RIO (Almalioglu et al., Oxford 2020)

Almalioglu, Y., Turan, M., Trigoni, N., Markham, A. *Milli-RIO: Ego-Motion Estimation with Low-Cost Millimetre-Wave Radar.* IEEE SENSORS Journal, 2020. University of Oxford. arXiv:1909.05774.

Milli-RIO uses a **single 77–81 GHz FMCW mmWave radar chip** fused with a **low-cost IMU** to estimate 6-DOF ego-motion (3D translation + 3D rotation). Unlike camera or LiDAR-based odometry, mmWave radar is unaffected by lighting or visual texture, and penetrates smoke, dust, and most non-metallic obstacles.

**Key contributions**:
- **Single-chip FMCW radar**: TI IWR-series chip operating 77–81 GHz; measures range, radial velocity (Doppler), and angle of arrival simultaneously
- **RNN-based motion estimation**: Learns temporal patterns of radar return signals to estimate velocity and rotation; avoids explicit feature extraction (which is fragile for sparse mmWave point clouds)
- **6-DOF output**: Full pose estimation including yaw, pitch, roll and x/y/z translation — unlike many radio IPS which provide only 2D position
- **cm-level precision**: Evaluated on indoor trajectories; competes with visual odometry methods
- **Low cost**: Commodity radar chip; combined with MEMS IMU; no camera or LiDAR required

**Significance for IPS**: Traditional mmWave FMCW radar required large antenna arrays; Milli-RIO demonstrates that a **single low-cost chip** suffices for ego-motion estimation when combined with a learned temporal model. This enables odometry-style localisation in challenging environments (smoke, darkness, featureless corridors) where vision fails.

**Source**: `raw/Almalioglu et al_2020_Milli-RIO.pdf | My Library | Zotero.pdf`

---

### Multi-Sensor Fusion in Dynamic Industrial Environments (Papaioannou et al., IEEE TMC 2017)

Papaioannou, S., Pastorino, M., Balleri, A., Viola, F., Fiorani, F., Langlois, P., Tziortziotis, K., Tuyls, K., Sherif, A. *Tracking People in Highly Dynamic Industrial Environments.* IEEE Transactions on Mobile Computing, 2017. DOI: 10.1109/TMC.2016.2613523.

A system for tracking **construction workers in active construction sites** — one of the hardest indoor tracking scenarios due to moving machinery, constantly changing geometry, and the presence of many non-target moving objects (forklifts, other workers).

**Sensor fusion approach**: Combines four modalities:
| Sensor | Role |
|---|---|
| **CCTV cameras** | Wide-area scene understanding; identity and trajectory from visual detection |
| **WiFi RSSI** | Coarse radio-frequency location; available from worker handsets |
| **IMU (wearable)** | Dead-reckoning between radio updates; footstep and motion detection |
| **UWB** | High-precision ranging in instrumented zones |

**Cross-modality training**: One sensor modality is used to generate labels or supervision signals for another. For example, camera-detected tracks are used to provide ground truth for WiFi/IMU position estimators — reducing the manual labelling burden in a changing environment.

**Social forces model**: Worker motion prediction uses a social forces formulation — workers avoid each other and obstacles, follow paths, and move in groups. Incorporating this prior significantly improves multi-target tracking under occlusion.

**Key challenge addressed**: Dynamic industrial environments invalidate static radio maps; the system adapts to geometry changes without full re-survey.

**Source**: `raw/Papaioannou et al. - 2017 - Tracking People in Highly Dynamic Industrial Envir.pdf | My Library | Zo.pdf`

---

## Device-Based vs. Device-Free Localisation — Taxonomy (Xiao et al., ACM CSUR 2016)

Xiao, J., Zhou, Z., Yi, Y., Ni, L.M. *A Survey on Wireless Indoor Localization from the Device Perspective.* ACM Computing Surveys (CSUR), 2016. HKUST. 31 pages.

Xiao et al. provide a systematic taxonomy distinguishing two high-level localisation paradigms, which cuts across all radio technologies:

| Mode | Definition | Requirements | Privacy |
|---|---|---|---|
| **Device-based (DBL)** | The user's device (phone, badge, tag) participates actively in the localisation process — either computing its own location or transmitting signals that the infrastructure uses to locate it | User carries a device; device has receiver/transmitter and possibly computation | Location can be kept private (if device self-computes); or tracked centrally if device transmits |
| **Device-free (DFL)** | The user is located without carrying any device — detected by how their body perturbs existing radio signals (WiFi, RFID, mmWave, etc.) | Infrastructure-only deployment; no user cooperation required | Inherently centralised and less private — user cannot opt out |

**Significance**: The device-based vs. device-free distinction is orthogonal to the choice of radio technology. Any technology (WiFi, BLE, UWB, RFID) can in principle support either mode. Device-free localisation is particularly relevant for:
- Elderly care (patients who forget or refuse to wear devices)
- Security monitoring
- Crowd counting

The survey (31 pages, ACM CSUR) provides extensive coverage of device-based methods. For device-free methods, see [[E-eyes-Device-Free-Localization]] (CSI-based, MobiCom 2014).

**Source**: `raw/Xiao et al. - 2016 - A Survey on Wireless Indoor Localization from the .pdf | My Library | Zotero.pdf`

## Wearable / Contactless / Fusion Taxonomy (Roohi & Roshan Fekr, Computer Networks 2025)

Roohi, K. and Roshan Fekr, A. *A comparative analysis of indoor localization technologies.* Computer Networks, vol. 270, art. 111527, 2025. DOI: 10.1016/j.comnet.2025.111527. University of Toronto / KITE Research Institute.  Source: `raw/1-s2.0-S1389128625004943-main.pdf`

A 2025 comparative review of 110 indoor localization papers (published 2013–2025) that introduces a **wearable / contactless / fusion** cross-cutting taxonomy orthogonal to the radio-technology classification used elsewhere in this knowledge base. 217 papers were initially retrieved; 135 remained after title/abstract screening; 25 removed on full-text review. Peak in wearable research: 2018–2019. Shift toward contactless and fusion: 2020–2025.

### Three-Framework Taxonomy

| Framework | Definition | Identification method |
|---|---|---|
| **Wearable** | Device or tag physically carried or attached to the tracked subject; receives/transmits signals from fixed APs | Easy — tag or node assigned to known person |
| **Contactless** | No device on the subject; infrastructure detects presence or motion from signal perturbations | Hard — requires gait recognition or auxiliary identification |
| **Fusion** | Combines data from multiple technologies or modalities | Varies — depends on fusion components |

Within each framework, technologies are further classified as RF-based, inertial-based, optical-based, or ultrasound-based.

### Wearable Systems (52 of 135 reviewed papers)

**RF-based wearables (dominant sub-category)** use RSSI, CSI, ToF, TDOA, AoA, or Doppler measurements from multiple APs.

*Wi-Fi* (32 of 52 wearable papers): The most widely used wearable technology. Widespread availability enables cost-effective integration. Best performance: SpotFi (AoA+ToF from CSI) achieves **0.40m MAE**. Range: short to long coverage; generalizability predominantly medium–high. Key challenge: RSSI instability and fingerprint staleness. Gaussian Process outperforms deep learning on dynamic fingerprinting datasets.

*BLE* (7 of 52 wearable papers): Power-efficient; limited range; primarily short coverage. Trilateration + particle filter and trilateration + Kalman filter both achieve **0.53m MAE** in room-sized environments. Smartwatch-based systems achieve **92.10% precision**. Generalizability medium–high.

*UWB* (6 papers): High precision; NLOS resilient; higher cost. EKF-based NLOS mitigation improves RMSE by **~67%**. LSTM achieves **0.07m MAE** in MATLAB simulation. GRU+attention achieves **0.05m MAE** in real indoor setting. Generalizability generally high.

*RFID* (5 papers): Room-level to sub-meter accuracy; passive tags for inventory; active for real-time. CNN-based 3D RFID: **0.095m RMSE**; SAR-based UHF-RFID: **0.06–0.08m** (95th percentile CDF). Transformer-based approach: **0.302m MAE** (2025).

**Inertial-based wearables** use accelerometers, gyroscopes, magnetometers. Benefits: infrastructure-independent; smartphones already equipped. Drawback: cumulative drift — error grows quadratically without external correction. Common techniques: PDR + particle filter, ZUPT, step detection, map matching. Best result among reviewed papers: CNN-based 1D (IMUNet): **1.20m MAE** at long range. Key limitation: initial position must be known; drift makes long-term standalone inertial impractical without fusion.

**Wearable summary accuracy** (Fig. 2a, reviewed papers): Accuracy range **5cm–6m**; **mean 1.66 ± 1.62m**.

### Contactless Systems (49 of 122 reviewed papers)

**Optical-based**: RGB cameras, depth cameras, thermal cameras, PIR sensors. High accuracy under controlled conditions. Best results: multi-camera VLP **0.0017m MSE** (2024); depth camera **0.1m MAE**; LiDAR + cosine similarity **83% robustness**. Primary challenges: LOS requirements, lighting sensitivity, privacy concerns (RGB captures identifiable features). Privacy-preserving alternatives: thermal imaging, depth-only imaging sacrifice some accuracy.

**RF-based contactless**: mmWave radar and WiFi CSI. Detect body-induced signal perturbations without any device. mmWave CNN: **0.04m MAE** (human skeletal pose); LSTM+MLP for 3D mesh: **0.025m MAE**; WiFi CSI with IRS: **<0.1m RMSE** for multi-person. Key advantage: privacy-preserving (no visual capture). Key limitation: site-specific; requires per-environment retraining; limited generalizability.

**Ultrasound-based**: High accuracy in short-range structured environments. Best result: Spring-relaxation in 1.2m×1.2m testbed: **0.02m** (90th percentile CDF); MEMS ultrasonic chip: **0.034m MSE** (3D). Limitations: rapid attenuation, NLOS sensitivity, limited range.

**Contactless summary accuracy** (Fig. 2b): Accuracy range **2cm–2.5m**; **mean 0.39 ± 0.55m**.

⚠️ **Key finding**: Contactless systems substantially outperform wearable systems in reported accuracy (0.39m vs 1.66m mean). However, contactless systems cannot easily identify *which* subject is being tracked (unlike wearables where a tag is assigned to a known person), limiting their applicability for personalized services.

### Fusion Systems (partial overlap with above)

Fusion systems combine data from multiple modalities to compensate for individual weaknesses. Common pairings and performance:

| Sensor combination | Best technique | Best result |
|---|---|---|
| Camera + RF (ResNet CNN) | Triangulation | **0.04m MSE** |
| IMU + UWB (U-PDR) | EKF + DNN | **0.08m MAE** |
| IMU + Wi-Fi (AAResCNN) | CSI fingerprinting + deep trajectory | **0.016–0.598m MSE** |
| IMU + BLE (3D-LBMS) | Adaptive unscented Kalman filter | **1.02m** (75th CDF) |
| Ultrasound + BLE | Triangulation | **0.39m MAE** |
| Magnetometer + BLE + Wi-Fi | Pattern matching + PDR + PF | **2.6m MSE** (hospital, long range) |

**Key finding**: Fusion does NOT always outperform single-modality systems — range 3.5m to 0.03m across reviewed papers. High generalizability (>70% of fusion methods rated medium–high). Main cost: computational complexity limits real-time edge deployment.

### Recommended Technologies by Application (Table 10)

| Application | Recommended technology | Rationale |
|---|---|---|
| Elderly monitoring at home | mmWave Radar | High privacy; short-range fine-grained motion sensing |
| Asset tracking (hospital/retail) | BLE or RFID | Low cost; adequate room-level precision |
| Patient-nurse tracking | Wi-Fi / mmWave + IMU | Generalizability; scalable across multi-room hospital |
| Warehouse robotics | UWB + INS | Sub-meter accuracy; NLOS robustness |

**Research gaps identified** (Roohi & Roshan Fekr 2025):
- Subject identification in contactless systems remains an open problem
- Limited generalizability of high-accuracy contactless systems across environments
- Computational efficiency vs. accuracy trade-off prevents real-time edge deployment
- Fusion strategies need multimodal temporal modelling rather than data-level integration

---

## Technology Selection Summary

| Technology | Accuracy | Range | Cost | Infrastructure | Best For |
|---|---|---|---|---|---|
| IR (Active Badge) | Room | Room | Low | Sensor network | Room-level presence |
| EM Tracker | ~1mm | Few metres | High | Tethered | Lab/precision tracking |
| Optical | ~mm | Constrained | High | Complex | Augmented reality |
| GPS/Radio | ~50cm+ | Wide area | Low | Existing | Outdoor only |
| Ultrasonic (ORL) | ~8–14cm | 75–1100m³ | Medium | Ceiling receivers | Fine-grain indoor |
| WiFi | 0.5–1m | Building | Low | Existing | General indoor |
| BLE/iBeacon | 1–3m | Room/zone | Low | Low-cost tags | Proximity/zone |
| UWB | 10–30cm | Building | Medium-High | Dedicated | High-accuracy tracking |
| RFID | Variable | Short | Low (passive) | Tags + readers | Asset tagging |
| Zigbee | 1–3m | Building | Low | Sensor network | IoT/low-power |

### FMCW RADAR / mmWave

- **Signal**: Frequency-modulated continuous wave electromagnetic; mmWave frequencies
- **Accuracy**: High; LSTM-based approaches achieve cm-level ranging; range/velocity/AoA simultaneously
- **Range**: Up to 300m; FoV adaptable up to 120°; multiple units can be cascaded
- **Pros**: Penetrates obstacles; provides range, velocity, and angle in one measurement; works regardless of lighting or weather; enables device-free positioning
- **Cons**: Higher cost; regulatory constraints; requires ML for robust indoor use; complex signal processing
- **Notes**: FMCW + mmWave is increasingly standard for indoor localization. Multiple radars cascaded for wider FoV. Time-frequency analysis extracts additional signal properties.
- **Source**: Sonny et al. 2024 (2403.04333)

---

### Visible Light Communication (VLC) / LED Positioning

- **Signal**: Visible or modulated LED light
- **Accuracy**: Sub-metre achievable; performance limited by LoS requirements
- **Pros**: Uses existing lighting infrastructure; no RF interference; privacy-preserving in some respects
- **Cons**: Requires line-of-sight; affected by ambient light; receivers must be in illuminated zones; limited to 2D unless multiple sources used
- **Notes**: LEDs can be modulated to encode position data. Photodetectors or cameras act as receivers.
- **Source**: Sonny et al. 2024 (2403.04333)

---

### Sound Source Localization (SSL) / Acoustic

- **Signal**: Sound / ultrasound via microphone arrays
- **Accuracy**: Environment-dependent; challenged by multipath, reverberation, low SNR
- **Pros**: No special hardware on tracked subject; uses standard microphones
- **Cons**: Multipath effects and reverberation cause errors; low SNR in noisy environments; limited range
- **Techniques**: TDOA, IDOA (Intensity Difference of Arrival), PDOA (Phase Difference of Arrival) using microphone arrays; combined with ML (probabilistic neural networks)
- **Applications**: Human-robot interaction, teleconferencing, automatic speech recognition, robot patient location
- **Source**: Sonny et al. 2024 (2403.04333)

---

### Magnetic Sensors / Geomagnetic Fingerprinting

- **Signal**: Earth's magnetic field (perturbed by building structures)
- **Accuracy**: Varies; magnetic anomaly patterns are location-unique but require careful calibration
- **Pros**: No dedicated infrastructure; uses smartphone magnetometer; works without network connectivity
- **Cons**: Signal variability; device heterogeneity affects readings; dynamic environmental effects (moving metal objects); limited range per fingerprint map; requires accurate real-time updates
- **ML methods used**: TCN, LSTM, RNN, DNN, ELM, ANN, XGBoost, CNN — see [[Indoor-Localization-ML-Methods]]
- **Source**: Sonny et al. 2024 (2403.04333)

---

### LiDAR

- **Signal**: Pulsed laser light (time-of-flight)
- **Accuracy**: Very high (cm-level point cloud resolution)
- **Pros**: Produces detailed 3D maps; enables SLAM (Simultaneous Localisation and Mapping)
- **Cons**: Expensive; high data volume; computationally intensive; affected by transparent or reflective surfaces
- **Applications**: Autonomous robot navigation, building mapping, precision asset localisation
- **Source**: Sonny et al. 2024 (2403.04333)

---

## Hybrid Localization Systems

No single technology excels across all indoor environments and use cases. Hybrid systems combine two or more technologies to offset individual weaknesses (Aziz & Koo 2025):

| Combination | Rationale |
|---|---|
| BLE + UWB | Low-cost broad coverage (BLE) + high-precision zones (UWB) |
| Wi-Fi + INS | Radio positioning + inertial dead-reckoning when signal degrades |
| BLE + Wi-Fi + RFID | Multi-layer coverage for large facilities |
| UWB + ZigBee | High-accuracy tracking + low-power mesh networking |

INS (Inertial Navigation Systems) using MEMS-based accelerometers and gyroscopes are increasingly integrated as a fall-back for GNSS-denied or RF-degraded zones — particularly in military and emergency response contexts.

## Entity Summary

### Hardware
- **IR (Active Badge)**: Wearable IR transmitter + fixed sensor network; medium = infrared light; ORL/Cambridge
- **Electromagnetic trackers**: Wired tethered coils; medium = magnetic field; Ascension Technology "Flock of Birds"
- **Optical trackers**: Camera/laser systems; medium = visible/IR light; constrained environments
- **GPS/LORAN**: RF receivers; medium = radio waves; outdoor/wide-area
- **Ultrasonic (ORL)**: 40KHz ultrasonic transducers + 418MHz RF; ceiling-mounted receivers; see [[ORL-Ultrasonic-Location-System]]
- **WiFi APs**: Standard 802.11 access points; medium = 2.4/5GHz radio; existing infrastructure
- **BLE beacons / iBeacons**: Low-power Bluetooth transmitters; medium = 2.4GHz radio; coin-cell or USB powered
- **UWB anchors**: Dedicated UWB infrastructure nodes; medium = ultra-wideband radio (3.1–10.6GHz impulse)
- **RFID**: Passive tags (no battery) + active tags (battery); readers at fixed points; medium = RF (various)
- **Zigbee**: IEEE 802.15.4 mesh nodes; medium = 2.4GHz radio
- **FMCW RADAR / mmWave**: Radar transceiver modules (e.g., TI IWR series); medium = mm-wave RF (77GHz typical); FoV up to 120°
- **VLC / LED**: Modulated LED luminaires + photodetector or camera receivers; medium = visible light
- **Acoustic / SSL**: Microphone arrays (≥2 mics); medium = sound/ultrasound
- **Magnetic**: Smartphone magnetometer; no dedicated infrastructure; medium = Earth's magnetic field perturbations
- **LiDAR**: Pulsed laser scanner; medium = near-IR laser; produces 3D point cloud
- **RFID-RC522 module**: 13.56 MHz RFID reader/writer; antenna coil + microcontroller; interfaces via SPI with Arduino/Raspberry Pi; read range 2–5 cm for passive tags; used in healthcare, industrial, and retail RTLS (Aziz & Koo 2025)
- **ESP8266 Wi-Fi module**: 2.4 GHz 802.11 b/g/n; client mode + access point mode; +25 dBm TX power; GPIO pins for sensor/actuator interfacing; widely used in IoT RTLS and hospital tracking of assets and patients (Aziz & Koo 2025)
- **HC-05 Bluetooth module**: 2.4 GHz Bluetooth 2.0+EDR; UART interface; master + slave modes; 3.6–6V; up to 10m range; up to 2 Mbps data rate; used in BLE RTLS wearables for patient/staff/equipment tracking (Aziz & Koo 2025)
- **ZigBee XBee shield**: IEEE 802.15.4 at 2.4 GHz; mesh networking; configurable DIN/DOUT + GPIO pins; RST button + ASSOCIATE LED; 3.3V regulated; suitable for home automation, healthcare monitoring, IoT networks (Aziz & Koo 2025)
- **BU01 UWB module (DW1000 chip)**: 3.1–10.6 GHz UWB; centimetre-level accuracy; uses TOA/trilateration; LSTM integration achieves **7 cm mean error** in NLOS conditions; used in RTLS for patients and industrial asset tracking (Aziz & Koo 2025; Poulose & Han 2020 — see [[UWB-LSTM-Localization]])

### Software
- **WiFi fingerprinting**: RSSI radio map databases; various open-source implementations (see [[Indoor-IPS-Datasets]])
- **UWB SDK**: Vendor SDKs (e.g., Decawave/Qorvo DW1000); typically C/embedded; proprietary
- **FMCW signal processing**: Python/MATLAB DSP toolchains; vendor SDKs (TI mmWave SDK); often proprietary
- **LiDAR SLAM**: Open-source frameworks (Cartographer, LOAM, HDL-Localization); C++/ROS
- **VLC drivers**: Custom modulation/demodulation firmware; research prototypes; no standard open-source stack
- **Acoustic SSL**: OpenSSL-style research code; various MATLAB implementations

### Algorithm
- **IR**: Presence detection — binary sensor/room mapping (no ranging)
- **WiFi / BLE RSSI**: Trilateration (distance from RSSI via path-loss model) or fingerprint matching (KNN, DNN)
- **WiFi RTT (802.11mc)**: Two-way ranging; more accurate than RSSI
- **UWB**: TOA / TDOA ranging → multilateration; AOA with antenna arrays
- **RFID passive**: Proximity detection; RFID active: RSSI trilateration
- **FMCW RADAR**: Fast Fourier Transform on beat frequency → range; Doppler shift → velocity; MUSIC/ESPRIT for AoA; LSTM/CNN for classification
- **VLC**: LED modulation ID → known anchor position → AOA or trilateral
- **Acoustic SSL**: TDOA via cross-correlation of microphone pair signals; IDOA; PDOA; ML fusion
- **Magnetic**: Geomagnetic fingerprinting — compare measured field vector to pre-built map; LSTM/DNN matching
- **LiDAR**: ICP (Iterative Closest Point) or NDT for SLAM; particle filter localisation on prior map

### Accuracy
- **IR (Active Badge)**: Room-level
- **EM tracker**: ~1mm, ~0.2° — tethered, short range
- **Optical**: ~mm-level — constrained environments
- **GPS indoors**: ~50cm+ — typically unusable
- **Ultrasonic (ORL)**: 8–14cm — see [[ORL-Ultrasonic-Location-System]]
- **WiFi RSSI**: 0.5–1m typical; improves with ML fingerprinting
- **BLE RSSI**: 1–3m typical; sub-metre with AoA (BT 5.1) ⚠️ see contradiction note in article body
- **UWB**: 10–30cm; sub-10cm with LSTM (7cm, Sonny et al. 2024)
- **RFID passive**: Proximity only; active: 1–2m
- **Zigbee**: ~1–3m
- **FMCW mmWave**: cm-level ranging with LSTM; simultaneous range + velocity + AoA
- **VLC**: Sub-metre achievable (LoS required)
- **Acoustic SSL**: Environment-dependent; challenged by reverberation
- **Magnetic**: Room-level to ~1m depending on ML method
- **LiDAR**: cm-level point cloud; SLAM enables continuous precision localisation

## Deployment Challenges

*(Sources: Yassin et al. IEEE COMST 2017 `raw/077620951.pdf`; Zafari et al. IEEE Comm. Surveys 2019 `raw/A_Survey_of_Indoor_Localization_Systems_and_Techno.pdf`)*

### Multipath and Noise
The fundamental challenge of indoor localization. Signals reflect, refract, and diffract off walls, metal, and people. Approaches relying on RSSI, ToF, TDoA, or AoA all receive multiple phase-delayed, power-attenuated copies of the original signal. Identifying the direct LOS path (if it exists) requires complex signal processing — which may be impractical on energy-constrained user devices (DBL). Chirp Spread Spectrum is one candidate for multipath-robust transmission.

### Radio Environment Dynamics
Indoor environments change: furniture moves, people enter/exit, density varies across time of day. Most systems are evaluated in controlled environments that don't replicate real-world conditions. Fingerprinting-based approaches are particularly vulnerable — the offline radio map becomes stale. CSI-based approaches (see [[E-eyes-Device-Free-Localization]]) are more robust to environment dynamics than RSSI. Systems should be designed for worst-case scenarios.

### Energy Efficiency
Continuous localization drains device batteries: the user device must periodically monitor wireless channels and pick up beacon signals. For DBL in particular, this conflicts with the device's primary purpose. Solutions include offloading computation to local/cloud servers and designing less-complex, energy-efficient algorithms. Latency must also be optimised when offloading (to maintain real-time updates).

### Security and Privacy
User location is sensitive information — among the most privacy-compromising data a system can collect. Specific threats in WLAN-based IPS:
- Mobile devices collect AP IDs and RSSI measurements → personal/infrastructure data exposed to IPS server
- Malicious nodes can infiltrate systems with weak authentication mechanisms
- Traditional key-based security systems are too computationally expensive for energy-constrained devices

**Approaches**: Self-localizing architectures (location computed on user device, not reported to server) provide stronger privacy. Location support systems (user discovers services based on own location without broadcasting position) are preferable to location tracking systems. Legislation and user consent frameworks are also required for wide-scale deployment.

*(Both Yassin et al. 2017 and Zafari et al. 2019 flag security/privacy as among the top challenges blocking wide-scale IPS adoption.)*

### Scalability
A scalable IPS must perform across two axes:
- **Geographic scaling**: Coverage area and volume (2D floor, 3D multi-storey)
- **Density scaling**: Number of units positioned per unit area per time

Wireless channels may become congested as coverage area or user density increases. Most published systems address 2D only; standardisation bodies (e.g., FCC) increasingly require 3D accuracy. Future 5G/mmWave deployments are expected to enable centimetre-level location estimation at scale (Yassin et al. 2017).

### Lack of Standardisation
No single wireless technology or methodology has been standardised for indoor localization. Systems are largely disjoint and non-interoperable. The ISO/IEC JTC1/SC31 working group (and ISO/IEC 18305) provide some evaluation metrics, but no governing specification for IPS design exists. The 3GPP has dedicated bearers for MTC/IoT; similar standardisation is needed for localization-as-a-service (ILPaaS).

### Handovers
Future networks will be heterogeneous — combining WiFi, BLE, UWB, cellular. An IPS relying on multiple technologies needs:
- **Vertical handovers**: Between technologies (e.g., switching from BLE to UWB for high-accuracy zones)
- **Horizontal handovers**: Between nodes of the same technology as the user moves out of range

Handovers must be fast (real-time tracking) and low-energy — stringent requirements that conflict with existing handover algorithm complexity.

## 5G and Future Localization

*(Source: Yassin et al. IEEE COMST 2017, Section IX)*

5G networks are expected to use precise location estimation across all protocol stack layers. Centimetre-level accuracy is targeted. Key enablers:
- **mmWave frequencies**: High bandwidth → fine ranging; large antenna arrays → precise AoA; short range requires dense deployment but enables high landmark density
- **Location-aware resource allocation**: SNR is inversely proportional to distance (pathloss); location can predict interference levels and optimal multi-hop paths
- **Cyber-physical systems**: Smart transportation, robotics, and IoT all require sub-metre localization integrated into the communication layer itself

mmWave localization is still an open research area; accuracy and practical deployment methods are unresolved at the time of both surveys (2017, 2019).

## Visible Light Communication (VLC) for Indoor Localization — Detailed Survey

Rahman, A.B.M.M., Li, T., and Wang, Y. *Recent Advances in Indoor Localization via Visible Lights: A Survey.* Sensors 2020, 20, 1382. DOI: 10.3390/s20051382. UNC Charlotte / Oxford College / Temple University. Source: `raw/sensors-20-01382-v2.pdf`

The brief VLC entry above (from the 2024 ML survey) is substantially expanded here from this dedicated 26-page VLC survey.

### Physical Principle
LEDs and fluorescent lights are modulated to transmit unique identifiers at frequencies above the human flicker threshold (>1000 Hz). A smartphone camera or dedicated photodetector (PD) at the receiver decodes the modulated light to identify which LED(s) are visible and infers position from their known locations.

**Key advantages over radio-based systems**:
- No RF interference; unaffected by WiFi/BT congestion
- Inherently secure — light does not penetrate walls (no eavesdropping from adjacent rooms)
- High accuracy: VLC systems regularly achieve **centimetre to sub-10cm** accuracy, substantially better than WiFi or BLE
- Low cost: LED photodiodes cost <$3; LED bulbs are already deployed in most modern buildings
- Dual-purpose: same LED infrastructure serves both illumination and positioning

**Key challenges**:
- **Line-of-sight requirement**: Light cannot penetrate walls or opaque obstacles; any occlusion causes localisation failure
- **Ambient light interference**: Sunlight and other light sources introduce noise into PD measurements
- **Limited coverage**: Receivers must be within illuminated zones; dark areas are unserviced
- **Modulation complexity**: High-frequency LED modulation requires hardware modifications in some approaches

### Receiver Types

**Imaging sensors (cameras)**: Smartphone camera captures image of LED(s) in field of view; image processing decodes modulated IDs and/or uses geometric AOA calculation. Examples: Luxapose (unmodified phone + slightly modified luminaries), PIXEL, FogLight.

**Photo-detector (PD) sensors**: Dedicated PD arrays with higher dynamic range than cameras; capture LED internal patterns and angular emissions. Examples: Pulsar (AoA with PD array), EPSILON (trilateration via RSS + IMU fusion with ≥3 or <3 LEDs).

### Localization Methods in VLC

| Method | Technique | Representative System |
|---|---|---|
| AOA (Angle of Arrival) | Image or PD geometry → bearing to LED | Luxapose, PIXEL, POLI |
| RSS Trilateration | RSS from ≥3 LEDs → range + trilateration | Epsilon, DIMLOC |
| PWM frequency ID | Each LED broadcasts unique frequency; receiver FFT decodes ID | LiTell, SpinLight |
| Light intensity fingerprinting | Light intensity map as fingerprint | NaviLight (DTW matching) |
| Image-based geometry | Camera captures LED array pattern; computes position from LED geometry | FogLight (shadow-based), SmartLight (LED array geometry) |
| Fusion with IMU | RSS + accelerometer + gyroscope for ≥3 / <3 LED coverage | Epsilon, EyeLight |

### Accuracy Summary (from survey Table 3)

- **Best reported (FogLight)**: Shadow-based; highest accuracy in survey
- **Imaging sensor systems**: Generally higher accuracy than PD systems due to more spatial information in images
- **Typical range**: 0.01–0.3m for LoS imaging systems; 0.1–1m for PD-based trilateration
- **Compared with WiFi/BLE**: VLC consistently outperforms; best WiFi/BLE systems achieve cm-level but only in dense AP environments
- **LoS limitation**: All reviewed systems fail without direct line-of-sight; EyeLight partially addresses this using shadow information but at reduced accuracy

### Future Directions (2020)
- Integration with other technologies (WiFi, UWB, IMU) to maintain positioning through LoS interruptions
- Advanced ML (deep learning on light intensity sequences) — underexplored at time of survey
- Device-free VLC: passive localisation using existing light flicker patterns (LiTell approach)
- Better solutions for dimmable LED environments (DIMLOC addresses but needs further work)

---

## 3D Indoor Localization — Survey

Sesyuk, A., Ioannou, S., and Raspopoulos, M. *A Survey of 3D Indoor Localization Systems and Technologies.* Sensors 2022, 22, 9380. DOI: 10.3390/s22239380. University of Central Lancashire / UCLan Cyprus. Source: `raw/sensors-22-09380.pdf`

The overwhelming majority of published indoor localization systems address **2D positioning only** (x, y coordinates on a single floor). This 33-page survey specifically reviews 3D systems, which also determine height (z) or floor — critical for multi-storey buildings and vertical navigation.

### Why 3D Matters

- Multi-storey buildings: elevator/staircase navigation requires knowing which floor the user is on
- Emergency response: firefighters and responders need 3D position to coordinate in high-rise buildings
- Industrial warehouses: forklifts and drones operate at multiple heights; collision avoidance requires 3D
- Precision manufacturing: components may need cm-level 3D positioning for robotic assembly guidance

### Geometric Approaches in 3D

| Technique | 3D Extension | Key Challenge |
|---|---|---|
| AOA (Angle of Arrival) | 2D bearing + elevation angle → 3D ray intersection | Elevation angle estimation requires height of anchor nodes and precise angle measurement |
| ToA (Time of Arrival) | Range-based; trilateration with ≥4 non-coplanar anchors gives unique 3D solution | Clock synchronisation; 4 non-coplanar anchors required (vs. 3 in 2D) |
| TDoA (Time Difference) | 4 anchors; TDOA system is non-linear in 3D — requires iterative solver | Hyperboloid intersection; numerical instability near anchor plane |
| RSS Fingerprinting | 3D fingerprint map; grid includes z-coordinate | Survey effort increases proportionally with floors/height; large training set needed |

### Fingerprinting Approaches in 3D

- **RSS fingerprinting**: Most common extension — add floor/height as a label to each fingerprint. Floor estimation typically uses separate classifier (SVM, random forest on floor-level signal features) before room-level positioning.
- **CSI fingerprinting**: Per-subcarrier CSI contains multipath profile which implicitly encodes height — 3D CSI fingerprinting shows promising results
- **Magnetic Field (MF)**: Magnetic anomalies vary by height (floor of building); multi-floor MF maps distinguish floors naturally; see [[Magnetic-Field-Localization]]
- **Fine Time Measurement (FTM)**: 802.11mc WiFi ranging protocol; enables ToA-based ranging without hardware modification; applicable to 3D with sufficient non-coplanar APs

### Technologies for 3D Localisation

- **WiFi**: Most deployed; floor estimation first (signal patterns differ by floor), then 2D positioning on that floor; ~floor-level 3D accuracy is achievable
- **Bluetooth/BLE**: Similar to WiFi but shorter range; requires denser beacon deployment for equivalent coverage; barometric pressure sensors in phones augment BLE for vertical positioning
- **UWB**: Best 3D geometric accuracy among radio technologies; TOA ranging achieves cm-level in 3D with non-coplanar anchor placement; [[UWB-LSTM-Localization]] is primarily 2D
- **mmWave**: High bandwidth → fine ranging; large arrays → precise AOA including elevation; Milli-RIO [[Indoor-Location-Sensor-Technologies]] achieves 6-DOF including height
- **Visible Light**: Generally limited to 2D (ceiling-to-floor); multi-height LED arrays enable 3D estimation; accuracy deteriorates significantly in 3D
- **Sound**: TOA-based acoustic systems ([[Cricket-Location-Support-System|Cricket]], [[ORL-Ultrasonic-Location-System|ORL]]) can achieve 3D with 4 non-coplanar ultrasonic beacons; ceiling-mounted transmitters provide natural height reference

### Fusion-Based 3D Positioning

Hybrid approaches consistently outperform single-technology 3D positioning:
- Barometric pressure sensor + WiFi/BLE: pressure disambiguates floor; WiFi/BLE provides 2D within floor
- IMU + floor estimation: accelerometer measures elevation changes (stairs, escalators) → floor transition detection
- UWB + IMU: UWB anchors provide 3D fixes; IMU bridges between fixes; achieves dm-level continuous 3D tracking

---

## Comparative Technology Review (Obeidat et al., 2021)

Obeidat, H., Shuaieb, W., Obeidat, O., and Abd-Alhameed, R. *A Review of Indoor Localization Techniques and Wireless Technologies.* Wireless Personal Communications, 119:289–327, 2021. DOI: 10.1007/s11277-021-08209-5. Jerash University / Omar Al-Mukhtar University / Wayne State University / University of Bradford. Source: `raw/s11277-021-08209-5.pdf`

A 39-page review that specifically compares indoor localization techniques (AOA, TOA, RSS) across all wireless technologies (WiFi, BT, RFID, FM, acoustic, inertial, infrared) in terms of accuracy, cost, advantages, and disadvantages. Primary contribution is the **structured comparison table** across technology × technique combinations.

### Measurement Technique Comparison

| Technique | Principle | Accuracy | Cost | Notes |
|---|---|---|---|---|
| AOA (Angle of Arrival) | Bearing angles from ≥2 anchors | 0.1–2m | High | Requires directional antenna arrays; line-of-sight preferred; multipath degrades bearing accuracy |
| TOA (Time of Arrival) | Propagation time × speed of light | 0.1–1m | High | Tight clock synchronisation required; 4 non-coplanar anchors for 3D |
| TDoA (Time Difference of Arrival) | Difference in TOA from multiple anchors | 0.1–1m | Medium | Relaxes sync requirement (receiver measures time difference); hyperbolic solution |
| RSS/RSSI | Signal strength → distance via path-loss model | 1–5m (model), <1m (fingerprint) | Low | No hardware modification; accuracy sensitive to multipath and environment |
| RSS Fingerprinting | Pattern matching vs. pre-surveyed radio map | 0.5–3m | Low (labour-intensive survey) | Site survey required; best accuracy for RSS-based methods |
| Hybrid | Combines ≥2 techniques | 0.1–1m | Medium | Best practical accuracy; adds complexity |

### Technology Comparison Summary

| Technology | Typical Accuracy | Cost | Infrastructure | Notes |
|---|---|---|---|---|
| WiFi (RSS fingerprint) | 1–5m | Low | Existing | Ubiquitous; device heterogeneity problem |
| Bluetooth/BLE (beacons) | 1–3m | Low | Beacon install | Battery-powered beacons; BLE 5.0 improves ranging |
| UWB | 0.1–0.3m | High | Dedicated | Best radio accuracy; regulatory constraints on power |
| RFID | Room-level | Low-Med | Requires RFID readers | Good for asset tracking; limited positioning precision |
| FM Radio | 2–3m (city scale) | None | Existing FM | Uses existing FM broadcast infrastructure; coarse |
| Acoustic / Ultrasound | 0.01–0.1m | Medium | Custom | High accuracy but limited range; see [[ORL-Ultrasonic-Location-System]] |
| Infrared | Room-level | Low | IR beacons | Active Badge approach; see [[Active-Badge]] |
| Inertial (IMU) | 1–3m (short term) | Low | None | Drifts unbounded; requires periodic correction |
| GPS (indoors) | N/A | None | Satellite | GPS signals attenuated to unusable levels indoors |

### Practical Applications Covered
- Supermarket navigation (WiFi + RFID hybrid cart tracking)
- Museum/gallery visitor guidance (BT + WiFi)
- Library book location (BLE beacons)
- Emergency response in buildings
- IoT manufacturing and asset tracking

The review recommends hybrid RSS fingerprinting + TOA as the best accuracy-cost trade-off for most practical deployments (2021). Full automation of the fingerprint survey process (crowdsourcing, SLAM) is identified as the key unsolved deployment challenge.

---

## Relationships

- [[ORL-Ultrasonic-Location-System]] — the ultrasonic system described in detail
- [[Active-Badge]] — the IR system; earliest practical indoor location technology
- [[Indoor-Localization-ML-Methods]] — ML methods that improve accuracy of radio-based technologies (WiFi, BT, UWB)
- [[Indoor-Localization-Applications-by-Sector]] — how technologies map to healthcare, industrial, military, retail use cases
- [[Indoor-IPS-Datasets]] — public datasets available for benchmarking each technology
- [[Context-Aware-Computing]] — the application domain driving demand for indoor location
- [[UWSN-Localization]] — underwater sensor networks face analogous but distinct challenges
- [[UWB-LSTM-Localization]] — deep dive into UWB+LSTM localization achieving 7cm mean error in simulation; source of the LSTM-UWB accuracy figure cited here
- [[UnLoc-Unsupervised-Localization]] — uses WiFi + IMU + magnetic sensors; deployment challenges (calibration overhead) are core motivation
- [[Walkie-Markie-Indoor-Mapping]] — uses WiFi RSS trend + IMU; device-independent WiFi-Marks overcome device heterogeneity challenge
- [[Indoor-Localization-Applications-by-Sector]] — Papaioannou et al. construction site tracking covered in industrial applications; RFID smart home in healthcare/home-care sector
- [[E-eyes-Device-Free-Localization]] — device-free CSI-based localisation; complements device-based taxonomy (Xiao et al.) and device-free RFID approaches
- [[Pedestrian-Dead-Reckoning]] — Milli-RIO (mmWave+IMU) is a radar-based alternative to PDR for ego-motion estimation in GPS-denied environments; both achieve cm-to-dm precision without infrastructure
- [[Magnetic-Field-Localization]] — magnetic fingerprinting is one technology surveyed here; the dedicated article provides full depth on DTW, SLAM, calibration, and PDR fusion
- [[CSI-Indoor-Localization]] — CSI is listed in Sesyuk 3D survey as a fingerprinting signal type for 3D localization; FILA article provides full depth

---
tags: [indoor-localization, datasets, benchmarks, WiFi, BLE, UWB, research-resources, evaluation-methodology, IPIN]
date-compiled: 2026-06-15
source-files:
  - "raw/2403.04333v1.pdf"
  - "raw/Barsocchi et al. - 2016 - A multisource and multivariate dataset for indoor .pdf | My Library | Zote.pdf"
  - "raw/Toth and Tamas - 2016 - Miskolc IIS hybrid IPS Dataset for hybrid indoor .pdf | My Library | Zotero.pdf"
  - "raw/Alhomayani and Mahoor - 2021 - Oversampling Highly Imbalanced Indoor Positioning .pdf | My Library |.pdf"
  - "raw/Sci-Hub. The Microsoft Indoor Localization Competition: Experiences and Lessons Learned : IEEE Signa.pdf"
  - "raw/sensors-17-02327-v2.pdf"
  - "raw/Sci-Hub. RSSI-Based Indoor Localization With the Internet of Things : IEEE Access, 2018.pdf"
  - "raw/Sci-Hub. A survey of experimental evaluation in indoor localization research : 2015 International Co.pdf"
status: active
---

# Indoor Positioning System — Public Datasets

## Summary

A catalogue of publicly available datasets for indoor positioning research, drawn from Sonny, Kumar & Cenkeramaddi (*"A Survey of Application of Machine Learning in Wireless Indoor Positioning Systems"*, arXiv:2403.04333v1, 2024). These datasets support benchmarking and training of [[Indoor-Localization-ML-Methods]] across a range of technologies. They vary in technology, scale, dimensionality, and year of publication.

## Dataset Table

| Name | Technology | Year | Dimension | Scale | Points |
|---|---|---|---|---|---|
| CRAWDAD (King et al.) | RSSI | 2008 | 1D | 221 m², 1 floor | 146,080 / 6,600 |
| KIOS dataset | Wi-Fi RSSI | 2013 | 1D | 560 m², 1 floor | 2,100 |
| UJIIndoorLoc | Wi-Fi | 2014 | 3D | 108,703 m² | 19,937 / 1,111 |
| UJIIndoorLoc-Mag | Magnetic | 2015 | 1D | 260 m², 1 floor | 270 / 11 |
| KTH/RSS Dataset | RSSI | 2016 | 1D | 400 m², multiple rooms | 1,689 |
| Z. Tóth & J. Tamás | WLAN, BLE | 2016 | 3D | 3 floors | 1,571 |
| Barsocchi et al. | Wi-Fi, Geomagnetic | 2016 | 1D | 185 m², 3 rooms, 4 hallways | 680 / 460 |
| Matterport3D | RGB-D Image | 2017 | 3D | 90 building-scale scenes | 194,400 |
| Tampere database | Wi-Fi | 2017 | 3D | 22,570 m², 4 floors | 687 / 3,951 |
| Minho database | Wi-Fi RSSI | 2017 | 1D | 1,000 m² | 4,973 / 810 |
| IPIN2017 Tutorial | Wi-Fi RSSI | 2017 | 1D | School corridor | 927 / 702 |
| InLoc | RGB-D Image | 2018 | 3D | 5 floors, 2 buildings | 277 |
| Baronti et al. | BLE | 2018 | 1D | 185 m², 7 rooms | 2,598 |
| Bristol database | BLE RSSI | 2018 | 1D | Multiple houses | 1,571 |
| D. Sikeridis et al. | BLE | 2018 | 3D | 3 floors | — |
| Mendoza-Silva et al. | Wi-Fi | 2018 | 3D | 308.4 m², 2 floors | 576 / 3,120 |
| JUIndoorLoc | RSSI | 2019 | 3D | 882 m², 5 floors | 23,904 / 1,460 |
| S. Sadowski et al. | Zigbee, Bluetooth, Wi-Fi | 2020 | 1D | 33–79 m², 3 rooms | 73 / 32 |
| F. Potortì et al. | Wi-Fi, Magnetic, Camera | 2020 | 3D | 6,000 m², 3 floors | — |
| SODIndoorLoc | Wi-Fi | 2022 | 3D | 8,000 m², 3 buildings, 3 floors | 21,205 / 2,720 |
| P. Pascacio et al. | BLE, RSSI | 2022 | 1D | 180 m² | 178,487 ± 85,824 |
| TUJI1 Dataset | Wi-Fi | 2023 | 1D | Multiple offices | 6,752 / 2,147 |

*Point counts shown as "train / test" where both are given.*

## Notable Datasets

**UJIIndoorLoc** — one of the most widely used benchmarks. 3D, covers 108,703 m² across multiple buildings. Wi-Fi RSSI fingerprints. Standard baseline for comparing localization algorithms.

**Matterport3D** — large-scale RGB-D image dataset covering 90 building-scale scenes, useful for vision-based and fusion approaches.

**CRAWDAD (King et al.)** — one of the earliest public datasets; RSSI-based; useful for historical comparison.

**SODIndoorLoc** — large-scale multi-building dataset (2022); 3D across 8,000 m² and 3 floors across 3 buildings.

## Technology Coverage

| Technology | Datasets available |
|---|---|
| Wi-Fi RSSI | UJIIndoorLoc, CRAWDAD, KIOS, Tampere, Minho, IPIN2017, Mendoza-Silva, JUIndoorLoc, SODIndoorLoc, TUJI1, Potortì |
| BLE / Bluetooth | Baronti, Bristol, Sikeridis, Pascacio, Sadowski, Tóth & Tamás |
| Magnetic / Geomagnetic | UJIIndoorLoc-Mag, Barsocchi |
| RGB-D / Vision | Matterport3D, InLoc |
| Multi-technology | Sadowski (Zigbee+BT+WiFi), Potortì (WiFi+Magnetic+Camera), Tóth & Tamás (WLAN+BLE) |

## Selecting a Dataset

- **Benchmarking fingerprinting ML**: UJIIndoorLoc (largest, most cited)
- **Multi-floor / multi-building**: SODIndoorLoc, JUIndoorLoc, UJIIndoorLoc
- **BLE-specific**: Baronti, Bristol, Pascacio
- **Vision-based fusion**: Matterport3D, InLoc
- **Sensor fusion (multi-tech)**: Potortì et al., Sadowski et al.
- **Small-scale / controlled**: Barsocchi, IPIN2017

## Entity Summary

### Hardware
- **WiFi RSSI datasets**: Standard 802.11 APs (existing infrastructure); mobile phone as receiver; no special hardware
- **BLE datasets**: iBeacon or generic BLE transmitters; smartphone or dedicated BLE scanner as receiver
- **Magnetic datasets**: Smartphone magnetometer; no dedicated infrastructure
- **RGB-D / Vision datasets**: Depth cameras (e.g., Kinect, Matterport Pro); produces point clouds + colour images
- **Multi-technology datasets**: Combination of the above; e.g., Potortì et al. uses WiFi + magnetic + camera; Sadowski uses Zigbee + BT + WiFi

### Software
- **Collection apps**: Custom Android/iOS survey apps for capturing RSSI fingerprints at known positions; often custom-built per study
- **Annotation tools**: Manual GPS/mapping tools used to label ground-truth positions during data collection
- **Benchmark frameworks**: Python-based evaluation pipelines; no single standard; EVARILOS, IndoorGML used in some studies
- **ML training**: TensorFlow, PyTorch, scikit-learn used to train and evaluate models against these datasets
- **Availability**: All datasets listed are publicly available (URLs in source paper 2403.04333v1)

### Algorithm
- **Fingerprinting ML benchmarks**: KNN, SVM, DNN, CNN evaluated against UJIIndoorLoc, CRAWDAD, JUIndoorLoc
- **Deep learning evaluation**: RNN/LSTM on temporal RSSI sequences; CNN on radio-map images
- **Vision-based**: CNN feature extraction on Matterport3D and InLoc RGB-D; place recognition + retrieval
- **Sensor fusion evaluation**: EKF / particle filter tested on multi-technology datasets (Potortì et al., Sadowski)
- **No single algorithm**: Datasets are used to benchmark a wide range of algorithms; no canonical "standard" algorithm per dataset

### Accuracy
- **UJIIndoorLoc baseline**: Fingerprinting KNN ~8–10m mean error; DNN methods improve to 2–4m in literature
- **CRAWDAD (1D)**: Historical baseline; RSSI-only; modern methods achieve ~2–3m
- **BLE (Baronti, Pascacio)**: Sub-metre achievable with dense beacon deployment and fingerprinting
- **RGB-D (Matterport3D, InLoc)**: Place recognition accuracy reported as top-1 retrieval accuracy; not distance-based
- **Multi-floor challenges**: JUIndoorLoc, UJIIndoorLoc show accuracy degrades significantly for floor estimation errors
- **Note**: Dataset age matters — older datasets (pre-2016) may not reflect modern device RSSI characteristics; newer datasets (SODIndoorLoc 2022, TUJI1 2023) better reflect current hardware

## Additional Datasets (IPIN 2016 + IEEE SENSORS 2021)

### Barsocchi et al. 2016 — WLAN + Geomagnetic Multi-Source Dataset

Barsocchi, P., Crivello, A., La Rosa, D., Palumbo, F. *A Multisource and Multivariate Dataset for Indoor Localization Methods Based on WLAN and Geo-magnetic Field Fingerprinting.* IPIN 2016. ISTI-CNR, Pisa, Italy.

A publicly available dataset (UCI Machine Learning Repository) capturing **WiFi RSS + geomagnetic field + inertial sensors**, collected simultaneously from **both a smartphone and a smartwatch** worn by participants. Key properties:

- **Technologies**: WiFi RSSI + 3-axis magnetometer + accelerometer/gyroscope (IMU)
- **Collection devices**: Smartphone + smartwatch (synchronized data from both simultaneously)
- **Campaigns**: 2 data collection campaigns in the same environment (reproducibility)
- **Motivation**: Most existing datasets used only a single technology; this enables direct comparison of WiFi, magnetic, and hybrid approaches *in the same environment*
- **Availability**: UCI Machine Learning Repository

Significance: one of the first multi-source datasets to include synchronized wearable (smartwatch) and smartphone data, enabling study of sensor fusion and device heterogeneity in the same physical environment.

### Toth & Tamas 2016 — Miskolc IIS Hybrid IPS Dataset

Tóth, Z., Tamás, J. *Miskolc IIS Hybrid IPS: Dataset for Hybrid Indoor Positioning.* Radioelektronika 2016. University of Miskolc, Hungary.

A hybrid dataset from the Institute of Information Science building (3-story, reinforced concrete; GPS and GSM unavailable indoors):

| Property | Value |
|---|---|
| Technologies | WiFi (W-LAN) + Bluetooth + Magnetometer |
| Building | 3-story reinforced concrete (University of Miskolc) |
| Coverage | ~50% of total building area |
| Position types | Both **absolute** coordinates AND **symbolic** room labels |
| Collection system | ILONA indoor positioning system |
| Formats | MySQL database + CSV |
| Imbalance | Minority class: 18 samples; Majority: 208 samples (≈ 1:12 ratio) |

Notable: Miskolc IIS is highlighted in Alhomayani & Mahoor 2021 as a severely imbalanced dataset (1:12 class ratio), making it a benchmark for class-imbalance mitigation techniques.

### The Class Imbalance Problem in Fingerprinting Datasets (Alhomayani & Mahoor, 2021)

Alhomayani, F. & Mahoor, M.H. *Oversampling Highly Imbalanced Indoor Positioning Data using Deep Generative Models.* IEEE SENSORS 2021. University of Denver.

This paper documents a pervasive but underappreciated problem: **fingerprinting datasets are commonly imbalanced** — some reference locations have far more training samples than others, leading to classifiers biased toward majority locations.

**Examples of imbalanced datasets** (from Table I):

| Dataset | Technology | Minority class | Majority class | Ratio |
|---|---|---|---|---|
| BLE (Aranda et al.) — 1st deployment | BLE | 240 samples | 1,680 samples | ≈ 1:7 |
| Miskolc IIS | WiFi+BT+Mag | 18 | 208 | ≈ 1:12 |
| Various (BLE) | BLE | 2 | 34 | 1:17 |
| Magnetic field | Magnetic | 17 | 404 | ≈ 1:24 |
| UJIIndoorLoc | WiFi | 2 | 139 | ≈ 1:70 |
| LoRaWAN | LoRaWAN | 1 | 398 | 1:398 |

**Proposed solution**: Variational Autoencoder (VAE) and Conditional VAE (CVAE) as oversampling tools to generate synthetic fingerprints for minority-class locations.

- **Compared against**: SMOTE and ADASYN (standard oversampling baselines)
- **Test dataset**: BLE fingerprints (Aranda et al., ~1:7 imbalance ratio)
- **Result**: VAE/CVAE outperforms SMOTE and ADASYN in both minority-class precision and overall precision
- **Code**: Publicly available

**Why this matters**: Most IPS research evaluates accuracy uniformly across all locations, masking poor performance in underrepresented areas. Imbalanced datasets are particularly common in large buildings where some areas are harder to access during surveys.

## Evaluation Benchmarks and Competitions

### IPIN Conference Evaluation Survey (Adler et al., IEEE IPIN 2015)

Adler, S., Schmitt, S., Wolter, K., Kyas, M. *A Survey of Experimental Evaluation in Indoor Localization Research: A Look Back on IPIN Conferences 2010, 2011, 2012, 2013, and 2014.* IEEE IPIN 2015, Banff, Alberta, Canada. Freie Universität Berlin / Reykjavík University. Source: `raw/Sci-Hub. A survey of experimental evaluation in indoor localization research : 2015 International Co.pdf`

A systematic review of **183 randomly selected papers** from the first five IPIN conferences (2010–2014), analysing how evaluation methods have evolved across the field. Provides a meta-level view of what "good evaluation" looks like in indoor localization research and identifies persistent methodological gaps.

**Five evaluation aspects categorised** (matching the entity types of this knowledge base):

| Aspect | What it captures |
|---|---|
| **(1) System/technology in use** | The underlying hardware/signal medium (WiFi, BLE, UWB, acoustic, magnetic, IMU, etc.) |
| **(2) Evaluation method** | How the proposed technique is tested (none, simulation only, real-world lab experiment, real-world non-lab) |
| **(3) Ground truth method** | How the true position is established during evaluation (manual annotation, motion capture, tachymeter, video, grid walk) |
| **(4) Applied metrics** | The reported accuracy measure (RMSE, MAE, CDF, mean/median error, percentage within threshold) |
| **(5) Baseline** | Whether the authors compare against a prior system or only report absolute accuracy |

**Key findings**:
- **Evaluation diversity is extreme**: Ranging from papers with no evaluation at all, to extensive simulations, to real-world experiments under non-lab conditions with moving people and changing environments
- **Most papers do not establish a baseline**: A large fraction of contributions report absolute accuracy figures only, making cross-paper comparison impossible
- **Ground truth methods vary dramatically**: Many papers use coarse manual labelling (standing at a pre-marked grid point); fewer use precise motion capture or tachymeter surveying
- **Technology prevalence**: WiFi RSSI dominated the conference papers across all five years; BLE/Bluetooth grew in later years; hybrid multi-technology systems increasing
- **Simulation vs. real-world**: A notable fraction of papers rely on simulation only; this is flagged as a concern since simulation results frequently do not reproduce in real environments

**Significance**: The Adler 2015 survey establishes the diagnostic framework for evaluating indoor localization papers — the same five-aspect taxonomy used to structure this knowledge base's entity summary sections (Hardware, Software, Algorithm, Accuracy/Evaluation, Ground Truth, Metrics, Baseline). It also contextualises the Microsoft Indoor Localization Competition (below) and EvAAL (also below) as responses to the identified comparability problem.

---

### Microsoft Indoor Localization Competition (2014–2017)

Lymberopoulos, D. and Liu, J. *The Microsoft Indoor Localization Competition: Experiences and Lessons Learned.* IEEE Signal Processing Magazine, September 2017. DOI: 10.1109/MSP.2017.2713817. Source: `raw/Sci-Hub. The Microsoft Indoor Localization Competition: Experiences and Lessons Learned : IEEE Signa.pdf`

A four-year competition (2014–2017) comparing diverse indoor localization technologies in realistic, unfamiliar environments. Over 100 teams from academia and industry participated.

**Motivation**: Individual IPS papers typically evaluate in custom, highly controlled environments — making direct comparison almost impossible. The competition provides a shared, standardised testbed where all systems are evaluated under identical conditions, enabling the first objective cross-system comparison.

**What was compared**: Both infrastructure-free approaches (WiFi, FM, GSM, geomagnetic, sound fingerprinting) and infrastructure-based approaches (UWB beacons, infrared, ultrasound, Bluetooth, short-range FM transmitters, light, magnetic modulators). Systems were evaluated on:
- **Localization accuracy** (distance error, percentile CDF)
- **Deployment overhead** (time to set up the system in an unfamiliar building)
- **Robustness** (performance in realistic conditions with people moving, doors opening, etc.)

**Key lessons learned (across 4 years)**:
- The indoor localization problem remains unsolved despite hundreds of proposed solutions
- No single technology consistently dominates across all evaluation conditions
- Infrastructure-based approaches (UWB, ultrasound) achieve best accuracy but highest deployment overhead
- Infrastructure-free approaches (WiFi fingerprinting) scale better but accuracy is scenario-dependent
- The highly ad hoc evaluation process in prior literature makes it difficult to draw conclusions about real-world performance

**Significance for knowledge base**: This competition is the origin of one of the most widely-used benchmark datasets in the field. Competition dataset (WiFi, magnetic, BLE measurements from a real building) is publicly available and widely used for algorithm comparison. Referenced in [[Magnetic-Field-Localization]] as one of the public magnetic field benchmark datasets.

---

### EvAAL Benchmarking Framework (Potortì et al., Sensors 2017)

Potortì, F. et al. *Comparing the Performance of Indoor Localization Systems through the EvAAL Framework.* Sensors 2017, 17, 2327. DOI: 10.3390/s17102327. CNR Pisa / ETRI Korea / CSIC-UPM Spain / Universitat Jaume I / University of Alcalá. Source: `raw/sensors-17-02327-v2.pdf`

The **EvAAL (Evaluating AAL Systems through Competitive Benchmarking)** framework is a standardised methodology for evaluating indoor localization systems under fair, reproducible conditions. The paper demonstrates EvAAL's application to the 2016 IPIN (Indoor Positioning and Indoor Navigation) Competition.

**The diversity problem**: Prior indoor localization evaluation is highly heterogeneous:
- Different testbed environments (sizes, buildings, floor counts)
- Different sensor hardware (smartphone vs. custom IMU vs. UWB anchor)
- Different evaluation metrics (RMSE, average error, CDF, final drift)
- Single-walk vs. multi-track evaluation
- This makes cross-paper comparison essentially impossible

**EvAAL evaluation criteria**:
- **Standardised metric**: Position error CDF (cumulative distribution function) — reports percentile accuracy (e.g., 75th percentile, 90th percentile)
- **Standardised environment**: Competition organisers deploy the evaluation in a specified building; participants have no prior familiarity with it
- **Multiple technology tracks**: Paper tracks smartphone IMU, radio (WiFi/BLE/UWB), multi-floor, and robotic navigation separately with appropriate metrics for each

**IPIN 2016 Competition results** (across 5 teams evaluated by EvAAL):
- Diversity in approaches: inertial sensor fusion, WiFi/BLE fingerprinting, UWB ranging, visual-magnetic-inertial fusion
- Best systems achieve 0.5–2m median accuracy in the competition building
- No single approach dominates across tracks; sensor fusion consistently outperforms single-modality approaches

**Value for knowledge base**:
- EvAAL provides the methodology for fair evaluation; any future dataset compilation should follow EvAAL criteria
- The paper also contains a literature survey of evaluation approaches (Table 1) documenting 2016 IPIN Conference approaches with their metrics — a useful audit of the state of the art at that point
- CNR/Barsocchi are the same authors as the Barsocchi 2016 dataset already in this article; EvAAL is their evaluation framework built around that data

---

### Multi-Technology IoT RSSI Dataset (Sadowski & Spachos, IEEE Access 2018)

Sadowski, S. and Spachos, P. *RSSI-Based Indoor Localization With the Internet of Things.* IEEE Access, Vol. 6, 2018. DOI: 10.1109/ACCESS.2018.2843325. University of Guelph, Canada. Source: `raw/Sci-Hub. RSSI-Based Indoor Localization With the Internet of Things : IEEE Access, 2018.pdf`

A comparative study of four IoT wireless technologies for indoor localization, with a publicly released multi-technology RSSI dataset:

**Technologies compared**: WiFi (IEEE 802.11n, 2.4 GHz), Bluetooth Low Energy (BLE), Zigbee, LoRaWAN (Long Range Wide Area Network). All four use RSSI-based trilateration for position estimation.

**Evaluation dimensions**:
- **Localization accuracy**: Error distance across all four technologies in the same indoor environment
- **Power consumption**: Energy cost per position update — critical for battery-constrained IoT sensor nodes

**Key findings**:
- WiFi achieves highest accuracy among the four but highest power consumption
- BLE offers competitive accuracy (sub-2m in most scenarios) with substantially lower power consumption — best accuracy-efficiency trade-off for IoT applications
- Zigbee: lower accuracy but extremely low power; suitable for coarse-grained room-level tracking of battery-constrained sensors
- LoRaWAN: lowest power but coarsest accuracy; suited for long-range building-scale or campus-scale tracking rather than room-level IPS

**Dataset characteristics**:
- Multi-technology RSSI measurements from same indoor environment
- Enables direct accuracy-vs-power comparison across technologies on identical testbed
- Dataset publicly available: direct download link provided in the paper

**Significance**: Most datasets cover a single radio technology. The Sadowski & Spachos dataset enables direct cross-technology comparison — important for practitioners choosing which IoT radio to deploy for a specific accuracy requirement and battery life target.

---

## Relationships

- [[Indoor-Localization-ML-Methods]] — datasets are used to train and benchmark fingerprinting, KNN, DNN, and Kalman-based methods
- [[Indoor-Location-Sensor-Technologies]] — datasets span Wi-Fi, BLE, UWB, magnetic, visual and acoustic technologies
- [[Indoor-Localization-Applications-by-Sector]] — dataset scales suggest typical deployment environments (hospital corridors, office buildings, multi-floor complexes)
- [[WiFi-Fingerprinting-Advances]] — the 14 public datasets used to evaluate the data cleansing method overlap with datasets catalogued here; matrix completion methods reduce survey burden for creating these datasets
- [[Indoor-Localization-ML-Methods]] — class imbalance problem (Alhomayani & Mahoor) motivates VAE/CVAE oversampling methods catalogued in ML methods article
- [[Magnetic-Field-Localization]] — Microsoft Competition dataset and IPIN Competition datasets include magnetic field data; Barsocchi 2016 is referenced in the magnetic survey as a benchmark
- [[Indoor-Location-Sensor-Technologies]] — Sadowski & Spachos IoT dataset directly informs technology selection (accuracy vs. power trade-offs across WiFi, BLE, Zigbee, LoRaWAN)

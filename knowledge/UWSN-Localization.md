---
tags: [UWSN, underwater-localization, sensor-networks, AUV, DV-Hop, ToA, TDoA, range-free, range-based]
date-compiled: 2026-06-14
source-files: ["raw/1-s2.0-S1110016826000694-main.pdf"]
status: active
---

# UWSN Localization

## Summary

A comprehensive review of localization techniques for Underwater Wireless Sensor Networks (UWSNs), drawn from Khan, Muhammad et al. (*"Advances in localization techniques and algorithms for UWSNs"*, Alexandria Engineering Journal 137, 2026, pp. 468–504). UWSNs are sensor networks deployed in underwater environments for environmental monitoring, marine life research, underwater exploration, and military surveillance. Localization in underwater environments is significantly more challenging than indoor or terrestrial scenarios due to the physical properties of water and acoustic communication constraints.

## Applications of UWSNs

- Environmental monitoring (pressure, temperature, salinity, pollutants)
- Marine life research and aquatic ecosystems
- Underwater exploration and mapping
- Military surveillance and defence

Accurate node localization is essential: correct positioning enables authentic data collection, network communication control, and navigation assistance.

## Why Underwater Localization Is Hard

Underwater localization is more complex than indoor localization (cf. [[Indoor-Location-Sensor-Technologies]]) because:

- **Signal attenuation**: Radio signals attenuate rapidly in water; acoustic signals are the primary communication medium
- **High propagation delay**: Sound travels at ~1500 m/s in water (vs. ~3×10⁸ m/s for radio in air) — introduces significant TOA measurement latency
- **Multipath effects**: Acoustic signals reflect off the surface, bottom, and objects
- **Low bandwidth**: Acoustic channels have limited bandwidth compared to radio
- **Power constraints**: Sensor nodes are typically battery-powered with no easy replacement
- **Dynamic environment**: Currents, temperature gradients, and salinity variations cause the acoustic channel to change over time
- **No GPS**: GPS signals do not penetrate water; underwater nodes cannot self-localise using satellite systems

## Classification Framework

The paper introduces a two-axis classification:

### Axis 1: Architecture
| Type | Description | Trade-offs |
|---|---|---|
| **Centralised** | Positions computed at a central node/base station | High accuracy; requires reliable communication links; high energy consumption |
| **Distributed** | Nodes estimate positions via peer message exchange | More scalable; robust to node failure; lower per-node energy |

Both centralised and distributed are further divided into:
- **Estimation-based**: Uses real-time distance or angle estimates (ToA, TDoA, AoA, RSSI)
- **Prediction-based**: Uses prior information or ML to predict position in dynamic environments

### Axis 2: Algorithm Type
| Type | Description | Trade-offs |
|---|---|---|
| **Range-based** | Uses acoustic/optical signal measurements (ToA, TDoA, AoA, RSSI) to estimate distances | High accuracy; computationally intensive; susceptible to signal conditioning errors in aquatic environments |
| **Range-free** | Uses relative positioning or connectivity (which nodes can hear which) | Lower energy; lower accuracy; suited for applications where precision is less critical |

## Key Algorithms

### Centroid
Range-free. A node estimates its position as the geometric centroid of its anchor neighbours. Simple and low-cost; accuracy limited by anchor density and distribution.

### DV-Hop (Distance Vector Hop)
Range-free. Estimates distance to anchors by counting hop counts and multiplying by an average hop distance. Widely analysed; accuracy degrades with irregular network topology. The 2026 paper provides mathematical and simulation-based analysis of DV-Hop performance.

### APIT (Approximate Point in Triangle)
Range-free. Determines whether an unknown node lies inside or outside triangles formed by anchor triplets; estimates position from the overlap of triangles containing the node. More accurate than Centroid or DV-Hop in certain topologies.

### Range-Based Methods (ToA, TDoA, AoA, RSSI)
- **ToA**: Measures one-way acoustic signal travel time; requires time synchronisation
- **TDoA**: Measures time difference of arrival at pairs of receivers; eliminates need to synchronise transmitter
- **AoA**: Measures angle of arrival using directional sensors or antenna arrays
- **RSSI**: Received signal strength; practical but highly variable in underwater acoustic channels

### AUV-Aided Localization (AAL)
Autonomous Underwater Vehicles (AUVs) act as mobile anchor nodes, surfacing periodically to obtain GPS fixes then diving to relay position information to static sensor nodes. Reduces infrastructure cost (no fixed anchors on the seabed) but introduces mobility-related timing issues.

### Advanced/ML Methods
The review covers a range of advanced approaches:
- **SLAM** (Simultaneous Localisation and Mapping): Builds a map while localising
- **Cooperative localisation**: Nodes share estimated positions to refine each other's estimates
- **Probabilistic / Bayesian methods**: Particle filters, probabilistic graphical models
- **Reinforcement Learning / Multi-agent RL (MARL)**: Adaptive localisation in dynamic environments
- **RNN, DNN**: Deep learning for position prediction from acoustic signal sequences
- **Magneto-Inductive (MI) based**: Alternative to acoustic; uses magnetic fields (different propagation characteristics)

## Key Challenges

1. **Time synchronisation**: ToA/TDoA require tight clock synchronisation across nodes; difficult in large networks
2. **3D localisation**: Underwater networks are inherently 3D (depth matters); most terrestrial algorithms assume 2D
3. **Node mobility**: Currents move sensor nodes; static algorithms become inaccurate
4. **Anchor availability**: Surface buoys or AUVs are needed as reference points; infrastructure cost is high
5. **Energy efficiency**: Acoustic communication is energy-intensive; minimising transmissions is critical
6. **Security**: Underwater networks are vulnerable to jamming and spoofing; advanced encryption (AES, ECC, RSA) required

## Contrast with Indoor Localization

| Dimension | Indoor | Underwater |
|---|---|---|
| Primary signal | Radio (WiFi, BT, UWB) or ultrasound | Acoustic |
| Signal speed | ~3×10⁸ m/s (radio) | ~1500 m/s (sound) |
| GPS availability | Not available indoors | Not available underwater |
| Main challenge | Multipath, NLOS, interference | Delay, attenuation, dynamic channel, 3D mobility |
| Reference anchors | Fixed APs/beacons on walls/ceiling | Surface buoys, AUVs |
| Typical accuracy target | 0.1–1m | 1–10m (varies widely) |

## Entity Summary

### Hardware
- **Medium**: Acoustic (sound) — primary signal medium underwater; radio waves attenuate rapidly in water
- **Acoustic modems**: Underwater transducers for signal transmission and reception; piezoelectric elements; commercially available (e.g., EvoLogics, LinkQuest, WHOI Micro-Modem)
- **Sensor nodes**: Battery-powered underwater pods; contain acoustic modem, CPU, sensors (pressure, temperature, etc.); no GPS receiver (signal doesn't penetrate water)
- **Surface buoys**: GPS-equipped; serve as anchor nodes with known position; relay data between underwater nodes and shore
- **AUVs (Autonomous Underwater Vehicles)**: Mobile platforms that surface to obtain GPS fix, then dive to act as mobile anchors; used in AAL approaches
- **Directional acoustic arrays**: Required for AoA measurement; more complex than omnidirectional transceivers
- **MI (Magneto-Inductive) coils**: Alternative to acoustic; magnetic field propagation; used in MI-UWSN research

### Software
- **Simulation platforms**: MATLAB (most common for UWSN algorithm analysis), NS2/NS3 (network simulation), AquaSim (UWSN-specific NS3 extension)
- **DV-Hop implementations**: Typically custom C/Python in research; no standard open-source production stack
- **ML frameworks**: TensorFlow/PyTorch for DNN/RNN position prediction; scikit-learn for classical ML
- **SLAM**: Custom implementations for AUV mapping; adapted from terrestrial SLAM (e.g., iSAM, GTSAM)
- **Availability**: Most UWSN localization software is research prototype code; no dominant open-source platform equivalent to indoor WiFi fingerprinting tools

### Algorithm
- **Centroid**: Range-free; position = geometric centroid of anchor neighbours; O(n) complexity; low accuracy
- **DV-Hop**: Range-free; hop-count × average-hop-distance to estimate anchor distances; accuracy degrades with irregular topology
- **APIT**: Range-free; triangle overlap test using anchor triplets; better than Centroid/DV-Hop in certain topologies
- **ToA**: Range-based; one-way acoustic travel time → distance; requires tight time synchronisation
- **TDoA**: Range-based; difference in arrival times at receiver pairs → hyperbolic positioning; eliminates transmitter clock sync
- **AoA**: Range-based; angle estimation from directional sensor array; requires array hardware
- **RSSI**: Range-based; signal strength → distance (unreliable in dynamic acoustic channels)
- **AUV-Aided Localisation (AAL)**: Mobile anchor visits static nodes; GPS-fixed surface position propagated to underwater nodes
- **SLAM**: Simultaneous map-building and self-localisation; adapted from robotics/terrestrial
- **Cooperative localisation**: Nodes share estimated positions to mutually refine; graph-optimisation or particle-filter based
- **MARL / RL**: Multi-agent reinforcement learning for adaptive localisation in dynamic currents
- **RNN / DNN**: Deep learning for acoustic signal sequence-based position prediction

### Accuracy
- **Typical target**: 1–10m (contrast with 0.1–1m for indoor radio systems)
- **Range-free methods (Centroid, DV-Hop)**: Lower accuracy; heavily topology-dependent; useful where energy dominates accuracy
- **Range-based ToA/TDoA**: Higher accuracy than range-free; synchronisation errors limit floor
- **AUV-aided**: Moderate accuracy; mobility introduces timing uncertainty
- **DNN/RNN methods**: Improving state-of-art; performance varies by environment and acoustic channel model
- **Limiting factors**: Acoustic propagation delay (~1500 m/s), multipath from surface/bottom, dynamic channel, node mobility from currents, clock drift
- **Comparison**: UWSN accuracy targets are order-of-magnitude looser than indoor systems; acoustic physics is the primary constraint

## Relationships

- [[Indoor-Location-Sensor-Technologies]] — indoor sensor technology survey; UWSN represents a distinct but analogous domain
- [[Indoor-Localization-ML-Methods]] — ML methods developed for indoor localization have analogues in UWSN (RNN, particle filters, cooperative localisation)
- [[ORL-Ultrasonic-Location-System]] — ultrasonic time-of-flight measurement is the indoor analogue of underwater acoustic ToA

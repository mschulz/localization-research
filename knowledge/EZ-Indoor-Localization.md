---
tags: [indoor-localization, WiFi, zero-calibration, genetic-algorithm, propagation-model, crowdsourcing, RSS, SAR, synthetic-aperture-radar, infrastructure-free]
date-compiled: 2026-06-15
source-files:
  - "raw/ez-mobicom.pdf"
  - "raw/Sci-Hub. Accurate indoor localization with zero start-up cost : Proceedings of the 20th annual inter.pdf"
status: active
---

# EZ — Indoor Localization Without the Pain

## Summary

Chintalapudi, K., Iyer, A.P., Padmanabhan, V.N. *Indoor Localization Without the Pain.* ACM MobiCom 2010. Microsoft Research India.

EZ is a WiFi indoor localization system that requires **zero explicit pre-deployment effort** — no war-driving, no radio map surveys, no knowledge of AP locations or transmit powers. Instead, EZ exploits the physics of radio propagation: all RSS observations made by users traversing an indoor space are constrained by a log-distance path loss model, even when observed at unknown locations. A **genetic algorithm** simultaneously infers the RF environment (AP positions, transmit powers, path loss exponents) and user positions. Occasional GPS fixes at building entrances or windows provide the only ground-truth anchors.

EZ achieves median localization errors of **2m (small building) and 7m (large building)** with no calibration — compared to 0.7m and 4m from the calibration-intensive [[Horus-WLAN-System]], and 5m and 7m from [[RADAR-WiFi-Fingerprinting]]. It was a direct predecessor to [[UnLoc-Unsupervised-Localization]] and [[Zee-Zero-Effort-Crowdsourcing]].

## The Core Problem

Traditional WiFi fingerprinting ([[RADAR-WiFi-Fingerprinting]], [[Horus-WLAN-System]]) requires:
- A surveyor to physically visit every reference point and record RSSI
- Knowledge of AP locations and transmit powers
- Repeated re-surveys when the environment changes

This is expensive, does not scale to malls/large buildings where APs are deployed by multiple unknown parties, and is impossible in spaces where no one has prior access.

**EZ's key insight**: Even RSS measurements taken at *unknown locations* are not arbitrary — they are constrained by the physics of how radio signals propagate. The relationship between RSS, distance, transmit power, and path loss exponent is:

```
RSSᵢⱼ = Pᵢ - 10·γᵢ·log(dᵢⱼ) + Gⱼ
```
where:
- `Pᵢ` = transmit power of AP i
- `γᵢ` = path loss exponent of AP i (typically 2–4 indoors)
- `dᵢⱼ` = distance from AP i to device j at location j
- `Gⱼ` = receiver gain of device j (varies by device model)

If we have enough measurements, we can solve for all unknowns simultaneously.

## Three Assumptions

EZ requires only:
1. **WiFi coverage**: APs throughout the indoor space
2. **Mobile devices**: Users carry WiFi-enabled smartphones or laptops
3. **Occasional GPS fixes**: At least some users obtain a GPS lock at building entrances, windows, or other outdoor access points — these provide absolute coordinate anchors

Users do not need to indicate their locations, annotate data, or do anything explicitly to help EZ. Normal usage generates the data.

## The EZ Localization Algorithm

EZ casts localization as an optimisation problem over the joint unknowns of:
- AP positions (xᵢ, yᵢ) for each AP i
- AP transmit powers Pᵢ
- Path loss exponents γᵢ
- User positions at each observation time
- Receiver gain differences Gⱼ between devices

The objective function minimises the mean absolute error between observed RSS and the log-distance model prediction:

```
J_EZ = (1/N) Σᵢⱼ |RSSᵢⱼ - Pᵢ + 10·γᵢ·log(dᵢⱼ)|
```

This is a **non-linear, non-convex** optimisation problem with no known closed-form solution. EZ solves it using a **genetic algorithm (GA)**:
- A population of candidate solutions (AP positions, powers, exponents) evolves over generations
- Solutions are selected, crossed-over, and mutated in proportion to their fitness (low J_EZ)
- The GA searches the solution space without requiring derivatives or convexity
- Convergence is achieved after sufficient RSS observations have been accumulated

## Handling Device Heterogeneity: RGEA

Different smartphones have different receiver gains — the same RSS at the same location differs by device model. EZ addresses this with the **Relative Gain Estimation Algorithm (RGEA)**:

- Pairs of devices whose gain-independent RSS difference vectors are similar (within 3 dB) are deemed to have measured from proximate locations
- From such pairs, the receiver gain difference ΔGᵢⱼ between each pair of devices can be estimated
- RGEA provides a coarse-grained estimate (within 1–3 dB) that helps the GA significantly narrow the search space for receiver gains
- Transitive estimation: if ΔG₁₂ and ΔG₂₃ are known, ΔG₁₃ = ΔG₁₂ + ΔG₂₃

## Implementation

### Client (EZ Client)
- Software on mobile devices (Windows Mobile 6; tested on HTC Advantage, Samsung SGH i780, HP iPAQ)
- Two functions: (a) RSS scanning and reporting to server; (b) location query answering
- RSS scan: collects beacons for ~3 seconds; reports mean ± std dev per AP
- Scans with RSS std dev > 10 dB are discarded as unreliable (multipath noise)
- ~2,000 lines of C# and C++

### Server (EZ Server)
- Runs the genetic algorithm; maintains the RF environment model
- Answers location queries from clients using the learned model
- For new devices: solves a system of gain-independent equations via least-mean-squares search

## Evaluation Results

Tested in two buildings at Microsoft Research India:

| Building | Size | EZ median error | RADAR best | Horus best |
|---|---|---|---|---|
| Small | 1 floor, ~20 APs | **2m** | 5m | 0.7m |
| Large | Multi-floor, 48 APs | **7m** | 7m (with AP selection) | 4m |

**Key findings:**
- In the small building, EZ outperforms RADAR significantly despite requiring zero calibration
- In the large building, EZ matches RADAR — Horus (fully calibrated) is 1.75× better
- Performance improves as more RSS data accumulates: median error drops from ~3m to <2m and 80th percentile from ~4m to <4m as more training walks are added
- New devices (never seen before) can be localized using the gain-independent formulation; performance is slightly worse but still competitive
- AP selection (using a subset of highest-discriminating APs) improves large-building accuracy

## Comparison with Related Systems

| System | Calibration | Knowledge required | Small bldg accuracy | Large bldg accuracy |
|---|---|---|---|---|
| **EZ (MobiCom 2010)** | None | WiFi infrastructure + occasional GPS | **2m median** | **7m median** |
| RADAR (INFOCOM 2000) | War-driving | Radio map | 5m | 7m |
| Horus (MobiSys 2005) | War-driving | Radio map | 0.7m | 4m |
| UnLoc (MobiSys 2012) | None | One GPS fix at entrance | ~1.69m | — |
| Zee (MobiCom 2012) | None | Floor plan | ~3m | — |
| Ubicarse (MobiCom 2014) | None | Twist device during each query | **39cm (3D)** | — |

EZ represents a trade-off: calibration-free but higher error than calibrated methods. Its accuracy is comparable to RADAR while requiring no human survey effort.

## Related Zero-Infrastructure System: Ubicarse (Kumar et al., ACM MobiCom 2014)

Kumar, S., Gil, S., Katabi, D., Rus, D. *Accurate Indoor Localization With Zero Start-up Cost.* ACM MobiCom 2014. Massachusetts Institute of Technology. Source: `raw/Sci-Hub. Accurate indoor localization with zero start-up cost : Proceedings of the 20th annual inter.pdf`; full paper verified at cse.buffalo.edu.

Ubicarse and EZ share the same goal — accurate indoor localization with no pre-deployment fingerprinting or specialised infrastructure — but take fundamentally different approaches. Where EZ infers AP positions from accumulated RSS using a genetic algorithm, Ubicarse enables a **single device to perform its own localisation** using Synthetic Aperture Radar (SAR) applied to WiFi signals.

**Core innovation — SAR on handhelds**: Synthetic Aperture Radar (SAR) traditionally requires mechanically controlled, precisely measured movement along a known path (millimetre-level precision). Ubicarse's primary contribution is reformulating SAR so it works when the device is **twisted by a user along an unknown path** — the irregular, imprecise movement typical of someone rotating a tablet while indoors.

**Two-component system**:
1. **RF localisation (SAR)**: The user twists a handheld WiFi device; the motion sweeps the device antenna through an effective aperture, allowing direction-of-arrival of each WiFi AP to be estimated with high angular precision. Multiple AP directions triangulate the device's position.
2. **Object geotagging (stereo vision)**: Ubicarse combines RF localisation with stereo-vision algorithms to localise common objects that have no RF source attached to them. The device's camera and the RF position are fused to estimate absolute object positions.

**Hardware**: HP SplitX2 tablet (commodity device — no modified hardware). Standard WiFi NICs; no UWB, no ultrasound, no dedicated beacons.

**Performance**:

| Metric | Value |
|---|---|
| 3D device localization (median) | **39 cm** |
| Object geotagging (median) | **17 cm** |
| Infrastructure required | None (uses existing WiFi APs) |
| Fingerprinting required | None |
| Hardware modifications | None (commodity tablet) |

**Comparison with EZ**:

| System | Accuracy (small bldg) | Calibration | How position is obtained |
|---|---|---|---|
| **EZ (MobiCom 2010)** | 2m median | None (accumulates RSS over time) | Genetic algorithm on RSS propagation model |
| **Ubicarse (MobiCom 2014)** | 39cm median (3D) | None | SAR direction-finding + triangulation |
| RADAR (INFOCOM 2000) | ~2.94m | War-driving | Nearest-neighbour fingerprint matching |
| Horus (MobiSys 2005) | ~42cm | War-driving | Probabilistic fingerprint matching |

Ubicarse achieves accuracy comparable to the best calibrated systems (Horus: 42cm) with zero calibration — a dramatic improvement over EZ's 2m. The cost is the requirement for the user to actively twist the device during each localisation request, unlike EZ and RADAR which work passively.

**Significance**: Ubicarse establishes that SAR-class accuracy is achievable on commodity handheld devices. It eliminates the training-data bottleneck entirely — no war-driving, no infrastructure installation — while substantially closing the accuracy gap that remained with EZ.

---

## Limitations

- **Large-building accuracy**: 7m median is too coarse for many applications (room-level only)
- **GA convergence time**: Sufficient RSS observations needed before the model stabilises; newly deployed in a new space, EZ takes time to converge
- **Log-distance model**: Assumes isotropic path loss — fails in environments with strong directional obstacles or unusual RF reflectors
- **GPS dependency**: Requires occasional outdoor GPS fixes; problematic in urban canyons or deep basement floors
- **Device heterogeneity**: RGEA estimates receiver gain differences but cannot perfectly compensate for very diverse device populations
- **No floor plan**: Cannot constrain locations to walkable areas; may place users inside walls
- **Multi-floor**: Paper focused on 2D; floor identification not addressed

## Entity Summary

### Hardware
- **Medium**: WiFi RF (802.11); passive RSS scanning; no AP modifications required
- **Devices**: Windows Mobile 6 smartphones (HTC Advantage, Samsung SGH i780, HP iPAQ)
- **Infrastructure**: Existing WiFi APs; occasional GPS lock at building edges only

### Software
- **Client**: Windows Mobile 6; C# + C++; ~2,000 lines; not released
- **Server**: C++ or similar; genetic algorithm implementation; not released
- **Availability**: Research prototype; no public code release

### Algorithm
- **Log-distance path loss model**: RSSᵢⱼ = Pᵢ − 10·γᵢ·log(dᵢⱼ) + Gⱼ
- **Genetic algorithm**: Evolving population of (AP positions, powers, exponents, device gains) to minimise J_EZ
- **RGEA**: Relative receiver gain estimation from co-located device pairs
- **Online localization**: Gain-independent least-mean-squares position search for new devices

### Accuracy
- **Small building**: 2m median — calibration-free; comparable to and better than RADAR (5m)
- **Large building**: 7m median — worse than calibrated Horus (4m) but matches RADAR
- **Comparison basis**: Median and 80th percentile localization error

## References

| # | Reference | In raw/ |
|---|---|---|
| [4] | ✅ Bahl, P., Padmanabhan, V.N. *RADAR.* INFOCOM 2000. → [[RADAR-WiFi-Fingerprinting]] | ⭐ High |
| [29] | ✅ Youssef, M. *Horus.* MobiSys 2005. → [[Horus-WLAN-System]] | ⭐ High |
| [9] | Ferris, B. et al. *WiFi SLAM Using Gaussian Process Latent Variable Model.* IJCAI 2007. | High — WiFi SLAM baseline |
| [17] | Lim et al. *Zero Configuration Robust Indoor Localization.* Infocom 2006. | High — earlier zero-config work |
| [6] | Banzhaf et al. *Genetic Programming: An Introduction.* Morgan Kaufmann 1998. | Low — GA reference |

## Relationships

- [[RADAR-WiFi-Fingerprinting]] — EZ is designed to achieve comparable accuracy without RADAR's war-driving requirement
- [[Horus-WLAN-System]] — calibrated baseline that EZ is benchmarked against; Horus is 3–5× more accurate but requires full radio map
- [[UnLoc-Unsupervised-Localization]] — cites EZ [10] as a predecessor zero-effort approach; UnLoc improves on EZ via landmark detection
- [[Zee-Zero-Effort-Crowdsourcing]] — cites EZ [9]; Zee uses EZ as one of two localization backends fed by its crowdsourced training data
- [[WiFi-Fingerprinting-Advances]] — EZ represents a deployment-efficiency approach within the fingerprinting taxonomy
- [[Indoor-Location-Sensor-Technologies]] — WiFi RSS propagation model and log-distance path loss covered there
- [[Indoor-Localization-ML-Methods]] — genetic algorithms as optimisers are an adjacent technique to ML methods for IPS; Seco 2009 taxonomy classifies EZ as a cost-function minimisation approach

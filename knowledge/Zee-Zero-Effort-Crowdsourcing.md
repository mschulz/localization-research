---
tags: [indoor-localization, WiFi, crowdsourcing, zero-calibration, particle-filter, IMU, dead-reckoning, floor-plan]
date-compiled: 2026-06-14
source-files: ["raw/zee.pdf"]
status: active
---

# Zee — Zero-Effort Crowdsourcing for Indoor Localization

## Summary

Rai, A., Chintalapudi, K.K., Padmanabhan, V.N., Sen, R. *Zee: Zero-Effort Crowdsourcing for Indoor Localization.* ACM MobiCom 2012. Microsoft Research India + IIT Bombay.

Zee solves the calibration bottleneck in WiFi fingerprinting by crowdsourcing a WiFi training set with **zero explicit effort from users**. Users simply walk normally through an indoor space carrying smartphones. Zee uses IMU sensors (accelerometer, compass, gyroscope) to track their trajectories, a floor plan map to constrain impossible paths, and an **augmented particle filter** to simultaneously estimate location and other uncertain variables (stride length, heading offset). As the location estimate converges, each WiFi scan is annotated with the inferred location — automatically building a labelled radio map. This crowdsourced training set, when fed into Horus or EZ, achieves **median localization error ~3m** — comparable to the 3.5m achieved with manually-gathered training data.

**Key distinction from related systems**:
- vs. [[UnLoc-Unsupervised-Localization]]: Zee crowdsources a *radio map* (enabling standard fingerprinting); UnLoc performs *localization directly* via landmark detection. Zee requires a floor plan; UnLoc does not.
- vs. [[Walkie-Markie-Indoor-Mapping]]: Walkie-Markie builds a *pathway map* from RSS trend reversals; Zee builds a *labeled fingerprint database* using augmented particle filtering. Both require no explicit user effort.
- vs. [[EZ-Indoor-Localization]]: Zee uses EZ (and Horus) as localization backends for the crowdsourced data it generates; EZ is a system Zee improves upon.

## The Core Insight

WiFi fingerprinting accuracy depends on the quality of the training set. Creating a good training set manually requires a surveyor to visit every reference point — labour-intensive and not scalable. Zee's insight: users already walk through the space in normal course. If their trajectories can be inferred automatically and correlated with WiFi measurements, the training set builds itself.

The challenge: at any given moment, a user's location is **unknown**. Zee must infer it from:
- IMU readings (step count, direction changes) — gives relative displacement but accumulates error
- The floor plan (constrains which paths are physically possible)
- WiFi scans (provide soft location hints even before the radio map is built)

## Augmented Particle Filter

Standard particle filters represent location uncertainty as a set of weighted particles in 2D space. Zee extends this with **multi-dimensional particles** that also represent uncertainty in:
- **Stride length** (varies by user, phone placement, walking pace)
- **Heading offset (HO)** — the unknown offset between the phone's orientation and the user's direction of walk (depends on whether phone is in hand, pocket, bag)

This augmented representation allows Zee to estimate stride length and heading offset *concurrently* with location — without any user-specific prior knowledge.

### Particle Filter Steps

1. **Propagation**: Each particle propagates according to the step count and direction estimated from IMU sensors, perturbed by the particle's current stride/HO estimate
2. **Correction**: Particles that would require the user to walk through walls or barriers (per the floor plan) are eliminated; surviving particles are reweighted by their consistency with WiFi observations
3. **Re-sampling**: Particles are re-sampled in proportion to their weights, concentrating probability mass on plausible locations
4. **Backward belief propagation**: Once location estimate at time T is confident, propagate that confidence backward along the trajectory to reduce uncertainty at earlier times T-k

### Speed-Up Techniques

Two techniques accelerate particle filter convergence:

**Placement-independent motion estimation**: Standard step counters and compass readings are sensitive to phone placement orientation. Zee uses a placement-independent algorithm that estimates step count and a coarse heading offset from the raw accelerometer and compass without assuming a fixed phone orientation.

**WiFi-based initialization**: Before the full radio map exists, partial WiFi information (visibility of specific APs) is used to eliminate regions of the building where the user cannot be, giving a better prior for particle initialization.

## Backward Belief Propagation

A key innovation: the particle filter runs forward in time, but location uncertainty typically decreases with time (more steps and turns reveal trajectory). After completing a walk:

- The final location estimate (after many turns and constraints) is often more confident than intermediate estimates
- Zee propagates this end-of-walk confidence **backward** along the stored trajectory
- Result: location estimates along the entire path are revised downward in error
- Reported: location error along the entire path drops to <2m after backward propagation for a single user walk

## Training Set Construction

Concurrently with tracking, Zee performs WiFi scans indexed by time. As location estimates become available (and confident), the corresponding WiFi scans are annotated with estimated locations. This produces a labelled RSS training set (location → [RSS₁, RSS₂, ..., RSSₙ]) that can be fed into any standard fingerprinting localization system.

## Evaluation Results

Tested in a large office building (Microsoft Research India campus). Multiple users contributed walking traces; the crowdsourced training data was evaluated by feeding it into Horus and EZ.

### Localization accuracy using crowdsourced training data

| Condition | Median error | 80th percentile error |
|---|---|---|
| EZ with manually-gathered training | ~2–3m | ~4m |
| Horus with manually-gathered training | ~3.5m | — |
| **ZeeEZ (EZ with Zee-crowdsourced data, full building)** | ~3m | Significantly higher* |
| **ZeeHorus (Horus with Zee-crowdsourced data, full building)** | ~3m | Significantly higher* |
| **ZeeEZ (path-only test locations)** | **1.2m** | **2.3m** |
| **ZeeHorus (path-only test locations)** | **1.2m** | **2.3m** |

*The higher 80th percentile for full-building tests occurs because Zee's crowdsourced data only covers walked paths; test locations far from any walked path are underrepresented in the training set, leading to poor performance at those locations.

### Single-walk trajectory example

- At the end of a single user's walk, location error was **under 1 metre** (correct to the last step)
- After backward belief propagation: location error along the entire path **under 2m**

### Key result

Zee's crowdsourced training set achieves accuracy *comparable to manually-gathered data* (1.2m vs. ~1–2m for manual) when test locations are restricted to the walked paths — which represent the space's actual usage patterns.

## Limitations

- **Requires floor plan**: Unlike UnLoc and Walkie-Markie, Zee requires a map showing pathways and barriers. Floor plans are often unavailable or out of date.
- **Coverage bias**: Training data only covers walked paths; rarely-visited areas are underrepresented, leading to poor accuracy there.
- **Initial convergence**: Particle filter needs sufficient walking (steps and turns) to converge; short or straight-line walks may not provide enough constraints.
- **Phone placement ambiguity**: Despite placement-independent estimation, very unusual phone orientations may cause errors in heading offset estimation.
- **No open-source release**: Research prototype; not publicly available.
- **2D only**: Floor identification not addressed for multi-floor buildings.

## Entity Summary

### Hardware
- **Medium**: WiFi RF (passive RSS scanning) + IMU (accelerometer, compass, gyroscope) — all standard smartphone hardware
- **Device**: Android smartphones (implied); no special hardware required
- **Infrastructure**: Existing WiFi APs used passively; floor plan map required

### Software
- **Client**: Smartphone app for RSS scanning + IMU data collection; not released
- **Server**: Augmented particle filter; backward belief propagation; not released
- **Localization backends**: [[Horus-WLAN-System]] and [[EZ-Indoor-Localization]] used to evaluate crowdsourced training set quality

### Algorithm
- **Placement-independent motion estimation**: Estimates step count and coarse heading offset without assuming fixed phone orientation
- **Augmented particle filter**: Multi-dimensional particles representing (x, y, stride length, heading offset); propagation uses step/direction estimates; correction uses floor plan wall constraints + WiFi soft observations
- **Backward belief propagation**: Post-hoc revision of earlier location estimates using later, more confident estimates
- **WiFi-based initialization**: AP visibility used to eliminate impossible starting regions; better particle prior
- **Training set construction**: Time-indexed WiFi scans annotated with particle-filter location estimates as they converge

### Accuracy
- **Crowdsourced vs. manual training set**: 1.2m / 2.3m (50th/80th percentile) on walked paths vs. ~1–2m with manual training — comparable
- **Full building (50th percentile)**: ~3m median — somewhat worse due to coverage gaps in rarely-walked areas
- **Single-walk trajectory**: <1m at walk endpoint; <2m along full path after backward propagation

## References

| # | Reference | In raw/ |
|---|---|---|
| [3] | ✅ Bahl, P., Padmanabhan, V.N. *RADAR.* INFOCOM 2000. → [[RADAR-WiFi-Fingerprinting]] | ⭐ High |
| [32] | ✅ Youssef, M. *Horus.* MobiSys 2005. → [[Horus-WLAN-System]] | ⭐ High |
| [9] | ✅ Chintalapudi et al. *Indoor Localization Without the Pain (EZ).* MobiCom 2010. → [[EZ-Indoor-Localization]] | ⭐ High |
| [6] | Beauregard & Haas. *Pedestrian Dead Reckoning: A Basis for Personal Positioning.* WPNC 2006. | High — PDR reference |
| [10] | Fox et al. *Monte Carlo Localization for Mobile Robots.* AAAI 1999. | High — particle filter foundation |
| [21] | Robertson, Angermann & Krach. *SLAM for Pedestrians using Foot-Mounted Inertial Sensors.* Ubicomp 2009. | High — foot-mounted IMU PDR |
| [22] | Sen et al. *SpinLoc: Spin once to know your location.* HotMobile 2012. | Medium — heading estimation |

## Relationships

- [[EZ-Indoor-Localization]] — Zee uses EZ as one of two localization backends evaluated; EZ is also a conceptual predecessor (zero-calibration WiFi)
- [[Horus-WLAN-System]] — Zee uses Horus as the primary evaluation backend; crowdsourced training data fed into Horus achieves 1.2m / 2.3m accuracy
- [[RADAR-WiFi-Fingerprinting]] — foundational fingerprinting system; Zee's crowdsourcing addresses RADAR's war-driving bottleneck
- [[UnLoc-Unsupervised-Localization]] — complementary: both solve zero-calibration localization; UnLoc uses landmark detection + dead-reckoning; Zee uses augmented particle filter + floor plan; UnLoc doesn't require floor plan
- [[Walkie-Markie-Indoor-Mapping]] — Walkie-Markie cites Zee [26]; both use crowdsourcing for zero-effort map/fingerprint building; Walkie-Markie builds a pathway map, Zee builds a fingerprint database
- [[Indoor-Localization-ML-Methods]] — trajectory learning, particle filter + GP approaches surveyed there are related to Zee's augmented particle filter
- [[Pedestrian-Dead-Reckoning]] — Zee relies on PDR for inter-scan displacement estimation; Woodman's ZUPT-based PDR is the foundational theory
- [[WiFi-Fingerprinting-Advances]] — Zee's crowdsourced radio map construction approach is reviewed in the deployment challenges section of He & Chan and Khalajmehrabadi surveys

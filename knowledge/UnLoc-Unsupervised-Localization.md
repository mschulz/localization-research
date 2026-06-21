---
tags: [indoor-localization, unsupervised, dead-reckoning, landmarks, WiFi, magnetic, crowdsourcing, SLAM]
date-compiled: 2026-06-14
source-files: ["raw/unloc.pdf"]
status: active
---

# UnLoc — Unsupervised Indoor Localization

## Summary

Wang, Sen, Elgohary, Farid, Youssef, Choudhury. *No Need to War-Drive: Unsupervised Indoor Localization.* ACM MobiSys 2012. Duke University / EJUST, Egypt.

UnLoc achieves indoor localization with **zero calibration** (no war-driving, no floor plan required). The core insight is that indoor environments contain ambient sensor signatures that can serve as *landmarks* — detected automatically and without labels. Dead-reckoning between landmarks provides continuous position tracking; error is periodically reset at each landmark encounter. The system improves recursively as more users contribute data, converging to <2m accuracy in under 2 man-hours.

## Key Insight: The War-Driving Bottleneck

Traditional WiFi fingerprinting ([[RADAR-WiFi-Fingerprinting]]; [[Horus-WLAN-System]]) requires meticulous offline calibration (war-driving): a surveyor must physically visit every reference point and record RSSI measurements. This is expensive, not a one-time cost (environments change), and does not scale. UnLoc eliminates this by:

1. Using **dead-reckoning** to track motion without a signal map
2. Detecting **environmental signatures** as self-calibrating landmarks — no human labelling needed
3. Requiring only a **single known anchor** (e.g., the GPS coordinate of a building entrance)

## Landmark Types

### Seed Landmarks (SLMs)
Physical structures that force predictable user behaviour with detectable sensor signatures:
- **Elevators**: Distinct accelerometer pattern (start/stop bumps; direction of bump indicates up/downstairs)
- **Stairs, escalators**: Specific inertial patterns
- **Entrances/doors**: GPS signal drops as user moves indoors
- Confusion matrix: 0.2% false positive, 1.1% false negative overall; elevators: 0% FP/FN

### Organic Landmarks (OLMs) — learned unsupervised
Ambient signatures discovered by clustering sensor data, then verified spatially:
- **WiFi landmarks**: Locations with a uniquely small WiFi AP set (similarity threshold 0.4); must overhear a set of APs with <40% similarity to surrounding areas; confined to <4m² area
- **Magnetic landmarks**: Distinct magnetic field anomalies near metals/electrical equipment (e.g., near networking lab or electrical room); stable over time
- **Inertial/gyroscope landmarks**: Distinctive turns captured as "bending coefficient" — path curvature over sliding window; corridor turns in unique WiFi sub-spaces
- OLMs are detected by K-means clustering on sensor features → dead-reckon cluster members → if all members land within 4m², declare OLM

OLM counts in practice: Engineering building (32 OLMs: 9 magnetic + 8 turns + 15 WiFi); CS building (29 OLMs: 9 magnetic + 10 turns + 10 WiFi).

## Dead-Reckoning Engine

Three-sensor fusion:
- **Accelerometer**: Step counting and displacement estimation
- **Gyroscope**: Angular velocity → turn angle (gyroscope replaces compass for direction — indoor magnetic field is heavily distorted by metals and electrical equipment)
- **Compass**: Absolute orientation for initial bearing; combined opportunistically with gyroscope to prevent drift

Error behaviour: pure dead-reckoning accumulates fast (useless within minutes). With gyroscope + periodic landmark resets, mean error drops below 1.2m. The classic "sawtooth" pattern — error grows between landmarks, resets sharply at each landmark encounter.

## WiFi Partitioning

WiFi AP sets divide indoor space into sub-spaces. A landmark does **not** need to be globally unique — only unique within its WiFi sub-space. This dramatically increases the number of usable landmarks and makes the system robust to overlapping signatures elsewhere in the building.

## Simultaneous Localization and Mapping (SLAM analogy)

UnLoc is inspired by SLAM but uses commodity smartphone sensors instead of LIDAR/odometers:
- SLMs and OLMs are used to reset dead-reckoning error
- Improved dead-reckoning refines OLM location estimates (via weighted centroid from multiple independent path estimates)
- This circular improvement produces recursive accuracy gains over time
- Weighted centroid: paths from nearby landmarks weighted more (less accumulated dead-reckoning error)

Unlike SLAM, UnLoc requires no special sensors and runs on unmodified Android phones.

## Implementation

- **Platform**: Google NexusS Android phones (JAVA client)
- **Sampling**: Magnetic, compass, accelerometer at 24 Hz; gyroscope at highest permissible rate; WiFi at 1 Hz
- **Server**: C# + MATLAB; dead-reckoning, K-means clustering, landmark signature matching
- **No special hardware**: All commodity; existing WiFi infrastructure; no infrastructure modifications

## Evaluation Results

Tested in 3 environments with 3 users, ~1.5 hours per user per building:

| Environment | Area | Median error (online) |
|---|---|---|
| CS building (Duke) | ~1,750 m² | <1.69m |
| Engineering building (Duke) | ~3,000 m² | <1.69m |
| North Gate shopping mall | ~4,000 m² | <1.69m |

- **Online (real-time)**: Median 1.69m across all 3 buildings combined
- **Offline**: Mean ~1.15m (SLMs only); ~0.89m (SLMs + OLMs, offline path correction)
- **Bootstrapping**: Converges in <2 man-hours
- **OLM false positive rate**: <1%
- **With 10/28 OLMs**: Average instantaneous error still within 1.9m

## Comparison with Contemporary Systems

| System | Accuracy | Calibration required | Special infrastructure |
|---|---|---|---|
| UnLoc (MobiSys 2012) | 1–2m | None (one door location) | None |
| RADAR (INFOCOM 2000) | ~2.94m | War-driving | None |
| Horus (MobiSys 2005) | ~0.42m | War-driving | None |
| EZ (MobiCom 2010) | 2–7m | GPS locks indoors | None |
| SLAM (robotics) | ~5m | None | LIDAR, odometer |

## Limitations

- **Heterogeneous hardware**: Landmark signatures vary across phone models (NexusS only tested); would require per-model signature libraries
- **Phone orientation**: Arbitrary phone orientation (hand vs. pocket) affects sensor readings; not fully addressed
- **Scalability**: Only tested with 3 volunteers; crowdsourcing incentives unexplored
- **Temporary landmarks**: Activity-based landmarks (e.g., café queue at lunchtime) not implemented
- **Dynamic environments**: Magnetic and WiFi signatures assumed stable; not robust to furniture changes or WiFi infrastructure modifications

## Entity Summary

### Hardware
- **Medium**: WiFi RF (sub-space partitioning) + magnetic field + inertial (accelerometer, gyroscope, compass) — all using existing smartphone hardware
- **Device**: Google NexusS Android smartphone; commodity off-the-shelf
- **No additional infrastructure**: Existing WiFi APs used passively; no beacons, no tags

### Software
- **Client**: Android (JAVA); open-source status: not released
- **Server**: C# + MATLAB; not released
- **Algorithms implemented**: K-means clustering, gyroscope dead-reckoning, WiFi similarity scoring, weighted centroid landmark refinement

### Algorithm
- **Dead-reckoning**: Accelerometer step count + gyroscope angular displacement + opportunistic compass correction
- **Landmark detection**: K-means on multi-dimensional sensor features (WiFi RSSI sets, magnetic readings, bending coefficient)
- **Spatial confinement check**: Dead-reckon all cluster members → verify they fall within 4m² → declare OLM
- **WiFi sub-space partitioning**: AP set similarity metric S = (1/|A|) Σ min(f₁(a), f₂(a)) / max(f₁(a), f₂(a)); threshold 0.4
- **SLAM-style refinement**: Weighted centroid of independent OLM location estimates (weight ∝ inverse distance from last SLM)

### Accuracy
- **Online median**: 1.69m across 3 diverse environments (office buildings + shopping mall)
- **Offline mean**: 0.89–1.15m
- **Bootstrapping cost**: <2 man-hours to convergence
- **Comparison**: Worse than Horus (0.42m) but requires zero calibration vs. full war-driving; comparable to RADAR (2.94m) with no calibration overhead
- **Landmark detection FP**: <1%; SLM overall: 0.2% FP, 1.1% FN

## References

| # | Reference | In raw/ |
|---|---|---|
| [6] | Bahl, P., Padmanabhan, V.N. *RADAR.* INFOCOM 2000. | ✅ [[RADAR-WiFi-Fingerprinting]] |
| [7] | Youssef, M., Agrawala, A. *Horus WLAN System.* MobiSys 2005. | ✅ [[Horus-WLAN-System]] |
| [10] | ✅ Chintalapudi et al. *Indoor localization without the pain (EZ).* MobiCom 2010. → [[EZ-Indoor-Localization]] | ⭐ High |
| [2] | Constandache et al. *Towards mobile phone localization without war-driving (CompAcc).* INFOCOM 2010. | — |
| [17] | Priyantha, N.B. *The Cricket indoor location system.* MIT PhD Thesis, 2005. | — |
| [23] | Montemerlo et al. *FastSLAM.* AAAI 2002. | — |
| [22] | Woodman & Harle. *Pedestrian localisation for indoor environments.* UbiComp 2008. | — |
| [24] | Tarzia et al. *Indoor localization using acoustic background spectrum.* MobiSys 2011. | — |

## Relationships

- [[RADAR-WiFi-Fingerprinting]] — foundational system UnLoc is designed to replace (eliminates war-driving requirement)
- [[Horus-WLAN-System]] — cited as related probabilistic fingerprinting; UnLoc trades accuracy for zero calibration
- [[WiFi-Fingerprinting-Advances]] — UnLoc's landmark + dead-reckoning approach is reviewed as a spatial-pattern technique in He & Chan COMST 2016
- [[Walkie-Markie-Indoor-Mapping]] — complementary system (same era): Walkie-Markie builds *maps*, UnLoc performs *localization*; both use WiFi landmarks + dead-reckoning + crowdsourcing
- [[Indoor-Localization-ML-Methods]] — dead-reckoning and unsupervised clustering are core techniques covered in the ML methods survey
- [[Indoor-Location-Sensor-Technologies]] — WiFi, magnetic, and IMU sensor modalities used by UnLoc

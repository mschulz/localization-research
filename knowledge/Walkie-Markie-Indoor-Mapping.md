---
tags: [indoor-localization, mapping, WiFi, landmarks, dead-reckoning, crowdsourcing, pathway-map, IMU]
date-compiled: 2026-06-14
source-files: ["raw/nsdi13-final130.pdf"]
status: active
---

# Walkie-Markie — Indoor Pathway Mapping Made Easy

## Summary

Shen, Chen, Zhang, Moscibroda, Zhang. *Walkie-Markie: Indoor Pathway Mapping Made Easy.* USENIX NSDI 2013. Microsoft Research Asia, Beijing.

Walkie-Markie automatically constructs **indoor pathway maps** from crowdsourced user traces — with no prior knowledge of the building (no floor plan, no AP locations). The core innovation is the **WiFi-Mark**: a pathway location where the received signal strength (RSS) trend from an AP reverses from increasing to decreasing as the user passes the AP's closest point. WiFi-Marks are device-independent and time-stable, unlike raw RSSI values. Multiple user trajectories fused at shared WiFi-Marks are embedded into a 2D pathway map using the Arturia graph-embedding algorithm.

**Key distinction from [[UnLoc-Unsupervised-Localization]]**: Walkie-Markie solves the *mapping* problem (where are the pathways?); UnLoc solves the *localization* problem (where am I?). Walkie-Markie's output — a pathway map — can then enable localization systems.

## The Core Problem

Most indoor localization systems assume either:
- A known floor plan (for constraining IMU drift)
- Known AP locations or a signal strength radio map (for WiFi fingerprinting)

Both are hard to obtain at scale: floor plans may be unavailable, outdated, or proprietary; radio maps require war-driving. Walkie-Markie removes both assumptions, requiring only that **WiFi infrastructure exists** in the building.

## WiFi-Marks: The Key Innovation

A **WiFi-Mark (WiFi-defined landmark)** is a location on a pathway where the RSS trend from an AP changes from increasing to decreasing — i.e., the user is passing the point of closest approach to that AP.

**Why trend instead of absolute RSS?**

| Property | Absolute RSS value | RSS trend reversal (WiFi-Mark) |
|---|---|---|
| Device diversity | Different per phone model | Consistent across devices |
| Time of day | Fluctuates | Stable |
| Phone placement (hand/pocket/bag) | Varies | Robust |
| Accuracy | Poor landmark | ✅ Stable, repeatable |

WiFi-Marks are determined by **physical geometry** (AP position relative to pathway), not RF propagation characteristics — making them fundamentally more stable than raw RSSI fingerprints.

A single AP often generates **multiple WiFi-Marks** along different corridors or pathways, yielding high landmark density.

### WiFi-Mark Stability (measured)

- 90% of WiFi-Marks: position deviation <2.5m across different devices, times of day, and phone positions
- 70% of WiFi-Marks: deviation <1.5m
- Center offset between clusters from different devices: 75% within 1.5m, 95% within 2.5m
- Tested on: HTC G7, Moto XT800, NexusS — different receiver gains, different body positions (hand, trouser pocket, purse, backpack)

## System Architecture

### Mobile Client (Android app)
- **Motion state detection**: Monitors walking state; activates IMU tracking when walking
- **IMU tracking**: Step count + step frequency + walking direction (instantaneous displacement per step)
- **WiFi scanning**: Opportunistic WiFi scans; detects WiFi-Marks in real time
- **Data sent to cloud**: WiFi-Mark identities + estimated displacements between neighbouring WiFi-Marks

### Cloud Backend
- **Trajectory fusion**: Receives partial traces from multiple users; aligns them at shared WiFi-Marks
- **Arturia embedding**: Places WiFi-Marks in 2D space using displacement vectors from user traces
- **Pathway map generation**: Connects WiFi-Marks with user trajectories to form the pathway graph

## Arturia: Graph Embedding Algorithm

A novel algorithm that differs from standard graph embedding by using **displacement vectors** (not scalar distances) as constraints:

- Standard methods use pair-wise distances → loss of directional information
- Arturia uses `<distance, direction>` tuples between neighbouring WiFi-Marks → preserves geometric layout
- Displacement measurements from multiple user traces are averaged → noise cancels out over time
- Result: WiFi-Marks placed in 2D such that the inferred layout matches the real physical layout

## Dead-Reckoning with IMU

IMU sensors (accelerometer, magnetometer, gyroscope) track the user's trajectory between WiFi-Marks:
- **Problem**: IMU error accumulates rapidly with time/distance
- **Solution**: WiFi-Marks bound the distance over which IMU must be relied upon; error is reset at each mark
- Multiple traces over the same path → independent displacement estimates → averaging reduces noise

The more user traces collected, the more accurate the inter-WiFi-Mark displacement estimates become. Accuracy improves continuously until stabilising.

## Evaluation Results

Tested in 2 environments (office building + shopping mall):

### Mapping Accuracy (after 5–6 rounds of walking)

| Metric | Result |
|---|---|
| Max discrepancy — anchor nodes (WiFi-Marks) | <3m |
| Max discrepancy — path segments | <2.8m |
| Stabilisation time | 1–2× additional walking after initial 5–6 rounds |

### Localization Using the Generated Pathway Map

| Metric | Result |
|---|---|
| Average localization error | 1.65m |
| 90th percentile error | 2.9m |

Localization method: estimate displacement from last encountered WiFi-Mark using IMU.

### Convergence Behaviour

Mapping accuracy improves monotonically as more user traces are added. Accuracy stabilises after roughly 10–12 rounds of walking total, requiring no dedicated surveying effort — just normal user walking.

## Comparison with Related Work

| System | Goal | Prior knowledge required | Accuracy |
|---|---|---|---|
| Walkie-Markie (NSDI 2013) | Pathway map construction | WiFi exists | Map: <3m; Loc: 1.65m avg |
| UnLoc (MobiSys 2012) | User localization | One door GPS coordinate | 1.69m median |
| RADAR (INFOCOM 2000) | Localization | War-driven radio map | 2.94m median |
| Zee (MobiCom 2012) | Localization | Floor plan | Sub-metre indoors |
| WiFi fingerprinting (general) | Localization | Radio map + floor plan | 1–3m |

## Limitations

- **WiFi dependence**: Requires existing WiFi infrastructure; cannot work in WiFi-free environments
- **Scanning latency**: Modern phones take ~1.5s per WiFi scan → user walks several steps during scan, introducing WiFi-Mark position uncertainty
- **Path coverage**: Mapping accuracy depends on users walking all pathways; rarely-walked corridors may never be mapped
- **Transient APs**: Mobile hotspots or temporary APs can generate false WiFi-Marks; mitigated by aging/filtering
- **Multi-floor**: Paper focuses on single-floor; multi-floor handling not fully addressed
- **No open source release**: System is a research prototype from Microsoft Research

## Entity Summary

### Hardware
- **Medium**: WiFi RF (passive scanning, no modifications to APs) + IMU (accelerometer, magnetometer, gyroscope)
- **Device**: Standard Android smartphones — HTC G7, Moto XT800, Google NexusS; commodity hardware
- **Infrastructure**: Existing WiFi APs used passively; no installation or configuration required

### Software
- **Client**: Android app (not released)
- **Backend**: Cloud service (not released); Arturia algorithm implemented in-house
- **Language**: Not specified; likely Java (Android) + server-side C/C++ or C#
- **Availability**: Research prototype; no public code release

### Algorithm
- **WiFi-Mark detection**: RSS trend analysis — identify point where RSS switches from increasing to decreasing along walking path; robust peak-detection algorithm with filtering
- **IMU dead-reckoning**: Step count × step length → displacement; magnetometer/gyroscope → direction
- **Arturia**: Displacement-vector graph embedding; minimises layout error given displacement constraints from multiple crowdsourced traces
- **Trajectory fusion**: Align partial traces at shared WiFi-Marks; average displacements across traces
- **Localization**: Displace from last WiFi-Mark position by IMU-estimated trajectory

### Accuracy
- **Map construction**: Anchor node error <3m, path segment error <2.8m after 5–6 walking rounds
- **Localization using map**: Average 1.65m, 90th percentile 2.9m
- **WiFi-Mark position stability**: 70% within 1.5m, 90% within 2.5m across devices/times/positions
- **Comparison context**: Map-based localization (1.65m) is competitive with RADAR (2.94m) while requiring zero dedicated survey effort

## References

| # | Reference | In raw/ |
|---|---|---|
| [4] | Bahl, P., Padmanabhan, V.N. *RADAR.* INFOCOM 2000. | ✅ [[RADAR-WiFi-Fingerprinting]] |
| [35] | Wang, H. et al. *UnLoc: No need to war-drive.* MobiSys 2012. | ✅ [[UnLoc-Unsupervised-Localization]] |
| [26] | ✅ Rai et al. *Zee: Zero-effort crowdsourcing for indoor localization.* MobiCom 2012. → [[Zee-Zero-Effort-Crowdsourcing]] | ⭐ High |
| [36] | ✅ Woodman & Harle. *Pedestrian localisation for indoor environments.* UbiComp 2008. → [[Pedestrian-Dead-Reckoning]] | ⭐ High |
| [24] | Park et al. *Growing an organic indoor location system.* MobiSys 2010. | — |
| [5] | Bruno & Robertson. *WiSLAM: Improving FootSLAM with WiFi.* IPIN 2011. | — |

## Relationships

- [[UnLoc-Unsupervised-Localization]] — complementary system: UnLoc localises users; Walkie-Markie builds the map; both use WiFi landmarks and IMU dead-reckoning
- [[RADAR-WiFi-Fingerprinting]] — Walkie-Markie's generated radio maps can be used as input to RADAR-style fingerprinting
- [[WiFi-Fingerprinting-Advances]] — He & Chan COMST 2016 reviews Walkie-Markie as a temporal/landmark fingerprinting approach; see section on spatial patterns
- [[Indoor-Location-Sensor-Technologies]] — WiFi and IMU modalities used; RSS characteristics and multipath effects discussed in that article
- [[Indoor-Localization-ML-Methods]] — dead-reckoning and crowdsourcing are techniques covered there
- [[Indoor-IPS-Datasets]] — Walkie-Markie generates its own map datasets; relevant to crowdsourced dataset creation

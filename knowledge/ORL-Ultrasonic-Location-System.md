---
tags: [location-system, ultrasonic, indoor-localization, ORL, hardware, multilateration]
date-compiled: 2026-06-14
source-files: ["raw/ORL New Location Technique tr.97.10.pdf"]
status: active
---

# ORL Ultrasonic Location System

## Summary

A wireless ultrasonic indoor location system developed at the Olivetti and Oracle Research Laboratory (ORL), Cambridge, by Andy Ward, Alan Jones, and Andy Hopper (~1997). It determines the 3D position of objects within a building to ~14cm accuracy using time-of-flight multilateration, supporting up to 216 mobile devices over a 75m³ volume. Designed specifically for [[Context-Aware-Computing]] applications in the Active Office.

## Motivation

Prior systems such as the [[Active-Badge]] and ParcTab could only locate objects to room-level granularity. The ORL system was designed to provide fine-grain (sub-15cm) location data suitable for a new class of context-aware applications. See [[Indoor-Location-Sensor-Technologies]] for why other sensor types (electromagnetic, optical, radio) were ruled out.

## Hardware

**Mobile transmitters** (one per tracked object):
- Microprocessor + 418MHz radio transceiver + Xilinx FPGA
- Hemispherical array of 5 ultrasonic transducers (40KHz pulse, 50µs duration)
- Unique 16-bit address; powered by two lithium cells
- Physical size: 100mm × 60mm × 20mm
- Power-saving state between transmissions; average current draw ~400µA; battery life ~3 months

**Ceiling receivers** (fixed infrastructure):
- Mounted on ceiling in a 4×4 grid, 1.2m apart (prototype: 16 receivers)
- Each has an ultrasonic detector → amplifier → rectifier → smoothing filter → 20KHz ADC
- ADC controlled by Xilinx FPGA; receivers daisy-chained via serial network to a controlling PC
- Network supports up to 256 receivers (maximum coverage ~1100m³)

**Controller (PC)**:
- Every 200ms, broadcasts a radio message containing a 16-bit address
- Sends reset signal to all receivers simultaneously with each radio broadcast
- Polls receivers for the time-of-first-signal-peak after each cycle
- Provides 5 location updates per second (theoretical maximum ~50Hz, limited by ultrasonic reverberation decay time of ~20ms)

## Distance Calculation

The interval *t_p* between the reset signal and the first detected signal peak is:

> t_p = t_r + t_u + Σd_i

where *t_r* is radio transit time (negligible vs. ultrasound), *t_u* is ultrasound transit time, and *d_i* are fixed hardware delays.

Since the speed of light *c* >> speed of sound *v_s*, radio transit time is negligible, giving:

> l_u ≈ v_s × (t_p − Σd_i)

Fixed delays are determined empirically; *v_s* is estimated from ambient temperature.

## Position Calculation (Multilateration)

A transmitter at (u, v, w) and a receiver at (x, y, 0) satisfy:

> l² = (x² + y²) + (u² − 2xu) + (v² − 2yv) + w²

This nonlinear model is fitted via nonlinear regression across multiple receivers to estimate (u, v, w²). Because all receivers lie in the ceiling plane and transmitters must be below it, only **3 distance measurements** are needed (vs. 4 in the general 3D case). Additional measurements improve accuracy.

### Reflection Handling

Reflected pulses arrive later than direct pulses, producing overestimated distances. Two elimination techniques are used:

1. **Geometric test**: The difference between any two transmitter-receiver distances cannot exceed the distance between those receivers. Pairs failing this test have their larger value discarded.
2. **Statistical test**: Studentized residuals identify outlier distance measurements during regression. The largest positive outlier is removed and regression is recomputed iteratively until variance falls below threshold or only 3 measurements remain.

Combined, these tests eliminate all reflection-caused errors in >90% of datasets, with <4% of valid measurements incorrectly discarded.

## Orientation Calculation

Two approaches:

1. **Multi-transmitter**: Place 3 mobile devices at known, non-collinear points on a rigid body. Computing all 3 positions determines the body's 3D orientation. Best for stationary/slow-moving objects; the 3 devices should be located consecutively.
2. **Single-transmitter (approximate)**: The hemispherical ultrasonic transmission pattern means the subset of receivers that detect the signal encodes orientation information. Suitable when attaching multiple transmitters is impractical (e.g., doors — open/closed state).

## Performance

| Metric | Value |
|---|---|
| Coverage (prototype) | ~75m³ |
| Max coverage (256 receivers) | ~1100m³ |
| Accuracy (raw, 95th percentile) | within 14cm |
| Accuracy (averaged over 10 cycles, 95th) | within 8cm |
| Update rate (prototype) | 5 Hz |
| Update rate (theoretical max) | ~50Hz |
| Max addressable devices | 2¹⁶ = 65,536 (prototype uses 16-bit address) |
| Battery life (mobile unit) | ~3 months |

## Applications

The paper describes several use cases enabled by fine-grain location data unavailable from [[Active-Badge]]-class systems:

- **Nearest-device services**: More accurate printer/phone routing than Active Badge allows
- **Teleporting System**: Redirect X Window System desktop to the screen facing the user as they move
- **Videophone auto-configuration**: Select cameras, speakers, microphones based on user position
- **Gesture input**: Location devices used to control computers or transfer data between screens
- **Spatially Situated Information Spaces** (Fitzmaurice): PDA "portholes" that display context-relevant info based on proximity to objects — the ORL system replaces tethered electromagnetic trackers
- **Interactive Office** (Hodges & Louie): Replaces multiple sensor types (motion detectors, reed switches) with a single untethered device

## Entity Summary

### Hardware
- **Medium**: Ultrasound (40KHz pulses) for ranging; 418MHz RF radio for addressing and synchronisation
- **Mobile transmitter**: Microprocessor + Xilinx FPGA + 418MHz radio transceiver + hemispherical array of 5 ultrasonic transducers; 100×60×20mm; two lithium cells; 16-bit unique address
- **Ceiling receivers**: Ultrasonic detector → amplifier → rectifier → smoothing filter → 20KHz ADC; Xilinx FPGA; serial daisy-chain network interface; mounted 1.2m apart in 4×4 grid
- **Controller**: Standard PC; 418MHz radio transmitter; serial network controller

### Software
- **Visualisation**: VRML (Virtual Reality Modelling Language) model of ORL building for real-time location display
- **Availability**: Research prototype — not publicly released; described in academic paper only
- **Language/platform**: Controlling PC software not specified; FPGA firmware on Xilinx devices

### Algorithm
- **Distance calculation**: Time-of-flight measurement (ultrasound transit time); fixed-delay correction; speed-of-sound temperature compensation
- **Position calculation**: Multilateration via nonlinear regression (least-squares fitting of transmitter-receiver distance model); minimum 3 measurements required
- **Reflection rejection (geometric)**: Pair-wise distance difference test — difference of two transmitter-receiver distances cannot exceed receiver separation
- **Reflection rejection (statistical)**: Studentized residuals identify outliers; iterative removal of largest positive residual until variance threshold met or 3 measurements remain
- **Orientation**: Multi-transmitter (3 non-collinear points → plane fitting) or single-transmitter (hemispherical transmission pattern → subset of detecting receivers)

### Accuracy
- **Raw readings (95th percentile)**: within **14cm** of true position
- **Averaged readings (10-cycle mean, 95th percentile)**: within **8cm** of true position
- **Limiting factors**: Finite transmitter array size; receiver noise
- **Update rate**: 5 Hz (prototype); theoretical max ~50Hz (limited by 20ms ultrasonic reverberation decay)
- **Comparison**: Significantly finer granularity than [[Active-Badge]] (room-level); comparable to electromagnetic trackers (~1mm) but wireless and scalable; better than radio/GPS indoors (~50cm+)

## Relationships

- [[Context-Aware-Computing]] — the motivating application domain
- [[Active-Badge]] — predecessor system; ORL system provides much finer granularity
- [[Indoor-Location-Sensor-Technologies]] — comparative context for this system vs. other approaches
- [[Indoor-Localization-ML-Methods]] — modern ML-based approaches that complement or replace multilateration
- [[Cricket-Location-Support-System]] — MIT successor system (MobiCom 2000); same RF+ultrasonic TOF principle; adds listener-computed privacy-preserving architecture and < $10/beacon cost

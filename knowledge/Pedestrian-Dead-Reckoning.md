---
tags: [indoor-localization, PDR, dead-reckoning, IMU, ZUPT, inertial-navigation, particle-filter, WiFi, foot-mounted, Kalman-filter]
date-compiled: 2026-06-14
source-files: ["raw/ojw28_thesis.pdf"]
status: active
---

# Pedestrian Dead Reckoning

## Summary

Woodman, O.J. *Pedestrian localisation for indoor environments.* PhD Thesis, University of Cambridge, 2010. (Supervisor: R. Harle.)

This thesis develops a complete indoor localisation system for pedestrians, progressing from raw MEMS inertial sensor data to room-level absolute position. The central contribution is a foot-mounted INS-based **Pedestrian Dead Reckoning (PDR) filter** that uses **Zero Velocity Updates (ZVUs)** to bound heading and position drift, outputting coarse **step events** for consumption by a higher-level **particle filter** that enforces floor plan wall constraints. A WiFi-assisted layer adds absolute position anchoring. The thesis is the canonical reference for ZUPT-based PDR in indoor localisation and is cited by [[UnLoc-Unsupervised-Localization]], [[Walkie-Markie-Indoor-Mapping]], and [[Zee-Zero-Effort-Crowdsourcing]] as the PDR foundation.

**Key result**: Over 9 test walks of 800 steps each (~500m), the PDR filter achieved a final drift of **3.48m** — compared to **43.15 km** for unconstrained inertial navigation over the same path. Worst-case horizontal drift across all 9 walks: **6.42m**; vertical: **5.47m**.

## Background: INS-Based PDR vs Step-Based PDR

Two classes of PDR system exist:

**Step-based systems** detect footfalls using a waist- or foot-mounted accelerometer, estimate step length (fixed or model-based), and track heading via magnetometer. They can be built from a single-axis accelerometer + magnetometer. Typical drift: ~3% of distance travelled. They assume the user walks forwards and cannot distinguish sidesteps.

**INS-based systems** (this thesis) use a full 6-DOF IMU (3-axis gyroscope + 3-axis accelerometer) mounted on the foot. The INS integrates accelerations and angular velocities to continuously track 3D position and orientation. Foot mounting enables Zero Velocity Updates during stance phases. Typical drift: **~0.3% of distance** (NavShoe system, with magnetometers). INS-based systems correctly track sidesteps, stairs, and backward walking.

The trade-off: INS-based systems require the IMU on the foot (less user-acceptable) and a full 3D IMU (more expensive). Step-based systems are cheaper and require more modest sensor placement.

## Chapter 3: MEMS Inertial Sensor Error Sources

MEMS gyroscopes and accelerometers introduce characteristic errors that cause INS drift:

### Gyroscope Error Sources (Table 3.1)

| Error Type | Description | Effect after integration |
|---|---|---|
| **Bias** | Constant offset in angular velocity | Linearly growing orientation error |
| **Random noise** (angle random walk) | Broadband noise from electronics/thermal | Orientation error ∝ √time |
| **Calibration errors** | Scale factor, alignment, linearity errors | Orientation drift ∝ rate × duration |
| **Temperature effects** | Residual bias depends on temperature | Linearly growing orientation error |
| **Bias instability** | Bias fluctuates as a random walk | Second-order random walk in angle |

Bias instability is specified as a 1σ value in °/h over a reference timespan (~100s). Under the random walk model: BRW (°/√h) = BSσ (°/h) / √t (h). This model breaks down over long durations.

### Accelerometer Errors

Analogous error types (bias, noise, scale factor, temperature). After double integration to position, gyroscope heading errors become the dominant source of horizontal position drift — heading bias causes position error that grows quadratically with distance from a turning point.

### Error-State Kalman Filter (Ch3.4)

The INS state is: (C_t, v_t, s_t) — orientation matrix, velocity, displacement. An **error-state EKF** tracks the difference between the INS estimate and the true state. The filter's correction step updates the error state whenever external measurements (ZVUs, WiFi) are applied, then transfers the correction to the underlying INS. This formulation correctly propagates correlations between orientation, velocity, and position errors — enabling heading error corrections to propagate into position corrections.

## Chapter 4: The PDR Filter

### 4.1 INS-Based PDR Architecture

The PDR filter is designed not as a standalone positioning system but as a **relative movement estimator** feeding a higher-level localisation filter (particle filter, Ch5). This requirement drives two design differences from existing PDR systems:

1. **Coarse output rate**: The particle filter update is computationally expensive; step events are generated at ~1 Hz (one per two physical footfalls), not at the 100 Hz IMU sampling rate.
2. **No inter-event corrections**: The particle filter cannot handle step events that contain corrections for errors in previous events — this would violate the Markov assumption. The error-state covariance matrix P_t is **reset** after each step event, severing error correlations across step boundaries (the "cascading filter problem").

### 4.1.1 Zero Velocity Updates (ZVU)

When a foot-mounted IMU is stationary (stance phase), the true velocity is zero. This is applied as a **pseudo-measurement** to the EKF, correcting accumulated velocity error and (via cross-correlations) partially correcting orientation and position errors.

**Stance phase detection algorithm**:

```
Start: |ω_b_t - α̂_t| < 0.5 rad/s  sustained for 10 samples (0.1 s delay)
End:   |ω_b_t - α̂_t| ≥ 0.5 rad/s  sustained for 10 samples (0.1 s early)
```

where ω_b_t is the body-frame angular velocity and α̂_t is the estimated gyroscope bias. The 0.1s delay and 0.5 rad/s threshold were empirically determined. The 0.1s delay before stance-start prevents premature ZVU application; the 0.1s early-end avoids applying ZVUs during the swing phase.

**ZVU measurement model**:

```
z_t = (0, 0, 0)^T          (zero velocity observation)
H_t = (0, I, 0)             (velocity component of state)
R_t: σ² = 0.01 (m/s)²     (residual velocity variance, empirically determined)
```

ZVUs are applied throughout each stance phase (multiple corrections per step).

### 4.1.2 Step Segmentation

The PDR filter segments continuous IMU data into **step events**:

```
e_t = (l_t, δz_t, δθ_t, ξ_t)
```

| Parameter | Description |
|---|---|
| l_t | Horizontal step length in the (x, y) plane |
| δz_t | Change in vertical displacement |
| δθ_t | Change in user heading |
| ξ_t | Offset between user heading and step direction (enables sidestep/backward-walk detection) |

Step events are generated at the **end of each stance phase** (when the INS state has been maximally corrected by ZVUs). If a stance phase exceeds 0.5s (user has stopped walking), an event is generated at the 0.5s mark to avoid delaying the localisation filter.

One step event corresponds to **two physical footfalls** (the IMU is on one foot only). Event frequency: approximately **1 Hz** during normal walking.

The ξ_t parameter (heading-vs-step-direction offset) is a key distinction: unlike waist-mounted step-based systems, the foot-mounted IMU can independently estimate the user's heading and the direction of motion, allowing the higher-level filter to correctly handle sidesteps.

### 4.1.3 The Cascading Filter Problem

When the PDR filter corrects a large error in position (e.g., after applying multiple ZVUs following accumulated drift), the step event that immediately follows reflects the corrected position. The particle filter at the higher level receives this event and interprets the correction as physical movement — introducing a false displacement.

**Solution**: Reset the error-state covariance matrix P_t to a diagonal matrix (asserting the current state is correct) immediately after each step event is generated. This prevents corrections from one event contaminating the next. The cost: some information about accumulated error is discarded. But since large single-stance corrections are rare (ZVUs applied every stance phase prevent large accumulations), this is acceptable.

### 4.2 Evaluation

**Setup**:
- IMU: Xsens MTx (100 Hz; 3-axis gyro + 3-axis accelerometer + 3-axis magnetometer)
- UMPC: OQO (on-body computing platform)
- Ground truth: Active Bat ultrasonic system (3 cm, 95%) — William Gates Building, University of Cambridge
- Ground truth method: Active Bat mounted on foot alongside IMU; range measurements fused into an "extended filter" for drift-free reference trajectory

Three filter configurations compared in each run:
1. **Unconstrained INS** — no ZVUs, no corrections
2. **PDR filter** — ZVUs + P_t reset after each step event
3. **Ground truth filter** — ZVUs + Active Bat range measurements

**Results (9 walks, 800 steps each, ~500m per walk)**:

| Metric | Unconstrained INS | PDR Filter |
|---|---|---|
| Final drift (example walk) | 43.15 km | **3.48 m** |
| Worst-case horizontal drift | — | **6.42 m** |
| Worst-case vertical drift | — | **5.47 m** |
| Worst-case heading error | — | **16.8°** |
| Drift as % of distance (~500m) | >8,000% | **~0.7%** |

Primary error source: heading bias from gyroscope errors. Heading error grows as a random walk around a linearly drifting value. Horizontal position error is largest when the user is at the extreme ends of a corridor (perpendicular to travel). Vertical drift is systematic (downward in most runs) and likely due to mounting position effects on the IMU during stance phases.

### 4.3 Magnetometers

Magnetometers measure absolute heading and can prevent heading drift — but indoor magnetic disturbances (structural steel, electrical cables, lifts) can render their measurements unreliable. The Xsens MTx includes magnetometers; the thesis notes these can be used to bound heading error when the magnetic environment is benign, but notes the deployment risk in buildings.

## Chapter 5: Particle Filter with Environmental Constraints

The PDR filter outputs relative step events; it has no knowledge of absolute position. A higher-level **particle filter** converts step events into absolute position estimates by enforcing floor plan constraints.

**Particle representation**: Each particle represents a hypothesis about the user's current location (x, y) and heading. Particles are propagated forward by each step event (l_t, δz_t, δθ_t, ξ_t), perturbed by noise.

**Wall constraint correction**: After propagation, particles whose implied path would require the user to walk through a wall are eliminated. Surviving particles are reweighted.

**Resampling**: Particle weights are used to concentrate probability mass on feasible locations. Adaptive resampling (Chapter 6) uses likelihood and KL-divergence criteria to trigger resampling only when particle degeneracy is detected, reducing computational cost.

**Convergence**: As the user makes turns (which eliminate many hypotheses), the particle filter converges to the true location. In areas of high topological ambiguity (long straight corridors), convergence may be slow.

This particle filter is the architectural predecessor to the augmented particle filter used in [[Zee-Zero-Effort-Crowdsourcing]], which extends it with additional latent variables (stride length, heading offset) and adds WiFi soft observations.

## Chapter 6: Adaptive Resampling

Standard particle filters resample at every step, which is computationally wasteful when the particle distribution is already concentrated. Chapter 6 develops two adaptive resampling strategies:

- **Likelihood-based resampling**: Resample when the effective sample size N_eff = 1/Σ(w²_i) falls below a threshold
- **KL-divergence resampling**: Resample when the estimated KL divergence between the current and previous particle distribution exceeds a threshold

**Clustered particle filtering**: Particles are grouped into spatial clusters; computational resources are focused on clusters with high total weight. This reduces per-step cost substantially for the particle filter while maintaining accuracy.

## Chapter 7: WiFi-Assisted Localisation

The particle filter uses floor plan geometry alone for correction. Chapter 7 extends this with **WiFi RSS measurements** to provide soft absolute position observations — addressing the long-corridor convergence problem.

**Automatic radio map construction**: Rather than requiring a manually gathered fingerprint database, the system constructs its own radio map. As the particle filter converges to a location hypothesis, WiFi scans observed at that time are annotated with the inferred location. Over time, this auto-constructed radio map provides useful (though noisier) fingerprint data.

**Containment measurements**: A simpler approach — using WiFi RSS to determine which room or zone a user is in (rather than precise coordinates). AP visibility and signal strength thresholds provide coarse location constraints that the particle filter can incorporate without a full radio map.

This WiFi-assisted approach directly inspired the crowdsourced radio map construction in [[Zee-Zero-Effort-Crowdsourcing]], which applies the same principle but at scale with many users.

## Contradictions / Notes

- **Contradicting step-based systems**: Woodman reports INS-based PDR (NavShoe) achieves ~0.3% drift with magnetometers; he cites step-based systems at ~3%. Source A (Woodman) compares foot-mounted INS to waist-mounted step detection. [[Zee-Zero-Effort-Crowdsourcing]] uses a placement-independent step estimator on Android phones that is step-based but augmented by particle filter convergence — a different accuracy regime.
- **Evaluation environment**: The Active Bat ground truth system covers only one wing of the William Gates Building (Cambridge). Results represent that specific environment; other buildings (more turns, different corridor lengths) may yield different drift characteristics.

## Entity Summary

### Hardware
- **IMU**: Xsens MTx — 100 Hz; 3-axis MEMS gyroscope, accelerometer, magnetometer; worn on foot
- **Medium**: Inertial (no RF); WiFi RSS used in Ch7 for absolute anchoring
- **Compute**: OQO UMPC (on-body); Active Bat ultrasonic system for ground truth (3 cm, 95%)
- **Mounting**: Foot-mounted IMU — required for ZUPT stance phase detection

### Software
- **Platform**: Custom C/C++ on Linux (UMPC)
- **Filter**: Error-state EKF for INS correction; particle filter for absolute localisation
- **Availability**: Research prototype; code not publicly released
- **Ground truth**: Active Bat system + custom extended EKF fusing IMU + ultrasonic ranges

### Algorithm
- **Error-state EKF**: Tracks INS state error (δC, δv, δs); propagation + correction; state transfer after correction
- **ZUPT**: Pseudo-measurement z_t = (0,0,0)^T applied during stance phases; σ² = 0.01 (m/s)²; stance detection via |ω - α| < 0.5 rad/s for 10 samples
- **Step segmentation**: e_t = (l_t, δz_t, δθ_t, ξ_t) at ~1 Hz; generated at stance phase end; P_t reset after each event
- **Particle filter**: Step event propagation + wall elimination + resampling; adaptive (likelihood-based, KL-divergence) resampling; clustered particle filtering
- **WiFi assistance**: Auto-constructed radio map from converging particle hypotheses; containment measurements for coarse zone detection

### Accuracy
- **PDR-only drift**: ~0.7% of distance; final 3.48m over 500m; worst-case 6.42m horizontal
- **Comparison baseline**: Unconstrained INS: 43.15 km drift over same path; NavShoe INS: 0.3% (with magnetometers)
- **Particle filter**: Converges to correct room following user turns; exact accuracy dependent on building topology and turn frequency

## References

| # | Reference | In raw/ |
|---|---|---|
| [6] | Harle, R.; Hopper, A. *The Potential for Location-Aware Power Management.* UbiComp 2008. | — |
| [14] | Foxlin, E. *Pedestrian tracking with shoe-mounted inertial sensors.* IEEE CGA 2005. | — |
| [15] | Beauregard, S.; Haas, H. *Pedestrian dead reckoning: A basis for personal positioning.* WPNC 2006. | — |
| [16] | Skog, I.; Händel, P. *Calibration of a MEMS inertial measurement unit.* Metrology 2006. | — |
| [21] | Robertson, P.; Angermann, M.; Krach, B. *Simultaneous localization and mapping for pedestrians using foot-mounted IMUs.* UbiComp 2009. | — |
| [44] | Alvarez et al. *Pedestrian dead reckoning by inertial sensing.* Microtech. 2003. | — |
| [45] | Sabatini et al. *Assessment of walking features from foot inertial sensing.* IEEE TBME 2005. | — |
| [46] | Stirling et al. *A novel shoe-mounted pedestrian navigation system.* ENC 2003. | — |
| [47] | Mathie et al. *Detection of body movement activities by accelerometer.* Med. Biol. Eng. Comput. 2003. | — |
| [52] | Doucet et al. *Sequential Monte Carlo Methods in Practice.* Springer 2001. | — |

## Relationships

- [[UnLoc-Unsupervised-Localization]] — cites Woodman [22] for PDR component; UnLoc uses dead reckoning to propagate location between organic landmarks; inherits the step event model concept
- [[Walkie-Markie-Indoor-Mapping]] — cites Woodman [36] for IMU-based walking trajectory estimation; Walkie-Markie uses PDR to reconstruct paths between WiFi-Mark landmarks
- [[Zee-Zero-Effort-Crowdsourcing]] — cites Beauregard & Haas [6] for PDR; Zee's augmented particle filter directly extends Woodman's particle filter + WiFi approach; Zee's WiFi auto radio map construction is the crowdsourced version of Ch7
- [[Indoor-Localization-ML-Methods]] — trajectory learning and particle filter + GP methods surveyed there extend the particle filter model described here
- [[Indoor-Location-Sensor-Technologies]] — IMU/MEMS technology, MEMS error characteristics, and inertial localization covered in sensor overview section
- [[Indoor-IPS-Datasets]] — Active Bat (used as ground truth here) described as an ultrasonic infrastructure system there
- [[ORL-Ultrasonic-Location-System]] — Active Bat is a successor to ORL's ultrasonic location system; same principle (ceiling receivers, mobile bats) used as Woodman's ground truth

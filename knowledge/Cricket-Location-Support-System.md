---
tags: [indoor-localization, ultrasonic, RF, TOF, beacons, privacy, decentralized, MIT]
date-compiled: 2026-06-14
source-files: ["raw/Balakrishnan_cricket_mobicom00.pdf"]
status: active
---

# Cricket — The Cricket Location-Support System

## Summary

Priyantha, N.B., Chakraborty, A., Balakrishnan, H. *The Cricket Location-Support System.* ACM MobiCom 2000. MIT Laboratory for Computer Science. (Project Oxygen.)

Cricket is an in-building location-support system that combines **RF + ultrasonic concurrent transmission** from ceiling-mounted beacons to determine which space a mobile listener is in. Unlike traditional location-*tracking* systems (Active Badge, Bat, RADAR), Cricket is a location-*support* system: **the listener computes its own location** and never broadcasts it, preserving user privacy and enabling decentralised administration. The system achieves a spatial granularity of **4×4 feet (~1.2×1.2m) grid cells** at a hardware cost of **< $10 per beacon**.

Cricket is the direct successor to the ORL Bat system ([[ORL-Ultrasonic-Location-System]]) and is cited as a foundational ultrasonic IPS by [[Horus-WLAN-System]] and [[Pedestrian-Dead-Reckoning]].

## Design Goals

Five guiding constraints distinguished Cricket from contemporaneous systems:

| Goal | Cricket approach | Contrast with Bat/RADAR |
|---|---|---|
| **User privacy** | Listener computes location; never transmitted | Bat and RADAR track centrally |
| **Decentralised admin** | Each space owner deploys and configures their own beacon | Bat requires coordinated ceiling installation |
| **Network heterogeneity** | Works regardless of device network type | RADAR WiFi-only |
| **Low cost** | < $10 per beacon (off-the-shelf) | Bat: high-cost centrally coordinated hardware |
| **Easy deployment** | No explicit coordination between beacons | Bat requires ceiling sensor matrix |

## RF + Ultrasonic Concurrent Transmission

The key mechanism: **speed-of-light vs speed-of-sound**.

Each beacon simultaneously transmits:
- An **RF data packet** (identifying the space and beacon UID)
- An **ultrasonic pulse**

The listener receives the RF signal first (light speed) and immediately turns on its ultrasonic receiver. When the ultrasonic pulse arrives (sound: ~1 ft/ms), the listener measures the **time difference** between RF reception and ultrasonic arrival. This time difference = distance to the beacon.

```
distance ≈ (t_US - t_RF) × speed_of_sound
```

This approach (concurrent RF + US) is the same principle as measuring the time gap between lightning and thunder. It differs from the ORL Bat system, which uses a centrally coordinated transmitter-receiver scheme.

### RF and Ultrasonic Ranges

| Medium | Range (line of sight) | Range (with obstacles) |
|---|---|---|
| RF | ~80 feet | ~40 feet |
| Ultrasonic | ~30 feet (angled transmitter) | Less through walls |

Because RF range exceeds ultrasonic range, a listener can never receive an ultrasonic pulse without also receiving the corresponding RF signal — a key design property ensuring correct pairing.

## Randomised Beacon Transmission

Beacons transmit at an **average rate of 4 Hz**, with random inter-transmission delays uniformly distributed in [150, 350] ms. The randomisation is critical because there is **no explicit coordination** between beacons — they cannot hear each other's transmissions. Randomisation reduces collision probability and ensures that, over multiple samples, the listener eventually receives uncontested transmissions from the nearest beacon.

The random schedule is designed so that at most 5–6 beacons are likely to be within range of each other at any location. With this density, the probability of persistent collisions is negligibly small.

## Interference Handling

Three types of RF/US interference can occur when multiple beacons are in range:

| Case | Mechanism | Effect |
|---|---|---|
| **RF-A : US-RA** (reflected US from correct beacon) | Direct US blocked; reflected arrives instead | Distance overestimate |
| **RF-A : US-I** (correct RF, interfering US) | Both RFs collide → corrupted, ignored **or** interferer wins | Either ignored or wrong distance |
| **RF-A : US-RI** (correct RF, reflected interfering US) | Stray reflected US arrives first | Wrong distance |

The system reduces Case 1 by pointing beacons directly at their target space (limiting reflected paths). Cases 2 and 3 are reduced by: (a) the randomised transmission schedule, (b) using a slower RF data rate (so the US pulse arrives while the RF packet is still being received — ensuring US and RF from the same beacon are temporally correlated).

## Listener Inference Algorithms

Listeners collect multiple distance samples from multiple beacons and run an inference algorithm to determine the closest beacon (and hence the current space). Three algorithms were developed and compared:

- **Majority**: Most frequently sampled beacon wins
- **MinMean**: Beacon with lowest mean estimated distance wins
- **MinMode**: Beacon with lowest mode (most frequent bin) of estimated distance wins

**MinMode performs best**, especially near location boundaries. It is robust to occasional wrong distance estimates from interference, since stray samples appear in different distance bins from the true minimum.

Number of samples needed: with MinMode, **error rate drops to near zero within 2–3 samples** after crossing a boundary (i.e., within ~0.5 seconds of arrival in a new space).

## Evaluation Results

**Experiment 1 — Boundary detection:**
- Two beacons 4 feet apart on ceiling; listener stepped at 0.5-foot intervals
- Correct beacon identified with high confidence when listener is **> 1 foot from the boundary**
- Standard deviations of ultrasonic propagation time do not overlap at >1 foot displacement
- **Achieved granularity: 4×4 feet grid cells** (~1.2m × 1.2m)

**Experiment 2 — Static performance with interference:**
- 4 legitimate beacons (covering rooms X, Y, Z) + 2 RF-only interferers (ultrasonic disabled)
- Interferer contribution: < 0.4% of total samples — confirming randomisation effectiveness
- MinMode and MinMean both: 0% location error at the test positions (even near boundaries)

**Experiment 3 — Mobile performance:**
- Listener walked through boundaries at walking speed
- Measurement: how quickly the system stabilises to the correct room after crossing
- **MinMode converges to correct location within 1 second** of crossing a boundary at walking speed

## System Architecture

```
Ceiling beacons (per space)
  ├── RF transmitter: space string + beacon UID
  └── Ultrasonic transmitter: concurrent pulse
         ↓
Mobile listener (on user device)
  ├── Hear RF → record t_RF; turn on US receiver
  ├── Hear US → record t_US; compute distance = Δt × speed_of_sound
  ├── Collect N samples per beacon
  ├── Run inference algorithm (MinMode)
  └── Determine current space → query INS/resource discovery
```

## Applications

Cricket was integrated with the **MIT Intentional Naming System (INS)** for resource discovery:

- **Active maps**: Device receives a map of services/resources in the current space; adapts UI to context
- **Device control**: Automatic configuration based on location (e.g., projector control, printer selection)
- **Privacy-preserving services**: User queries for services at a location without broadcasting their position

## Comparison with Contemporary Systems

| Property | Cricket | Active Badge (ORL) | Bat (ORL) | RADAR (Microsoft) |
|---|---|---|---|---|
| Privacy | ✅ Yes | ❌ Central tracking | ❌ Central tracking | Possible (with user computation) |
| Decentralised | ✅ Yes | ❌ No | ❌ No | ❌ Centralized RF DB |
| Network heterogeneity | ✅ Yes | ✅ Yes | ✅ Yes | ❌ WiFi only |
| Cost | ✅ Low ($10) | ❌ High | ❌ High | ✅ No extra cost |
| Ease of deployment | ✅ Easy | ❌ Sensor matrix | ❌ Sensor matrix | ❌ RF mapping |
| Accuracy | 4×4 ft cells | Room-level | ~3cm (centralised) | ~2.94m |

## Limitations

- **Space-level granularity only**: Cricket identifies which 4×4 ft cell the user is in, not precise coordinates within that cell. Sub-cell precision requires denser beacon deployment.
- **Ultrasonic occlusion**: Large obstacles (furniture, people) can block the direct ultrasonic path, causing missed readings. This is handled by collecting multiple samples.
- **Environmental factors**: Temperature and humidity affect ultrasonic speed (~2 ft/s per °C). Effects are minimised by keeping beacons away from boundaries, but cannot be eliminated.
- **Beacon pointing**: Ultrasonic transmitters must be physically aimed at the target space to limit range overlap. Incorrect pointing degrades accuracy.
- **No floor differentiation**: Ultrasonic signals don't penetrate floors; separate beacon networks needed per floor.

## Entity Summary

### Hardware
- **Medium**: RF (data channel, ~40-80 ft range) + ultrasonic (timing, ~30 ft range)
- **Beacons**: Off-the-shelf RF + ultrasonic transmitters; < $10 per unit; ceiling-mounted
- **Listeners**: Custom receiver hardware on mobile devices; no special infrastructure at receiver
- **Frequency**: Beacons transmit at ~4 Hz with random jitter in [150, 350] ms

### Software
- **Inference**: Majority, MinMean, MinMode algorithms; MinMode recommended
- **Resource discovery**: Integrated with MIT INS (Intentional Naming System)
- **Platform**: Research prototype, MIT Project Oxygen

### Algorithm
- **Time-of-flight (TOF)**: Δt = t_US − t_RF; distance = Δt × speed_of_sound
- **Randomised broadcast**: Random inter-transmission delay [150, 350] ms prevents coordinated collisions
- **MinMode inference**: Mode of distance samples per beacon per sliding window; most collision-resistant
- **Sample collection**: Multiple samples per location; typically 2–3 needed for accurate boundary detection

### Accuracy
- **Spatial granularity**: 4×4 feet (~1.2×1.2m) grid cells
- **Boundary accuracy**: > 1 foot clear separation; < 0.4% interference from adjacent beacons
- **Convergence time**: ~1 second to correct location identification after boundary crossing (MinMode)
- **Comparison**: Active Badge (room-level); Bat (~3cm centralised); RADAR (~2.94m)

## References

| # | Reference | In raw/ |
|---|---|---|
| [4] | ✅ Bahl, P., Padmanabhan, V.N. *RADAR.* IEEE INFOCOM 2000. → [[RADAR-WiFi-Fingerprinting]] | ⭐ High |
| [7] | ✅ Harter, A., Hopper, A. *A Distributed Location System for the Active Office.* IEEE Network 1994. → [[ORL-Ultrasonic-Location-System]] | ⭐ High |
| [19] | ✅ Want, R., Hopper, A. *The Active Badge Location System.* ACM TOIS 1992. → [[Active-Badge]] | ⭐ High |
| [1] | Adjie-Winoto, W. et al. *The design and implementation of an intentional naming system.* SOSP 1999. | Medium — INS resource discovery |
| [9] | Edwards, W.K. et al. *Speakeasy: Dynamic voice messaging.* UbiComp 2000. | Low |
| [17] | Ulrich, I., Borenstein, J. *VFH+: Reliable obstacle avoidance for fast mobile robots.* ICRA 1998. | Low — collision avoidance reference |

## Relationships

- [[ORL-Ultrasonic-Location-System]] — Cricket is the conceptual successor to the ORL Bat system; both use ultrasonic TOF; Cricket decentralises the infrastructure and adds RF for concurrent timing
- [[Active-Badge]] — active badge cited as predecessor; Cricket shares the beacon-on-badge paradigm but adds ultrasonic distance and user privacy
- [[RADAR-WiFi-Fingerprinting]] — compared qualitatively in Cricket's evaluation; RADAR requires central RF database; Cricket requires no prior mapping
- [[Horus-WLAN-System]] — Horus cites Cricket [19] as ultrasonic complement to WiFi-based approaches
- [[Indoor-Location-Sensor-Technologies]] — ultrasonic TOF systems and hybrid RF/ultrasonic approaches covered in sensor survey
- [[Pedestrian-Dead-Reckoning]] — Woodman thesis uses Active Bat (successor to Cricket) as ground truth system; Cricket's ultrasonic TOF principle is the same

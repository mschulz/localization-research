---
tags: [active-badge, infrared, indoor-localization, ORL, wearable, technology]
date-compiled: 2026-06-14
source-files: ["raw/ORL New Location Technique tr.97.10.pdf"]
status: active
---

# Active Badge

## Summary

An infrared-based wearable location tag developed at the Olivetti and Oracle Research Laboratory (ORL), Cambridge, by Want, Hopper, Falcão, and Gibbons (published in *ACM Transactions on Information Systems*, January 1992). Each badge broadcasts a unique IR code that is detected by a network of sensors placed around a building, allowing room-level location of personnel and equipment. The foundational technology for early [[Context-Aware-Computing]] deployments at ORL.

## How It Works

- Each badge has a **globally unique code** broadcast periodically via infrared
- IR signals reflect off walls and furniture, flooding the surrounding area
- A **network of fixed IR sensors** placed around the building detects which badges are visible from which locations
- By determining which sensor(s) detected a badge, its location is inferred — typically to **room granularity**

An extension called the **Equipment Tag** is a low-power variant of the badge used to track equipment (computers, printers) rather than people. The "nearest printer" service demonstrated by ORL uses Equipment Tags on both laptops and printers to automatically configure the laptop to use whichever printer is closest as it moves around the building.

## Applications

Active Badge location data was used for:

- **Telephone call routing** — incoming calls forwarded to the handset in the same room as the callee
- **Security** — access control and personnel tracking
- **Environmental control** — lighting/temperature adjustment based on room occupancy
- **Nearest-printer service** — automatic printer configuration for portable computers

## Key Limitation

Active Badge locates objects only to **room granularity**. IR signals are naturally contained within rooms (acting as "containers"), which caps the precision available. This limitation drove development of the [[ORL-Ultrasonic-Location-System]], which achieves sub-15cm accuracy.

Room-level granularity is insufficient for applications such as:
- Gesture-based computer control
- Screen-facing desktop teleportation
- Connecting devices by physical proximity
- Differentiating between people in the same room

## References

- Want, R., Hopper, A., Falcão, V., Gibbons, J. *The Active Badge Location System.* ACM Transactions on Information Systems, January 1992.
- Want, R., Hopper, A. *Active Badges and Personal Interactive Computing Objects.* IEEE Transactions on Consumer Electronics, February 1992.
- Elrod, S., Hall, G., Costanza, R., Dixon, M., Rivieres, J. *Responsive Office Environments.* Communications of the ACM, July 1993.

## Entity Summary

### Hardware
- **Medium**: Infrared (IR) light — signals flood rooms via reflections off walls and furniture
- **Badge**: Small wearable IR transmitter; broadcasts a globally unique code; periodic transmission
- **Equipment Tag**: Low-power badge variant for tracking equipment (printers, laptops) rather than people
- **Fixed sensors**: Network of IR detectors placed around the building; passive infrastructure; detect badge presence

### Software
- **Call routing system**: Maps badge-to-sensor presence data to room lookup, routes phone calls accordingly; running on ORL network infrastructure; research prototype, not publicly released
- **Nearest-printer service**: Automatically configures portable computer to use closest printer based on equipment tag proximity
- **Availability**: Research prototype at ORL/Cambridge; system described in academic papers only; no known open-source release

### Algorithm
- **Localisation**: Presence detection — which fixed sensor(s) detected a badge; room inferred by sensor-to-room mapping table; no distance or geometric computation
- **IR containment**: IR signals are naturally blocked by walls, making each room a detection zone; reflections within a room ensure reliable detection throughout
- **No ranging**: No measurement of signal strength or timing — purely binary presence/absence per sensor

### Accuracy
- **Granularity**: Room-level only — cannot resolve position within a room
- **Comparison**: ~10–20× less spatial precision than [[ORL-Ultrasonic-Location-System]] (8–14cm); sufficient for call routing and environmental control but unable to support gesture control, screen-facing, or inter-device proximity applications

## Relationships

- [[Context-Aware-Computing]] — Active Badge is the earliest practical context-aware location system
- [[ORL-Ultrasonic-Location-System]] — supersedes Active Badge for fine-grain location
- [[Indoor-Location-Sensor-Technologies]] — Active Badge (IR) is one of several sensor technology approaches compared

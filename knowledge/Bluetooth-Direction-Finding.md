---
tags: [indoor-localization, Bluetooth, BLE, direction-finding, AoA, AoD, antenna-array, IQ-sampling, RTLS, IPS, proximity, triangulation, trilateration, standards, vendor-whitepaper]
date-compiled: 2026-08-20
source-files:
  - "raw/1901_Enhancing Bluetooth Location Service_FINAL.pdf"
status: active
source-type: standards-body whitepaper — no experimental evaluation, no ground truth, no baseline
---

# Bluetooth Direction Finding (AoA / AoD)

## Summary

Bluetooth Core Specification **5.1** added an optional **direction finding** feature, letting a device determine the *direction* of a signal rather than only its strength. Until then every standard Bluetooth location service rested on one building block: use the Bluetooth Low Energy radio to decide whether two devices are in range and, via **RSSI**, roughly how far apart — which supports metre-level positioning at best. Direction finding adds an angle, so positioning systems can use **triangulation** alongside RSSI trilateration.

This is the dedicated home for Bluetooth direction finding as a **mechanism and a standard**. [[Indoor-Localization-ML-Methods]] already carries BLE 5.1 AoA from the *measurement* side — a deep-learning fusion of RSSI and AoA (PCA + Kalman filter + CNN) reported at **sub-metre** accuracy — and that section is the empirical counterpart to this one. What is only here: the angle-of-**departure** method, the proximity-vs-positioning taxonomy, the RTLS/IPS topology inversion that decides which method fits which deployment, and the interoperability-profile status.

> **⚠️ Read this as a claim, not a measurement.** The source is a Bluetooth SIG whitepaper. It contains **no experiment, no dataset, no ground-truth methodology and no baseline comparison** — every accuracy figure below is a vendor/standards-body assertion about what deployments *can* achieve, and its market projections come from a commissioned analyst forecast. It is compiled because it is the authoritative description of *how the feature works*, which is verifiable from the specification; treat the numbers as claims to be tested against measured sources.

## Bluetooth location services taxonomy

The whitepaper splits the solution space in two, and the split matters because AoA and AoD are aimed at different halves:

**Proximity solutions** — determine where two devices are relative to each other.
- **Point-of-interest (PoI) information**: battery-powered transmitters ("beacons") deployed through a venue; a phone app listens and surfaces content for whatever it hears. Museums, retail.
- **Item finding**: "personal property tags" attached to keys or a wallet; the phone estimates whether the tag is near and roughly how close.

**Positioning systems** — determine a device's physical location, and need infrastructure.
- **RTLS (real-time locating systems)**: fixed **locators** (receivers) report every tag they hear, plus RSSI, to a central **location engine**, which trilaterates against known locator positions. Asset and people tracking — pallets, forklifts, workers, ultrasound machines, patients.
- **IPS (indoor positioning systems)**: the inverse topology. Fixed **locator beacons** transmit; the phone hears several, and trilaterates from their known positions and RSSI. Wayfinding in malls, airports, large offices.

Claimed accuracy for both positioning families as of 2019: **metre-level, ~1–10 m**, influenced by floor plan and the number of nodes deployed.

## How the two methods work

Both rest on an **antenna array** and on **IQ sampling** taken while switching between the array's active antennas. The array sees a phase difference across its elements because each sits a slightly different distance from the transmitter, and that phase difference yields the relative signal direction.

| | **Angle of Arrival (AoA)** | **Angle of Departure (AoD)** |
|---|---|---|
| Transmitter | The device being located (e.g. an RTLS tag), **single antenna** | The reference device (e.g. an IPS locator beacon), **antenna array** |
| Receiver | The infrastructure (e.g. a locator), **antenna array** | The mobile device (e.g. a phone), **single antenna** |
| Who computes the angle | Receiver, from IQ samples across its own array | Receiver, from IQ samples of the array's several signals |
| Intended for | RTLS, item finding, PoI information | IPS — wayfinding |

The asymmetry is a deployment argument, not a physics one: AoA puts the array cost in the infrastructure and keeps the tag simple, while AoD puts the array in the infrastructure beacon and lets an ordinary single-antenna phone compute its own position.

**Profiles.** Bluetooth publishes profile specifications so implementations interoperate. At the time of writing, separate AoA and AoD profiles were "in their final stages of development" — so this document describes a feature whose interoperability layer was not yet released.

## What the whitepaper claims direction finding buys

- **Item finding** gains *direction*, not just distance — point the phone at the lost item — conditional on handset vendors shipping AoA support.
- **PoI information** gains selection by pointing: aim at one exhibit among several in a room.
- **RTLS** improves "down to centimetre-level, depending on deployment considerations".
- **IPS** gains accuracy *or* keeps its accuracy with **fewer locator beacons deployed** — the cost argument rather than the precision one.

## Entity Summary

- **Hardware / medium:** Bluetooth Low Energy radio (2.4 GHz ISM). Antenna arrays on one side of the link; single antenna on the other. Battery-powered tags/beacons; fixed locators; a central location engine.
- **Software:** Location engine performing trilateration (RSSI) and triangulation (angles); phone applications for proximity and IPS use cases; AoA/AoD profiles pending at publication.
- **Algorithm:** Phase-difference angle estimation from **IQ samples captured while switching antennas** across an array; RSSI-based distance estimation; **trilateration** from three known reference points; **triangulation** from angles.
- **Accuracy:** *Claimed, unmeasured.* RSSI-only RTLS and IPS: metre-level, ~1–10 m. RTLS with direction finding: "centimetre-level, depending on deployment considerations."
- **Evaluation:** **None.** No testbed, no dataset, no error distribution, no environment description.
- **Ground truth:** **Not applicable** — no experiment is reported.
- **Metrics:** None computed. Accuracy is described qualitatively by order of magnitude.
- **Baseline:** **None.** The implicit comparison is RSSI-trilateration Bluetooth against direction-finding Bluetooth, but no head-to-head measurement is given.
- **Market context:** >400 million location-services-capable Bluetooth products shipping per year by 2022, attributed to ABI Research. A commissioned forecast, not a measurement.

## Contradictions / Scope Notes

- **The centimetre-level claim outruns the best measured AoA result in this corpus by an order of magnitude.** The nearest like-for-like evidence is in [[Indoor-Localization-ML-Methods]]: BLE 5.1 AoA fused with RSSI through PCA + Kalman filter + CNN, reported at **sub-metre** — better than RSSI-only BLE (1–3 m there, metre-level in Altini et al.'s per-orientation neural networks and Bruno & Delmastro's BIPS in [[Indoor-Location-Sensor-Technologies]]), but not centimetre-level. The whitepaper hedges with "depending on deployment considerations" and reports no measurement at all, so treat centimetre-level as an upper bound under favourable conditions rather than an achieved figure. **Recorded as open, not reconciled** — a measured AoA/AoD evaluation with a stated environment and error distribution would be a high-value addition to `raw/`.
- **Angle estimation is not new; its availability in a commodity radio is.** AoA is long-established in the array-processing literature, and this corpus already carries AoA/AoD in other contexts — [[UWSN-Localization]] (underwater AoA), [[Compressive-Sensing-Localization]] (angle information inside channel covariance). The contribution of Core Spec 5.1 is standardisation and interoperability, not method novelty, and the whitepaper says as much: "established, time-tested methods … to a proven, trusted radio."
- **Accuracy claims are conditional on deployment and on handset support.** Both the item-finding and PoI improvements require smartphone vendors to implement AoA, which the whitepaper explicitly frames as a vendor choice rather than a given.

## References (preserved for deeper research)

- Bluetooth SIG, *Enhancing Bluetooth Location Services with Direction Finding*, 2019. Full text in `raw/1901_Enhancing Bluetooth Location Service_FINAL.pdf`.
- Bluetooth Core Specification v5.1 — the normative source for the direction-finding feature, IQ sampling and antenna switching. **Not in `raw/`; worth acquiring** for any implementation-level work.
- AoA and AoD profile specifications — unreleased at the time of writing; a current version would settle how interoperable implementations actually sample and report angles.
- ABI Research shipment forecast (cited as footnote 1) — the source of the 400 million/year figure.

## Relationships

- [[Indoor-Location-Sensor-Technologies]] — the Bluetooth/BLE and iBeacon entries there cover the RSSI-based generation this feature extends; this article is the dedicated direction-finding home cross-linked from it.
- [[Indoor-Localization-Applications-by-Sector]] — the RTLS use cases here (warehouse asset flow, hospital equipment, worker safety zones) are the same industrial and healthcare deployments catalogued there.
- [[Indoor-Localization-ML-Methods]] — holds the measured Bluetooth fingerprinting work that these claims should eventually be tested against.
- [[Compressive-Sensing-Localization]] — channel covariance carries angle-of-arrival information, the same physical quantity estimated here by phase difference across an array.
- [[UWSN-Localization]] — AoA as one of the classical ranging/angle modalities, in a medium where it behaves very differently.

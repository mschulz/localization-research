---
tags: [context-aware-computing, ubiquitous-computing, active-office, concept]
date-compiled: 2026-06-14
source-files: ["raw/ORL New Location Technique tr.97.10.pdf"]
status: active
---

# Context-Aware Computing

## Summary

A paradigm in which computing devices autonomously adapt their behaviour based on observations of their environment — who is nearby, where the device is located, and what is happening around it. Location information from sensor systems is a primary input. First explored in the early 1990s at Xerox PARC and ORL/Cambridge; foundational motivation for the [[ORL-Ultrasonic-Location-System]].

## Core Concept

A context-aware computer must determine the state of its surroundings without user intervention. Location is one of the most tractable forms of context — if a device knows where it is and who is near it, it can make useful inferences. The goal is to transfer configuration burden from users to devices.

Key properties a context-aware device needs:
- Awareness of its own location (fine-grain, not just room-level)
- Awareness of nearby people and objects
- Ability to adapt behaviour (personalisation, reconfiguration, triggering actions)

## Weiser's Ubiquitous Computing Vision

Mark Weiser (*"The Computer for the 21st Century"*, Scientific American, September 1991) described a future in which computing elements are integrated so deeply into the environment that they become invisible to everyday awareness. Key points:

- Devices will range in size to support different tasks
- Devices will **not** be specialised to one task; they will adapt based on surroundings
- The computing "fabric" of an environment will be seamless and self-configuring

This vision is the theoretical backdrop against which both the [[Active-Badge]] and [[ORL-Ultrasonic-Location-System]] were developed.

## The Active Office

Introduced by Harter & Hopper (*"A Distributed Location System for the Active Office"*, IEEE Network, January 1994). The Active Office is a working environment in which location-aware equipment is commonplace — devices reconfigure themselves as people move through the space. Applications include:

- Telephone call routing to the handset nearest the callee
- Automatic printer selection
- Environmental control (lighting, temperature)
- Security

The ORL ultrasonic system was designed to enable a more capable Active Office than was possible with room-level [[Active-Badge]] data.

## Early Implementations

**Active Badge system** (Want, Hopper et al., ORL/Cambridge): IR wearable badges providing room-level location. Used for call routing, security, environmental control. See [[Active-Badge]].

**ParcTab** (Adams, Schilit et al., Xerox PARC): IR-based PDA using the same cellular network for both communication and location. Schilit et al. used ParcTabs to implement:
- Context-triggered actions (actions fired when a user enters/leaves a zone)
- Automatic device reconfiguration
- A *memory prosthesis* ("Forget-me-not", Lamming & Flynn) — a biography built from contextual observations that could be consulted later

## Location as a Proxy for Context

Location is not the only form of context, but it is a practical entry point:
- Room-level location (Active Badge) enables coarse adaptation (routing calls, choosing printers)
- Sub-15cm location (ORL ultrasonic) enables fine-grained adaptation (gesture recognition, touching devices together to connect them, screen-facing desktop teleportation)
- Orientation adds another dimension — knowing which way a person faces enables smarter display/audio selection

## Entity Summary

### Hardware
- **Active Badge**: Small wearable IR transmitter; medium = infrared light; broadcast unique IR code periodically
- **ParcTab**: IR-based PDA; medium = infrared light; dual-purpose communication and location
- **Active Office infrastructure**: Network of fixed IR sensors placed throughout building; no special user hardware beyond the badge
- **ORL Ultrasonic system**: See [[ORL-Ultrasonic-Location-System]] — ultrasound + RF medium

### Software
- **Teleporting System** (Richardson et al.): Redirects X Window System desktop to nearby display; runs on standard Unix/X11; not publicly released as a product
- **"Forget-me-not"** (Lamming & Flynn): Memory prosthesis application; builds biography from contextual log; research prototype only
- **ParcTab applications** (Schilit et al.): Context-triggered actions and auto-reconfiguration; research code, not commercially released
- **Availability**: All systems described are 1990s research prototypes; no known open-source release

### Algorithm
- **Active Badge localisation**: Sensor presence detection — which fixed sensors detected a badge; room-level inference by sensor-to-room mapping; no geometric computation
- **Context inference**: Rule-based triggers (if badge detected in room X, route calls to phone Y); event-driven
- **ParcTab context**: Zone-based triggers; NLOS-tolerant (IR flooding fills rooms)

### Accuracy
- **Active Badge**: Room-level granularity only — cannot distinguish positions within a room
- **ParcTab**: Room-level (same IR containment principle)
- **Comparison**: Both systems are ~10–20× less precise than the [[ORL-Ultrasonic-Location-System]] (8–14cm); adequate for coarse applications (call routing, environmental control) but insufficient for gesture control, screen-facing or proximity-based interactions

## Relationships

- [[Active-Badge]] — earliest practical implementation; room-level granularity
- [[ORL-Ultrasonic-Location-System]] — fine-grain location enabling next generation of context-aware apps
- [[Indoor-Location-Sensor-Technologies]] — the sensor landscape that makes context-awareness possible

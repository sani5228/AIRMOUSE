# Air Mouse for College Teaching — AI Coding Guide

## 1. Purpose

This document controls AI-assisted implementation behavior. The AI treats project documentation as source of truth and implements the smallest practical solution satisfying documented requirements. The goal is a reliable college teaching prototype, not unnecessary enterprise complexity.

Do not write source code from this guide. Do not invent frameworks, libraries, protocols, ports, databases, package managers, operating systems, or implementation details. Unresolved choices are **TBD**.

## 2. Documentation Hierarchy

Consult documents in this order:

```text
MASTER_SPEC.md
↓
PRD.md
↓
TECH_STACK.md
↓
ARCHITECTURE.md
↓
Feature-specific documents
↓
API_PROTOCOL.md / DATA_MODEL.md
↓
UI_UX.md / UI_COMPONENTS.md
↓
IMPLEMENTATION_RULES.md
↓
PROJECT_STRUCTURE.md
↓
Other supporting documentation
```

If documents conflict: identify conflict, prefer higher authority, do not silently merge contradiction, and record unresolved conflict as **TBD**. Update documentation once resolved.

## 3. Read Before Coding

Before changing a feature, AI must:

1. Inspect repository.
2. Read relevant requirements.
3. Identify existing implementation and partial work.
4. Reuse existing components/services where possible.
5. Identify affected modules/dependencies/tests.
6. Determine how application currently runs.
7. Only then modify code.

Never immediately generate a new implementation without repository inspection.

## 4. Requirement Traceability

Every significant implementation follows:

```text
Requirement → Feature → Component/Service → Implementation → Test
```

Do not implement speculative features merely because they seem useful.

## 5. Architecture Rules

Preserve:

```text
Mobile Phone → Wi-Fi → PC/System Receiver → Classroom PC/OS
```

No ESP32, Arduino, Bluetooth controller, external intermediary hardware, or unnecessary cloud infrastructure. Mobile is teacher controller; PC performs OS-level actions.

## 6. Mobile Responsibilities

Mobile primarily handles UI, IMU input, Trackpad touch, teacher controls, room selection, OTP input, file selection, screen capture, semantic command requests, and connection/session state. Do not move PC-only OS duties into mobile.

## 7. PC Responsibilities

PC receiver handles network reception, session authorization, mouse/keyboard/presentation actions, local approved application actions, file receiving, Screen Hosting reception, Custom Command validation, tray/background operation, local configuration, and error reporting.

## 8. Security Rules

AI must validate incoming requests, require authorized session where specified, respect OTP, reject unauthorized commands, validate message/command/parameters, enforce Custom Command allowlists, validate file paths, prevent path traversal, avoid unrestricted shell execution, and avoid sensitive logging.

Never create hidden authentication bypasses for convenience.

## 9. QR and OTP Rules

```text
QR = Room Identification
OTP = Temporary Authentication
```

QR contains only room number and never becomes authentication credential. OTP is temporary/expiring according to authoritative pairing/security requirements.

## 10. UI Implementation Rules

Follow `UI_UX.md`, `UI_COMPONENTS.md`, and `DESIGN.md`. Preserve glassmorphism and a simple main mobile screen exposing connected room/system, Gyro Mouse, Trackpad, Laser Pointer, Calibrate, Presentation Controls, Share File, Screen Hosting, and Custom Commands.

Advanced settings belong under three-dot menu. Do not redesign unrelated screens while implementing feature.

## 11. Component Reuse

Before creating UI component: search existing components, check `UI_COMPONENTS.md`, reuse/extend if suitable, and create new component only when genuinely needed. Avoid duplicate buttons, cards, dialogs, settings rows, status/loading/error indicators.

## 12. Networking Rules

Follow `NETWORK.md`, `API_PROTOCOL.md`, and `SECURITY.md`. Reuse established protocol mechanisms. Separate control communication from File Sharing and Screen Hosting streaming. Do not send continuous sensor/video data through ordinary control-only mechanism.

## 13. Mouse Rules

Support Gyro/IMU Air Mouse and laptop-style Trackpad. Follow `MOUSE.md` and `IMU.md`. Gyro supports XY/XZ/YZ, inversion, sensitivity, acceleration, dead zone, filtering, drift compensation, stationary detection, and calibration.

Do not discard all small movement merely to reduce jitter; preserve intentional fine movement.

## 14. Calibration Rules

Follow:

```text
Calibrate → Stable Surface → Countdown → Sample Sensors
→ Estimate Bias → Store Calibration → Success/Failure
```

Never silently calibrate. Handle failure clearly.

## 15. Presentation Rules

Use only semantic intent:

`NEXT_SLIDE`, `PREVIOUS_SLIDE`, `START_PRESENTATION`, `END_PRESENTATION`, `BLANK_SCREEN`, `VOLUME_UP`, `VOLUME_DOWN`, `MUTE`, `PLAY_PAUSE`.

Mobile requests semantic action; PC maps to local approved behavior. Do not expose arbitrary keyboard/shell presentation API.

## 16. Screen Hosting Rules

Screen Hosting is real-time mobile mirroring:

```text
Mobile Capture → Encode → Wi-Fi Streaming → PC Receiver → Decode → Display
```

Preserve low latency, orientation support, start/stop, fullscreen, quality/FPS where supported, interruption recovery, and normal phone use. Do not implement as file sharing, screenshots, periodic image upload, or normal control messages.

## 17. File Sharing Rules

Support Phone→PC and PC→Phone with selection, progress, completion, cancellation, configurable destination, and safe interruption handling. Do not implement file-sharing history. Validate name/path/size/storage/state.

## 18. Custom Command Rules

```text
Mobile Request → Command ID → PC Validation → Allowlist
→ Approved Local Action → Result
```

Never provide unrestricted arbitrary command execution. Every command has approved known action.

## 19. State Management Rules

Distinguish UI state, application state, session state, persistent configuration, and temporary runtime state. Represent network state explicitly. UI reacts to state; it does not directly drive low-level network/OS operations. Avoid unjustified global mutable state.

## 20. Error Handling Rules

Every feature handles success, loading, timeout, failure, cancellation, disconnect/reconnect, invalid input, and unsupported operation. Errors are actionable and understandable; do not swallow errors or expose raw stack traces to normal users.

## 21. Offline and Network Failure

Assume classroom Wi-Fi may fail. Handle unavailable PC/mobile, network changes, packet loss, receiver restart, and session expiry safely. Never assume permanent connectivity.

## 22. Performance Rules

Prioritize low cursor/Trackpad/presentation/Screen Hosting latency, low idle CPU/battery, and smooth UI. Avoid excessive polling/background work/logging/allocations/rendering and blocking UI thread.

## 23. Platform Integration Rules

Follow `PLATFORM_INTEGRATION.md`. Keep OS-specific behavior behind abstractions. Do not spread OS code across unrelated logic.

## 24. Dependency Rules

Before dependency: read `DEPENDENCIES.md`, check existing dependency/platform capability, assess security/license, choose smallest appropriate dependency, document it, and never silently introduce major framework.

## 25. Configuration Rules

Follow `CONFIGURATION.md`. Do not hardcode configurable settings. Use safe defaults, validate configuration, and preserve configuration across normal updates where appropriate.

## 26. Project Structure Rules

Follow `PROJECT_STRUCTURE.md`. Keep UI, state, networking, feature logic, OS integration, storage, and security separated. Do not create random top-level folders.

## 27. Testing Rules

For each significant feature:

```text
Implement → Unit Test → Integration Test → Feature Test → Error-Path Test
```

Test happy path, invalid input, disconnect, reconnect, permission failure, unsupported operation, and security rejection. UI rendering alone is not completion.

## 28. Incremental Implementation

Work in small verifiable steps:

```text
Understand → Plan → Implement → Test → Inspect → Fix → Continue
```

Avoid dozens of unrelated file changes for a small feature.

## 29. Existing Code Preservation

Understand current behavior before modification. Make focused changes, preserve working functionality, remove obsolete code only when safe, and never replace entire project because another implementation seems easier.

## 30. Debugging Rules

Reproduce failure, identify failing layer, inspect sanitized logs/errors, determine root cause, make smallest fix, retest affected feature and related behavior. Do not hide errors using broad exception handling.

## 31. Documentation Synchronization

When implementation changes documented behavior, identify/update affected documentation. Do not leave contradictory specifications. If human/product decision is needed, mark **TBD** rather than inventing it.

## 32. AI Communication Rules

Progress reports clearly distinguish:

- Implemented.
- Tested.
- Not tested.
- Known limitations.
- TBD decisions.
- Required human decisions.

Never claim a feature works without testing.

## 33. Definition of Done

Feature is complete only when requirements implemented, UI matches design, components reused, error states exist, security respected, relevant tests pass, no obvious regression exists, and documentation remains consistent.

## 34. Forbidden Shortcuts

AI must not invent requirements/security mechanisms, remove authentication for testing, add arbitrary shell execution/cloud services/ESP32/Arduino/Bluetooth intermediary hardware, hardcode secrets/room-specific behavior, ignore errors, fake functionality as complete, replace working architecture without reason, or hide main-screen primary controls in advanced settings.

## 35. Decision Handling

For ambiguity:

```text
Check documentation → architecture → feature specification → implementation constraints → TBD
```

Do not make architectural decision merely because convenient to code.

## 36. Final AI Coding Checklist

### Architecture

- [ ] Mobile → Wi-Fi → PC; no intermediary hardware.
- [ ] Responsibilities separated.

### Pairing

- [ ] Room number, QR registration, discovery, OTP, session handling.

### Controls

- [ ] Gyro Mouse, Trackpad, Laser, Calibration, Presentation, File Sharing, Screen Hosting, Custom Commands.

### UI

- [ ] Glassmorphism, minimal main screen, three-dot advanced settings, consistency, accessibility, loading/error states.

### Security

- [ ] Authentication, authorization, validation, allowlist, safe file handling, no unrestricted shell execution.

### Engineering

- [ ] Reusable components, dependency discipline, configuration, tests, recovery, documentation consistency.

## AI Coding Summary

The coding AI implements the documented Air Mouse product—not its own invented version. It makes the smallest safe change necessary, preserves architecture and working behavior, validates security boundaries, tests real behavior, and marks unresolved decisions **TBD** rather than silently deciding them.

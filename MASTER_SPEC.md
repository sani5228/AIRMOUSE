# Air Mouse for College Teaching — Master Specification

## 1. Project Identity

**Project Name:** Air Mouse for College Teaching.

Air Mouse lets a teacher use a mobile phone as a wireless classroom controller for a PC.

```text
Teacher Mobile Phone
       │ Wi-Fi
       ▼
Air Mouse PC Receiver
       │
       ▼
Classroom PC / Operating System
```

There is no ESP32, Arduino, Bluetooth controller, or intermediary hardware. Mobile phone is the primary teacher controller; PC receiver performs authorized OS-level actions.

## 2. Problem Statement

Teachers often remain near keyboard/mouse or carry separate presentation remote. Air Mouse combines Air Mouse, Trackpad, presentation remote, Laser Pointer, File Sharing, Screen Hosting, and Custom Commands in the teacher’s phone for local classroom PC control.

## 3. Product Goals

- Simple classroom operation.
- Low-latency control.
- Reliable local Wi-Fi operation.
- Minimal setup.
- Secure room pairing.
- Clean mobile UI.
- Unobtrusive PC receiver.
- Reusable modular architecture.
- Practical college prototype.

## 4. Non-Goals

Air Mouse is not a general remote-desktop system, unrestricted remote shell, cloud-dependent control platform, IoT hardware project, enterprise device-management platform, or replacement for full remote-desktop solution.

## 5. Target Users

Primary user: teacher/lecturer. Deployment: college classroom. Primary hardware: teacher smartphone, classroom PC, and permitted local Wi-Fi network.

## 6. Core User Journey

```text
Install PC Receiver → Enter Room Number → Generate QR
→ Teacher Scans QR → Mobile Stores Room Number
→ Mobile Discovers Reachable Systems → Teacher Selects Room
→ PC Displays Temporary OTP → Teacher Enters OTP
→ PC Verifies OTP → Authorized Session → Teaching Controls
```

QR equals room identification/registration. OTP equals temporary authentication. QR contains only room number.

## 7. Multi-Classroom Model

Multiple classroom PCs may exist. Teacher identifies systems by room number, not technical network metadata. Handle unavailable rooms, duplicate room numbers, network changes, multiple discovered PCs, PC restart, session disconnect, and OTP expiry safely. Concurrency policy is **TBD**.

## 8. Main Mobile Screen

Main screen remains simple and exposes connected room/system, Gyro Mouse, Trackpad, Laser Pointer, Calibrate, Presentation Controls, Share File, Screen Hosting, and Custom Commands. Advanced settings belong under three-dot menu.

## 9. Mobile Features

### Gyro Mouse

Phone IMU controls PC cursor with XY/XZ/YZ axes, independent inversion, individual/overall sensitivity, acceleration, dead zone, filtering, drift compensation, stationary detection, calibration, lock/pause, and optional haptics. See `MOUSE.md` and `IMU.md`.

### Trackpad

Laptop-style phone touchpad: move→cursor; tap→left click; two-finger tap→right click; double tap→double click; two-finger vertical/horizontal scroll; tap/hold+move→drag.

### Laser Pointer

Start, stop, pointer movement, and visible active state.

### Calibration

```text
Calibrate → Stable Surface → Countdown → Sensor Sampling
→ Bias Estimation → Store Calibration → Success/Failure
```

### Presentation Controls

Direct main-screen semantic controls: Next, Previous, Start, End, Blank Screen, Volume Up/Down, Mute, Play/Pause. No arbitrary keyboard/shell control.

### File Sharing

Phone→PC and PC→phone with selection, progress, completion, cancellation, safe configurable destination. No file-transfer history.

### Screen Hosting

Full real-time mobile mirroring:

```text
Mobile Capture → Encoding → Wi-Fi Streaming → PC Reception → Decoding → Display
```

Low latency, start/stop, orientation, fullscreen, quality/FPS where supported, recovery, and continued phone use. It is not file sharing.

### Custom Commands

Examples: Open Calculator, Open Browser, Open Teaching Folder.

```text
Mobile → Semantic Command ID → PC Validation → Allowlist
→ Approved Local Action → Result
```

No unrestricted arbitrary shell execution.

## 10. PC Receiver

PC runs primarily in system tray/background with compact glassmorphism UI. First-time setup is Welcome → Room Number → Generate QR → Display QR.

Normal state shows Air Mouse, room number, system status, connected device, mouse/presentation/hosting/transfer status, Disconnect, and Settings/three-dot menu. During teaching it stays out of way.

## 11. PC Notifications

Compact notifications include Air Mouse connected, file received, Screen Hosting started, disconnected, and important errors. Never notify for every continuous movement update.

## 12. UI/UX

Both applications use consistent glassmorphism: translucent surfaces, blur, subtle borders, rounded corners, soft shadows, clear typography/icons, restrained animations, light/dark themes, and readable accessible contrast. See `UI_UX.md` and `UI_COMPONENTS.md`.

## 13. Advanced Settings

Three-dot settings groups: Mouse Settings, IMU, Presentation, Screen Hosting, Systems, File Sharing, Custom Commands, Security, General/App Settings. Do not overload teaching main screen.

## 14. Architecture

```text
Mobile UI → Mobile Feature Logic → Network/Session Layer → Wi-Fi
→ PC Receiver Network Layer → Command/Streaming/File Processing → OS Integration
```

Keep feature logic, network, security, UI, storage, and OS integration separated. See `ARCHITECTURE.md` and `DESIGN.md`.

## 15. Communication Model

Three logical categories:

| Category | Use |
|---|---|
| Control | Mouse, clicks, presentation, laser, Custom Commands, session events. |
| File Transfer | Phone→PC and PC→phone file data/lifecycle. |
| Screen Hosting | Continuous real-time screen stream. |

Do not force all traffic into one mechanism. Protocol/transport details are in `API_PROTOCOL.md` and `NETWORK.md`; unselected technical choices are **TBD**.

## 16. Security Model

```text
Room Identification → Discovery → Room Selection → Temporary OTP
→ OTP Verification → Authorized Session → Commands / Files / Streaming
```

Requirements: authenticate sessions, authorize requests, validate incoming data/command IDs, allowlist Custom Commands, validate file paths, prevent path traversal, avoid unrestricted shell execution, minimize sensitive storage/logging, and handle expiry/disconnect. See `SECURITY.md` and `PAIRING.md`.

## 17. Configuration

Configuration divides into mobile preferences, PC configuration, persistent configuration, session state, temporary runtime state, and calibration data. Advanced settings remain outside main teaching surface. See `CONFIGURATION.md` and `DATA_MODEL.md`.

## 18. Performance Priorities

Prioritize low cursor/presentation/Screen Hosting latency, responsive Trackpad, reliable commands, low idle resource usage, low mobile battery use, and smooth UI. Do not sacrifice reliability for feature count. Numeric targets are **TBD** until testing establishes them.

## 19. Reliability

Handle PC/mobile unavailable, network interruption, receiver restart, session/OTP expiry, invalid OTP, file/stream interruption, unsupported commands, and permission failure. Every important operation has understandable failure/recovery state.

## 20. Privacy

Minimize data. Do not unnecessarily store personal information, OTP values, private file contents, sensitive session data, recordings, screen frames, or transfer history. Room number is primary user-facing identity.

## 21. Development Philosophy

```text
Simple → Modular → Testable → Secure → Extensible
```

Implement smallest solution that correctly meets requirements. Do not overengineer.

## 22. Documentation Map

| Document | Purpose |
|---|---|
| `README.md` | Project overview |
| `PRD.md` | Product requirements |
| `ARCHITECTURE.md` | System architecture |
| `FEATURES.md` | Feature overview |
| `UI_UX.md` | Visual/UX requirements |
| `UI_COMPONENTS.md` | Reusable UI components |
| `PAIRING.md` | Room pairing/authentication |
| `MOUSE.md` | Mouse behavior |
| `IMU.md` | Sensor processing |
| `PRESENTATION.md` | Presentation controls |
| `SCREEN_MIRRORING.md` | Screen Hosting |
| `FILE_SHARING.md` | File transfer |
| `CUSTOM_COMMANDS.md` | Approved local commands |
| `NETWORK.md` | Networking |
| `SECURITY.md` | Security |
| `API_PROTOCOL.md` | Communication contract |
| `DATA_MODEL.md` | Data structures/storage |
| `ERROR_HANDLING.md` | Error behavior |
| `TESTING.md` | Testing |
| `DEVELOPMENT_PHASES.md` | Implementation phases |
| `DESIGN.md` | Component design |
| `TECH_STACK.md` | Technology decisions |
| `PROJECT_STRUCTURE.md` | Repository structure |
| `SETUP.md` | Development setup |
| `IMPLEMENTATION_RULES.md` | Coding constraints |
| `COMMAND_CATALOG.md` | Semantic command definitions |
| `PLATFORM_INTEGRATION.md` | OS/platform integration |
| `CONFIGURATION.md` | Settings/configuration |
| `DEPENDENCIES.md` | Dependencies |
| `BUILD_DEPLOYMENT.md` | Build/deployment |
| `AI_CODING_GUIDE.md` | AI implementation guide |

## 23. AI Implementation Rules

AI reads this document first, then relevant detailed specification; inspects repository; reuses components/services; follows `TECH_STACK.md`, `ARCHITECTURE.md`, `IMPLEMENTATION_RULES.md`, and `AI_CODING_GUIDE.md`; never invents requirements; marks unknown choices **TBD**; never removes security; never adds unrestricted shell/cloud/intermediary hardware; tests changes; and synchronizes documentation.

## 24. Definition of Done

Project completion requires:

- Room setup, QR registration, discovery, OTP, authorized sessions.
- Gyro Mouse, calibration, noise/drift handling, Trackpad.
- Laser, Presentation Controls, File Sharing, Screen Hosting, Custom Commands.
- Clean consistent glassmorphism mobile/PC UI; primary controls direct; advanced settings organized.
- Reliable networking, enforced security, error handling, persistent configuration, documented deployment, and passing relevant tests.

## 25. Final AI Rule

This is high-level product contract. Detailed document governs specific feature behavior. On conflict: identify → apply hierarchy → follow authority → do not invent compromise → mark unresolved **TBD**.

## Master Specification Summary

**Product:** local Wi-Fi classroom mobile controller for PC.

**Architecture:** Mobile → Wi-Fi → validated PC system-tray receiver → OS/application actions.

**Core features:** Gyro Mouse, Trackpad, Laser, Calibration, Presentation Controls, File Sharing, Screen Hosting, and allowlisted Custom Commands.

**Security:** room QR registration, temporary OTP authentication, authorized sessions, PC validation/allowlists/path safety, no unrestricted shell.

**UI:** simple teacher main screen, advanced three-dot settings, consistent readable glassmorphism.

**Engineering:** modular, local-first, low-latency, testable, safe failure/recovery, no unnecessary stack/cloud/hardware decisions.

**AI coding philosophy:** implement documented product with smallest safe tested change; preserve working behavior; mark unresolved decisions **TBD** rather than inventing them.

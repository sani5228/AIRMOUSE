# Air Mouse for College Teaching — Setup Guide

## 1. Purpose

This guide provides practical setup guidance for developers and AI coding agents. Air Mouse consists of a mobile teacher controller and PC receiver communicating directly over local Wi-Fi. The receiver runs primarily in the PC system tray/background.

The project has no ESP32, Arduino, Bluetooth controller, intermediary hardware, or required cloud service. Follow `TECH_STACK.md` for technology decisions and `PROJECT_STRUCTURE.md` for repository organization. All unselected technical items are **TBD**.

## 2. Prerequisites

| Category | Required | Optional | TBD |
|---|---|---|---|
| Operating systems | Mobile and PC platforms supported by final project scope | Additional platform test environments | Supported OS versions/platforms |
| Languages/runtimes | Those selected in `TECH_STACK.md` | Additional tooling runtimes | Language/runtime/version |
| SDKs/package managers/build tools | Those required by selected stack | IDE integrations | Exact tools/versions |
| IDE/editor | Any tool compatible with selected stack | AI-assisted/editor plugins | Preferred tool |
| Mobile device | Wi-Fi; gyro/IMU for Gyro Mouse; camera for QR; screen capture for hosting | Emulator for UI-only work | Device/version matrix |
| PC | Local network, display, capability to run receiver | Projector/secondary display | Minimum CPU/RAM/OS |
| Wi-Fi | Mobile and PC reachable on permitted local network | Controlled test network | Network policy requirements |

Do not invent versions, commands, dependencies, ports, credentials, services, or secrets.

## 3. Repository Setup

1. Obtain the repository using the organization’s approved version-control workflow. Exact command is **TBD** until tool selection.
2. Open the repository root.
3. Verify expected structure: `mobile/`, `pc/`, `shared/`, `tests/`, root specifications, and optional `docs/`/`scripts/`.
4. Read `TECH_STACK.md`, `PROJECT_STRUCTURE.md`, `ARCHITECTURE.md`, `DESIGN.md`, and relevant feature documents.
5. Install only dependencies approved by selected stack and dependency policy.
6. Prepare mobile and PC development configurations without real credentials, fixed addresses, OTPs, or production paths.
7. Start mobile/PC projects using exact selected-stack commands, which are **TBD**.

## 4. Environment Setup

| Variable/Setting | Purpose | Required | Example | Secret? |
|---|---|---:|---|---:|
| Application mode | Separates development/testing/classroom behavior | Yes | `development`, `test`, `classroom` | No |
| Room number | PC classroom identity | PC setup | `Room 204` | No |
| Logging level | Controls sanitized diagnostic detail | Optional | `debug` in development | No |
| Test file destination | Safe PC receive location for test transfers | Test environment | `<configured test folder>` | No |
| Network/session settings | Final selected implementation configuration | TBD | `<TBD>` | Depends |
| Secrets/keys/certificates | Only if later security design requires them | TBD | Never commit real values | Yes |

Development configuration enables safe diagnostics and controlled test rooms. Test configuration supports fault testing. Classroom configuration uses temporary OTP, safe local paths, and production-safe logging. Never hardcode addresses, credentials, OTPs, or secret values.

## 5. Mobile Development Setup

After mobile stack selection:

1. Install selected dependencies using selected package/build tool (**TBD**).
2. Connect a physical phone or configure emulator where supported.
3. Start a development build using selected-stack command (**TBD**).
4. Grant required permissions when prompted: sensor/IMU, camera, file access where applicable, and screen capture when hosting is tested.
5. Verify Wi-Fi availability and local storage availability where applicable.
6. Verify gyroscope/IMU availability before Gyro Mouse testing.
7. Verify camera/QR scanning and screen capture capabilities on real device.
8. Use selected debugging/logging tools (**TBD**).

Emulators can help UI/state development but real device testing is required for sensors, Wi-Fi discovery, QR camera use, haptics, and screen hosting.

## 6. PC Receiver Setup

After PC stack selection:

1. Install approved PC receiver dependencies (**TBD**).
2. Start receiver using selected command/package (**TBD**).
3. On first run, enter classroom room number.
4. Generate/display QR containing room number only.
5. Confirm receiver enters available/waiting state.
6. Confirm tray/background operation and compact receiver panel.
7. Open receiver UI through tray when needed.
8. Stop/restart receiver through selected PC runtime UI or command (**TBD**).

Expected first run: welcome → room number → QR display → room registration wait → normal tray receiver state.

## 7. Local Development Pairing

Developer flow:

```text
Start PC Receiver
→ Configure Room Number
→ Generate QR
→ Start Mobile App
→ Scan QR
→ Discover PC
→ Select Room
→ PC Displays Temporary OTP
→ Enter OTP
→ Session Established
```

| Step | Expected behavior |
|---|---|
| Start PC | Receiver runs and is locally discoverable when network permits. |
| Configure room | Room number is saved on PC and shown in setup/tray UI. |
| Generate QR | QR contains only room number. |
| Scan QR | Mobile saves room registration; scanning alone grants no control. |
| Discover/select | Mobile lists reachable system by room number. |
| OTP | PC visibly displays temporary code; mobile enters it. |
| Session | Valid OTP creates authorized session and enables controls. |

Wrong OTP is rejected; expired OTP requires a new current PC code; PC unavailable returns mobile to discovery/unavailable state; mobile disconnect/Wi-Fi change stops controls safely; PC restart invalidates old session. See `PAIRING.md`.

## 8. Running the Full System

1. Start PC receiver and verify room configuration/tray status.
2. Start mobile app on permitted local Wi-Fi.
3. Discover/select room and complete OTP authentication.
4. Test Gyro Mouse and calibration.
5. Test Trackpad movement, click, scroll, and drag.
6. Test direct main-screen Presentation Controls.
7. Test Laser Pointer start/move/stop.
8. Test file upload/download using safe test files.
9. Test Screen Hosting start, rotation, display, and stop.
10. Test enabled approved Custom Commands.
11. Explicitly disconnect, then verify reconnect behavior.

Run features only through authorized session.

## 9. Development Test Environment

Use a safe local test setup:

- Distinct test room number, not an active classroom identity.
- Real test mobile device with required sensors/camera/capture support.
- Test PC with receiver and safe configured file destination.
- Non-sensitive small/large test files.
- Non-critical test presentation/media content.
- Only harmless approved Custom Commands, such as opening a configured test folder/application.
- Local Wi-Fi where direct peer communication is permitted.

No external server is required for normal development testing.

## 10. Sensor Testing Checklist

- [ ] Gyroscope/IMU availability detected.
- [ ] Sensor readings received and invalid readings handled safely.
- [ ] Calibration starts from mobile main screen.
- [ ] Recalibration and reset work.
- [ ] Stationary detection reduces drift.
- [ ] Noise filtering preserves intentional small movement.
- [ ] XY, XZ, YZ axis selection works.
- [ ] X/Y/Z inversion works.
- [ ] Individual/overall sensitivity works.
- [ ] Acceleration and dead zone work.
- [ ] Trackpad fallback works if sensor unavailable.

See `IMU.md` and `MOUSE.md`.

## 11. Network Testing

Verify:

- Mobile and PC are on same permitted local Wi-Fi.
- Discovery finds PC and shows room number correctly.
- OTP authentication creates authorized session.
- Control commands reach PC only while session valid.
- Disconnects are detected and stale controls stop.
- Reconnection/rediscovery works according to policy.

Do not prescribe a specific network diagnostic command until selected stack supports it. See `NETWORK.md`.

## 12. Screen Hosting Testing

Test:

- Start and Stop.
- Full mobile screen display.
- Portrait, landscape, and rotation.
- Fullscreen PC display where supported.
- Quality/FPS/resolution controls where implemented.
- Network interruption and recovery.
- Continued normal mobile interaction while hosting.

Verify PC clears active display/status after stop/failure/session end. See `SCREEN_MIRRORING.md`.

## 13. File Sharing Testing

Test Phone → PC and PC → Phone with:

- Small and large files.
- Multiple files where implemented.
- Progress and completion.
- Cancellation and interruption.
- Invalid destination, insufficient storage, and permissions.

**File-sharing history is not implemented.** See `FILE_SHARING.md`.

## 14. Custom Command Testing

Test valid enabled command, unknown command, disabled command, unauthorized command, invalid parameters, execution failure, and successful result feedback.

Verify arbitrary shell/terminal commands, scripts, and arbitrary executable paths cannot be executed through normal interface unless a future separately controlled design explicitly permits them. See `CUSTOM_COMMANDS.md` and `SECURITY.md`.

## 15. Debugging and Troubleshooting

| Problem | Possible causes and actions |
|---|---|
| Mobile does not discover PC | Wi-Fi mismatch, receiver stopped, discovery unavailable, firewall/isolation/policy. Verify network/receiver and retry. |
| OTP fails | Expired OTP, wrong room, incorrect code, session mismatch. Obtain current PC OTP and retry. |
| Mouse does not move | Sensor unavailable, calibration issue, mouse locked, session disconnected, PC input integration issue. Try Trackpad and inspect sanitized diagnostics. |
| Screen Hosting fails | Capture permission, encoder/decoder, bandwidth, PC display, unsupported configuration. Check UI error and retry/stop. |
| File transfer fails | Storage, permission, interruption, invalid destination/path. Check safe destination and retry. |

Do not assume a specific cause without verified diagnostics. See `ERROR_HANDLING.md`.

## 16. Logs and Diagnostics

Log locations, activation, and export are **TBD** until stack selection. Development logs should include timestamp, component, sanitized error code, operation, severity, and safe recovery outcome.

Never log OTP values, passwords, authentication/session secrets, unnecessary personal data, sensitive file contents, screen contents, or raw internal stack traces in teacher-facing output. See `ERROR_HANDLING.md` and `SECURITY.md`.

## 17. Build Instructions

| Deliverable | Development build | Release build/package | Output artifact |
|---|---|---|---|
| Mobile | Command/tool is TBD after stack selection | Signing/package process TBD | Mobile application package TBD |
| PC receiver | Command/tool is TBD after stack selection | Installer/package process TBD | PC executable/package/installer TBD |

See `BUILD_DEPLOYMENT.md` if created. Do not claim build output until selected technology defines it.

## 18. Clean and Rebuild

Exact clean/rebuild commands are **TBD**. Selected tooling must support:

- Cleaning generated build artifacts only.
- Reinstalling approved dependencies.
- Rebuilding mobile application.
- Rebuilding PC receiver.
- Resetting development configuration without deleting source files or unintended user/classroom configuration.

Back up/avoid production configuration before destructive reset operations.

## 19. Reset Development Environment

| Item | Development reset | Classroom/production caution |
|---|---|---|
| Saved room | Remove test room registration through systems settings | Do not remove active classroom registration unintentionally. |
| Pairing/session | Disconnect/clear temporary session state | Requires re-authentication; room remains saved. |
| IMU calibration | Reset/recalibrate from settings | Inform teacher if calibration changes. |
| App settings | Reset local test preferences | Preserve intended classroom settings where possible. |
| PC configuration | Reset test room/configuration through PC settings | Do not erase room/allowlist/destination without authorization. |

Exact reset storage mechanism is **TBD**.

## 20. Common Setup Mistakes

- Mobile and PC are on different networks.
- Required camera/sensor/capture/file permissions were denied.
- Firewall, guest Wi-Fi, AP isolation, or college policy blocks local peer communication.
- Incorrect room configuration or duplicate room number.
- Expired OTP used after waiting too long.
- Screen capture tested without permission/support.
- Phone lacks supported sensor hardware for Gyro Mouse.
- PC receive location lacks permission/storage.
- Testing Custom Commands with unsafe/unapproved action assumptions.

## 21. Setup Verification Checklist

- [ ] Approved dependencies installed after stack selection.
- [ ] Mobile builds/runs.
- [ ] PC receiver builds/runs.
- [ ] PC starts successfully in background/tray mode.
- [ ] Room number configured.
- [ ] QR generated with room number only.
- [ ] Mobile scans QR and saves room.
- [ ] PC discovered.
- [ ] OTP authentication succeeds.
- [ ] Gyro Mouse works.
- [ ] Trackpad works.
- [ ] Presentation Controls work.
- [ ] Laser Pointer works.
- [ ] File upload works.
- [ ] File download works.
- [ ] Screen Hosting works.
- [ ] Custom Commands work.
- [ ] Disconnect/reconnect works.

## 22. AI Coding Agent Setup

The AI coding agent must:

1. Read `MASTER_SPEC.md` if it exists.
2. Read `TECH_STACK.md`, `PROJECT_STRUCTURE.md`, `ARCHITECTURE.md`, and `DESIGN.md`.
3. Inspect actual repository before modifying anything.
4. Install only approved dependencies.
5. Run relevant project component after significant changes.
6. Run relevant tests.
7. Fix errors before moving to next feature.
8. Never assume feature works only because code compiles.
9. Avoid overwriting working functionality unnecessarily.
10. Keep documentation synchronized with major implementation/structural changes.

## 23. Setup Status

| Item | Status | Notes |
|---|---|---|
| Mobile environment | TBD | Framework/language/build tools not finalized. |
| PC environment | TBD | Receiver stack/OS scope not finalized. |
| Networking | TBD | Local Wi-Fi architecture finalized; technology/protocol details TBD. |
| Screen Hosting | TBD | Required feature; capture/stream/decode technology TBD. |
| Packaging | TBD | Mobile/PC package/installer choices TBD. |

## Setup Summary

Minimum local setup is: choose/record the technology stack, prepare a supported mobile device and PC on permitted local Wi-Fi, start the PC receiver, configure room number and QR, scan/save room in mobile app, discover/select the PC, enter temporary PC OTP, and then test authorized teaching features. Exact installation/build commands remain **TBD** until the stack is finalized.```    

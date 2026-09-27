# Air Mouse for College Teaching — Technology Stack and Technical Constraints

## 1. Purpose

This document defines technology-selection constraints for coding AI and project contributors. It does not invent a stack. Technologies not explicitly finalized by existing project requirements are **TBD**.

Air Mouse consists of a teacher mobile application and a classroom-PC receiver that communicates directly over local Wi-Fi. The PC runs primarily in system tray/background. There is no ESP32, Arduino, Bluetooth controller, intermediary hardware, mandatory cloud service, or enterprise infrastructure requirement.

## 2. Technology Selection Principles

Select technology only when it satisfies project needs:

- Cross-platform practicality appropriate to supported devices.
- Low latency for mouse, pointer, and presentation controls.
- Easy development and maintainability for a college prototype.
- Open-source/free preference where practical.
- Minimal cost and no unnecessary cloud dependency.
- Good mobile sensor, camera, file, permission, haptic, and screen-capture access.
- Reliable local Wi-Fi communication.
- Simple packaging/distribution for phone and classroom PC.
- Strong security/session-validation support without over-engineering.
- Suitability for AI-assisted/vibe coding: clear ecosystem, documentation, debugging, and testing support.
- Limited dependency surface and maintainable license terms.

## 3. Mobile Technology Stack

| Area | Technology | Version | Status | Reason / decision needed |
|---|---|---|---|---|
| Mobile framework | TBD | TBD | TBD | Choose based on target mobile platforms, sensor/capture access, UI productivity, packaging, and maintainability. |
| Programming language | TBD | TBD | TBD | Select with chosen framework and team/AI-assisted development capability. |
| UI framework | TBD | TBD | TBD | Must implement responsive glassmorphism, light/dark themes, accessible touch UI. |
| Sensor/IMU access | TBD | TBD | TBD | Must provide timestamped gyro/IMU inputs and availability/permission handling. |
| Local networking | TBD | TBD | TBD | Must support local Wi-Fi discovery, session/control, file, and streaming paths. |
| QR scanning | TBD | TBD | TBD | Must scan room-number-only QR safely. |
| Local persistence | TBD | TBD | TBD | Must store saved rooms/settings/calibration with minimal local data. |
| File selection | TBD | TBD | TBD | Must provide user-selected file access for transfers. |
| Screen capture | TBD | TBD | TBD | Must permit full live screen hosting with required permission flow. |
| Screen encoding | TBD | TBD | TBD | Choose for low latency, device support, resource use, hardware acceleration. |
| Audio/haptic feedback | TBD | TBD | TBD | Haptics optional; must not disrupt teaching. |
| Permissions | TBD | TBD | TBD | Must support sensor/camera/file/capture permissions and safe denial states. |
| Build system | TBD | TBD | TBD | Choose with mobile framework and distribution requirements. |
| Testing | TBD | TBD | TBD | Support unit, integration, device, sensor, UI, and network testing from `TESTING.md`. |

## 4. PC Receiver Technology Stack

| Area | Technology | Version | Status | Reason / decision needed |
|---|---|---|---|---|
| Programming language | TBD | TBD | TBD | Choose for supported desktop environments, native integration, packaging, and maintenance. |
| Desktop UI | TBD | TBD | TBD | Must support compact setup/tray/settings and glassmorphism visual requirements. |
| System tray integration | TBD | TBD | TBD | Must support unobtrusive background receiver operation. |
| Local networking | TBD | TBD | TBD | Must support discovery, sessions, controls, files, streams, and recovery. |
| Discovery | TBD | TBD | TBD | Choose local-network approach compatible with college networks; do not require cloud. |
| Authentication | TBD | TBD | TBD | Must implement temporary OTP/session validation; security mechanics remain TBD. |
| Mouse control | TBD | TBD | TBD | Requires supported OS input integration through platform adapter. |
| Keyboard/presentation control | TBD | TBD | TBD | Must map semantic command to supported local behavior. |
| Volume/media control | TBD | TBD | TBD | Platform/application capability dependent. |
| Presentation integration | TBD | TBD | TBD | Choose supported-app strategy; mobile remains application-independent. |
| Screen-stream decoding | TBD | TBD | TBD | Choose for low latency, display, hardware acceleration where available. |
| File system access | TBD | TBD | TBD | Must enforce configured/allowed paths and safe filename handling. |
| QR generation | TBD | TBD | TBD | Must generate QR containing room number only. |
| Configuration persistence | TBD | TBD | TBD | Lightweight local configuration is expected; database necessity is TBD. |
| Notifications | TBD | TBD | TBD | Compact non-intrusive teaching-time notifications. |
| Logging | TBD | TBD | TBD | Sanitized local diagnostics, no secrets/content. |
| Packaging | TBD | TBD | TBD | Simple classroom-PC installation and updates. |

Cross-platform components should remain independent of OS-specific input, window, media, filesystem, tray, and display adapters. Exact supported PC platforms are **TBD**.

## 5. Networking Technology

Confirmed requirements:

- Direct Mobile ↔ PC communication over local Wi-Fi.
- Local discovery, room identification, OTP-authenticated session, reconnect behavior, and multiple systems.
- Separate logical paths for low-latency control, file transfer, and real-time screen streaming.
- No required cloud server for normal operation.

| Communication area | Technology | Status | Decision criteria |
|---|---|---|---|
| Discovery | TBD | TBD | Local reachability, college-network compatibility, room-number result, low setup burden, privacy. |
| Authentication/session | TBD | TBD | Temporary OTP, replay/stale protection, session lifecycle, security, simplicity. |
| Control communication | TBD | TBD | Low latency, small messages, current-state movement handling, duplicate prevention. |
| File-transfer communication | TBD | TBD | Reliable delivery, progress/cancel, path safety, coexistence with controls. |
| Screen-stream communication | TBD | TBD | Low latency, streaming suitability, adaptation, device support, resource use. |
| Reconnection/health | TBD | TBD | Safe recovery, no stale session/action replay, low idle traffic. |

Final protocol/ports/serialization are not selected. See `NETWORK.md` and `API_PROTOCOL.md`.

## 6. Screen Hosting Technology

Required pipeline:

```text
Mobile screen capture → encoding → Wi-Fi streaming → PC decoding → PC display
```

| Requirement | Technology status | Selection criteria |
|---|---|---|
| Mobile capture | TBD | Platform permission/support, full-screen capture, normal phone use while active. |
| Encoder/decoder | TBD | Low latency, compatible hardware/software fallback, CPU/GPU/battery efficiency. |
| Stream transport | TBD | Real-time delivery, network adaptation, security/session authorization. |
| Display/fullscreen | TBD | Portrait/landscape, scaling, multi-display support where available. |
| Quality/FPS/resolution controls | TBD | Classroom responsiveness over unnecessary video quality. |
| Hardware acceleration | TBD | Use where available; graceful fallback/clear limitation where unavailable. |

Do not treat stream frames as ordinary control messages. See `SCREEN_MIRRORING.md`.

## 7. OS Integration

| Capability | Required behavior | Platform technology/API | Status |
|---|---|---|---|
| Mouse movement | Authorized relative cursor movement | TBD | TBD |
| Left/right/double click | Authorized semantic mouse actions | TBD | TBD |
| Drag/scroll | Safe drag lifecycle and vertical/horizontal scrolling | TBD | TBD |
| Keyboard actions | Only supported semantic presentation behavior | TBD | TBD |
| Volume/media | Supported PC audio/media semantic actions | TBD | TBD |
| Presentation controls | Map semantic commands to local application behavior | TBD | TBD |
| Window/application actions | PC-side supported focus/launch behavior | TBD | TBD |
| System tray | Background receiver and compact panel | TBD | TBD |
| Notifications | Compact, non-intrusive events | TBD | TBD |
| Screen display | Mirrored stream display/fullscreen | TBD | TBD |

Platform-specific integration must stay behind adapters as described in `DESIGN.md`. Do not allow platform APIs to bypass session/allowlist validation.

## 8. Security Technologies

| Requirement | Current direction | Status |
|---|---|---|
| OTP | Temporary PC-displayed OTP authenticates session | Confirmed requirement; generation details TBD |
| Session authentication | PC validates session before privileged action | Confirmed requirement; technical mechanism TBD |
| Secure communication | Protect local communication as appropriate | TBD; choose based on threat model/platform support |
| Command validation | PC validates semantic requests | Confirmed requirement |
| File validation | Validate session, filename, paths, destination, storage/permissions | Confirmed requirement |
| Custom Commands | PC-side allowlist; no arbitrary shell/scripts/paths | Confirmed requirement |
| Replay/stale protection | Avoid stale/duplicate privileged operations | Confirmed requirement; mechanism TBD |
| Secure local storage | Protect sensitive local configuration where needed | TBD; determine retained sensitive data and platform capability |

Do not introduce third-party authentication providers or unnecessary identity infrastructure. See `SECURITY.md`.

## 9. Storage and Persistence

Lightweight local persistence appears sufficient unless implementation testing establishes otherwise. A database is not currently required and remains **TBD**.

| Side | Persistent data |
|---|---|
| Mobile | Saved room numbers, UI/settings, IMU calibration, mouse/gesture preferences, hosting preferences, session-related information only as required. |
| PC | Room number, receiver configuration, approved Custom Commands, file destination configuration, presentation/hosting preferences, security configuration. |

OTP, active sessions, transfers, stream state, and discovery results are temporary. No persistent file-transfer history, screen recordings, or stored mirrored frames. See `DATA_MODEL.md`.

## 10. Development Tools

No IDE/editor, version-control host, package manager, build tool, test tool, linter, formatter, debugger, emulator, or device tool has been finalized.

| Category | Selected tool | Status | Decision criteria |
|---|---|---|---|
| IDE/editor | TBD | TBD | Team familiarity, mobile/PC debugging, AI assistance. |
| Version control | TBD | TBD | Collaboration, review, backup, availability. |
| Package management | TBD | TBD | Chosen language/framework ecosystem. |
| Build tools | TBD | TBD | Reproducible mobile/PC builds. |
| Testing tools | TBD | TBD | Unit/integration/device/network capability. |
| Lint/format | TBD | TBD | Consistency and automated quality checks. |
| Debugging | TBD | TBD | Sensor/network/session/PC integration diagnosis. |
| Device/emulator tools | TBD | TBD | Real-device testing remains mandatory for sensors/capture/Wi-Fi. |

## 11. Dependency Policy

- Prefer actively maintained, open-source/free dependencies where practical.
- Avoid unnecessary dependencies and duplicate libraries solving same problem.
- Check license compatibility.
- Pin versions where appropriate after selection.
- Document purpose and justification for each important dependency.
- Remove unused dependencies.
- Avoid dependencies that force unnecessary cloud, analytics, authentication, or hardware requirements.
- Review security and maintenance risk before adding dependencies.

`DEPENDENCIES.md` should record selected dependencies once technologies are finalized.

## 12. Hardware Requirements

| Side | Requirement | Status |
|---|---|---|
| Mobile | Wi-Fi connectivity | Required |
| Mobile | Gyroscope/IMU for Gyro Mouse | Required for gyro feature; Trackpad fallback if unavailable |
| Mobile | Camera for QR scanning | Required for QR registration flow; alternative/manual behavior TBD |
| Mobile | Screen capture capability | Required for Screen Hosting where supported |
| Mobile | Basic performance sufficient for sensor/UI/streaming | Exact specification TBD |
| PC | Local Wi-Fi/network connectivity | Required |
| PC | Display/projector-capable output | Required for classroom usage/hosting display |
| PC | Basic CPU/RAM for receiver/control | Exact specification TBD |
| PC | Stream decoding/display capability | Required for Screen Hosting where supported |
| PC | Supported desktop OS/environment | TBD |

No ESP32, Arduino, Bluetooth controller, or intermediary hardware is permitted.

## 13. Performance Requirements

| Area | Target | Status |
|---|---|---|
| Cursor responsiveness | Low perceived latency, smooth updates | Numeric target TBD |
| Sensor processing latency | Preserve responsive small movement | Numeric target TBD |
| Presentation command latency | Timely classroom response | Numeric target TBD |
| Pointer latency | Low-latency classroom pointer motion | Numeric target TBD |
| File throughput | Reliable practical local Wi-Fi transfer | Numeric target TBD |
| Screen-hosting latency/FPS | Responsive stream with stable practical playback | Numeric target TBD |
| Mobile CPU/battery | Suitable for classroom session | Target TBD |
| PC CPU/RAM | Low idle overhead; practical stream overhead | Target TBD |

Values must be measured and established through `TESTING.md`, not invented.

## 14. Packaging and Distribution

Required deliverables:

- Mobile application package appropriate to selected mobile platform.
- PC receiver installer/package appropriate to selected PC platform.
- Simple classroom-PC room setup/configuration flow.
- Clear runtime dependency handling if selected stack needs it.
- Setup, troubleshooting, and known-limitations documentation.

Packaging technology, signing, update model, installer format, and runtime dependencies are **TBD**. Installation must be simple enough for classroom-PC deployment. See `BUILD_DEPLOYMENT.md` if created.

## 15. Development vs Production Configuration

Separate development, testing, and final classroom deployment configuration.

- Do not hardcode development network addresses, credentials, OTPs, paths, mock rooms, or debug behavior into production builds.
- Development can use controlled mock services/adapters only behind explicit configuration.
- Testing configuration supports fault injection and diagnostics without exposing sensitive values.
- Classroom deployment uses room configuration, temporary OTP, safe local paths, and production-safe logging.
- Configuration mechanism is **TBD**.

## 16. Technology Decision Log

| Decision | Selected Technology | Alternatives Considered | Reason | Status |
|---|---|---|---|---|
| System topology | Direct mobile → local Wi-Fi → PC receiver | Intermediary hardware/cloud | Confirmed project constraint | Finalized |
| Teacher-facing identity | Room number | Network/device identifiers | Simple classroom selection | Finalized |
| QR payload | Room number only | QR credentials/network metadata | Registration only; QR is not authentication | Finalized |
| Authentication concept | Temporary PC-displayed OTP | QR-only/no authentication | Authorized session before control | Finalized requirement |
| Mobile framework | TBD | TBD | Must select before implementation | TBD |
| PC receiver stack | TBD | TBD | Must select before implementation | TBD |
| Discovery technology | TBD | TBD | Local network/college compatibility | TBD |
| Control protocol/transport | TBD | TBD | Low latency/security/reliability | TBD |
| File transfer transport | TBD | TBD | Reliable delivery/cancellation | TBD |
| Screen streaming technology | TBD | TBD | Low latency/device support/resource use | TBD |
| Persistence mechanism | TBD | TBD | Minimal local settings/configuration | TBD |
| OS integration approach | TBD | TBD | Supported desktop targets and packaging | TBD |

## 17. Final Stack Summary

| Area | Stack summary |
|---|---|
| Mobile stack | **TBD**: framework, language, UI, sensor/camera/file/capture APIs, local persistence, build/test stack. |
| PC stack | **TBD**: language, desktop/tray UI, networking, platform adapters, decoder/display, packaging. |
| Communication stack | Confirmed direct local Wi-Fi architecture; discovery/session/control/file/stream technologies **TBD**. |
| Screen-hosting stack | Required capture → encode → stream → decode → display design; exact implementation **TBD**. |
| Storage | Minimal local persistence; database necessity **TBD**; no persistent transfer history/recording. |
| Testing | Requirements defined in `TESTING.md`; tools/frameworks **TBD**. |
| Build/distribution | Mobile package and PC receiver installer required; tooling and packaging formats **TBD**. |

## 18. Technology Stack Status

### Finalized decisions

- Direct mobile-phone-to-PC communication over local Wi-Fi.
- Mobile phone is controller; PC receiver runs mainly in system tray/background.
- No ESP32, Arduino, Bluetooth controller, intermediary hardware, or required cloud service.
- Room number is teacher-facing system identity.
- QR contains only room number and is not authentication.
- Temporary PC-displayed OTP establishes authorized session.
- Control uses semantic commands; Custom Commands use PC-side allowlist.
- No unrestricted shell/terminal execution by default.
- No persistent file-transfer history, screen recording, or mirrored-frame storage.

### TBD decisions

- Supported mobile and PC platforms.
- Mobile/PC languages, frameworks, UI toolkits, build systems, and packaging.
- Discovery, control, transfer, and streaming transports/protocols/ports/serialization.
- OTP/session/replay/secure-communication technical mechanisms.
- Screen capture/encoding/decoding/streaming implementation and hardware acceleration fallback.
- Platform adapters for mouse, presentation, volume/media, tray, rendering, and display.
- Local persistence mechanism and secure-storage details.
- Development/test/lint/debugging tools and dependency list.
- Numeric performance targets and minimum hardware specifications.

### Decisions required before implementation begins

1. Supported mobile platform scope and supported PC desktop environment scope.
2. Mobile application framework/language and PC receiver framework/language.
3. Local discovery and authorized-session/control communication design.
4. Screen Hosting feasibility path: capture, encode, stream, decode, display.
5. PC OS-integration adapter strategy for mouse/presentation/media/tray functions.
6. Local persistence/configuration approach.
7. Build, packaging, device testing, and dependency governance approach.

All selected technologies must be recorded with version, rationale, license considerations, supported-platform impact, and dependency justification before becoming implementation constraints.

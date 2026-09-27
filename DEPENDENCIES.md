# Air Mouse for College Teaching — Dependencies

## 1. Purpose

Dependency management keeps the mobile app, PC receiver, networking, IMU input, mouse/Trackpad control, presentation control, Screen Hosting, file sharing, Custom Commands, QR handling, testing, building, and packaging maintainable, secure, and practical.

No library, framework, version, service, protocol, port, or API has been finalized by this document. Unselected dependencies are **TBD**. This document is a tracking/control document for developers and coding AIs.

## 2. Dependency Principles

- Prefer mature, maintained, lightweight dependencies.
- Avoid unnecessary libraries and duplicate solutions.
- Prefer open-source/free solutions where practical.
- Avoid subscription-dependent services and unnecessary cloud services.
- Keep mobile and PC dependencies separate.
- Use platform APIs where they adequately meet needs.
- Pin/constrain versions appropriately after selection.
- Review license compatibility and attribution obligations.
- Remove unused/abandoned/high-risk dependencies.
- Add dependency only with clear documented purpose.

## 3. Dependency Categories

- Mobile
- PC Receiver
- Shared/Protocol
- Build Tools
- Testing
- Development Tools
- Optional Dependencies
- External Services
- Platform APIs

## 4. Mobile Dependencies

| Dependency | Purpose | Required/Optional | Version | License | Notes |
|---|---|---|---|---|---|
| Mobile UI framework | Main UI/glassmorphism/settings/navigation | Required | TBD | TBD | Final framework not selected. |
| IMU/sensor access | Gyro/accelerometer/timestamped motion input | Required for Gyro Mouse | TBD | TBD | Prefer platform capability where possible. |
| Touch input | Trackpad gestures | Required | TBD | TBD | May be framework/platform built-in. |
| Local networking | Discovery/session/control/file/stream paths | Required | TBD | TBD | Technology TBD; see `NETWORK.md`. |
| QR scanning | Room registration scan | Required | TBD | TBD | QR contains room number only. |
| QR display | Display when mobile flow needs it, if any | Optional | TBD | TBD | PC QR generation is primary requirement. |
| File selection/access | User-selected transfer files | Required | TBD | TBD | Avoid unrestricted storage access. |
| Screen capture | Full mobile Screen Hosting | Required where feature supported | TBD | TBD | Permission/capability dependent. |
| Video encoding/streaming | Low-latency Screen Hosting | Required where feature supported | TBD | TBD | Technology/codec TBD. |
| Local persistence | Saved rooms/settings/calibration | Required | TBD | TBD | Lightweight local storage expected. |
| Haptic feedback | Optional state feedback | Optional | TBD | TBD | Must not disrupt teaching. |
| Testing support | Unit/UI/device/network testing | Required | TBD | TBD | Tooling TBD. |

## 5. PC Receiver Dependencies

| Dependency | Purpose | Required/Optional | Version | License | Notes |
|---|---|---|---|---|---|
| Desktop UI | Setup/tray/settings/compact panels | Required | TBD | TBD | Final desktop framework TBD. |
| System tray integration | Background receiver/tray panel | Required | TBD | TBD | May use platform capability. |
| Network discovery | Local PC discoverability | Required | TBD | TBD | Discovery mechanism TBD. |
| Network communication | Sessions/control/file/stream coordination | Required | TBD | TBD | Protocol/transport TBD. |
| OS mouse control | Cursor/click/drag/scroll | Required | TBD | TBD | Platform-specific adapter. |
| Keyboard/presentation control | Approved semantic presentation behavior | Required | TBD | TBD | No arbitrary remote keys. |
| Media/volume control | Volume/mute/play/pause where supported | Required where capability exists | TBD | TBD | Platform-specific. |
| File system access | Safe transfer destination/source handling | Required | TBD | TBD | Path validation mandatory. |
| Screen stream receiver/decoder | Screen Hosting display | Required where feature supported | TBD | TBD | Codec/decoder TBD. |
| QR generation | Room-number QR | Required | TBD | TBD | QR contains only room number. |
| Notifications | Compact important events | Required | TBD | TBD | May use platform capability. |
| Local configuration | Room/settings/allowlist/destination persistence | Required | TBD | TBD | Database not required by default. |
| Custom Command execution | Approved allowlisted local actions | Required | TBD | TBD | No unrestricted shell execution. |
| Logging | Sanitized diagnostics | Required | TBD | TBD | Never log OTP/secrets/content. |
| Packaging | Receiver installer/package | Required for release | TBD | TBD | Tooling TBD. |

## 6. Platform APIs

Prefer platform APIs where adequate; capability differs by platform.

| Capability | Platform capability/dependency status |
|---|---|
| Mobile sensors | TBD; use supported mobile sensor capability. |
| Mobile screen capture | TBD; platform permission/capability required. |
| Mobile file picker/filesystem | TBD; use safe user-selected file capability. |
| PC mouse/keyboard | TBD; platform-specific input adapter required. |
| PC audio/media | TBD; platform-specific capability. |
| PC window/application interaction | TBD; only approved semantic actions. |
| System tray | TBD; platform-specific capability. |
| Desktop notification | TBD; platform-specific capability. |
| Screen display/fullscreen | TBD; platform-specific capability. |

## 7. OS-Specific Dependencies

Target platforms are not finalized in `TECH_STACK.md`.

| Feature | Windows | Linux | macOS | Notes |
|---|---|---|---|---|
| Mouse/keyboard control | TBD | TBD | TBD | Requires target-specific adapter. |
| Audio/media | TBD | TBD | TBD | Capability varies. |
| Presentation applications | TBD | TBD | TBD | Supported-app strategy TBD. |
| Screen capture/display | TBD | TBD | TBD | Stream display capability varies. |
| Notifications/tray | TBD | TBD | TBD | Native integration varies. |
| Application launching | TBD | TBD | TBD | Only approved allowlisted actions. |

Do not claim platform support until selected/tested.

## 8. Network Dependencies

| Layer | Required responsibility | Dependency/capability status |
|---|---|---|
| Local Wi-Fi | Direct mobile↔PC reachability | Required; underlying platform networking. |
| Discovery | Reachable room systems | TBD. |
| Authentication/session | OTP and authorized session | TBD technical mechanism. |
| Control | Low-latency semantic commands | TBD transport/library. |
| File transfer | Reliable file data/progress/cancel | TBD transport/library. |
| Screen Hosting | Continuous real-time stream | TBD streaming technology. |
| Recovery | Health/reconnect/stale-state handling | TBD mechanism. |

Do not duplicate `NETWORK.md`/`API_PROTOCOL.md`; these documents define behavior and contract.

## 9. Screen Hosting Dependencies

```text
Mobile Capture → Video Encoding → Network Streaming → PC Reception → Video Decoding → Display
```

| Category | Requirement | Status |
|---|---|---|
| Capture | Full mobile screen capture with permission flow | TBD |
| Encoding | Low latency, hardware acceleration where available | TBD |
| Streaming | Real-time local Wi-Fi path separate from controls | TBD |
| Reception | Authorized stream reception | TBD |
| Decoding | Efficient PC decode/software fallback where practical | TBD |
| Display | Portrait/landscape/scaling/fullscreen | TBD |
| Adaptation | Quality/FPS/bitrate response to conditions | TBD |

Do not choose codec/library until platform and performance testing justify it. See `SCREEN_MIRRORING.md`.

## 10. QR Dependencies

Required categories are PC QR generation and mobile QR scanning. QR encodes only room number, handles invalid/unsupported data safely, and is never authentication. Specific QR libraries are **TBD**.

## 11. File Sharing Dependencies

File sharing needs user file selection, controlled file reading, transfer data path, destination handling, progress reporting, storage/permission checks, and partial-transfer cleanup. No dependency may introduce persistent file-transfer history. Specific dependencies are **TBD**. See `FILE_SHARING.md`.

## 12. Custom Command Dependencies

Custom Commands require PC-side allowlist definition, approved local action mapping, safe parameter validation, status reporting, and platform-specific approved application/folder/resource handling.

Do not add unrestricted shell/terminal execution dependency. Platform differences for opening approved actions are **TBD**. See `CUSTOM_COMMANDS.md`.

## 13. Development Dependencies

Developer tools for installing dependencies, running mobile/PC apps, tests, packages, linting/static analysis, formatting, and debugging are **TBD** until stack selection. Exact setup belongs in `SETUP.md` after selection.

## 14. Testing Dependencies

| Test type | Required/Optional | Dependency status |
|---|---|---|
| Unit tests | Required | TBD |
| Integration tests | Required | TBD |
| UI tests | Required where supported | TBD |
| Network tests | Required | TBD |
| Sensor-processing tests | Required | TBD |
| File-transfer tests | Required | TBD |
| Screen Hosting tests | Required | TBD |
| Optional diagnostics/profiling | Optional | TBD |

Testing requirements are authoritative in `TESTING.md`.

## 15. External Services

| Service | Purpose | Required | Online dependency | Alternative |
|---|---|---:|---:|---|
| External/cloud service | None for normal operation | No | No | Direct local Wi-Fi architecture. |

No paid API, subscription service, cloud infrastructure, authentication provider, or hosted server is required by current requirements.

## 16. Dependency Security

- Review dependency vulnerabilities and updates.
- Use lockfiles where selected ecosystem supports them.
- Evaluate untrusted package/supply-chain risk.
- Avoid abandoned packages and excessive permissions.
- Keep dependency set minimal.
- Check license/security before upgrade/addition.
- Do not add package merely to bypass session/path/allowlist security.

## 17. Licensing

Selected dependencies must have compatible licenses, required attribution/notices, and acceptable redistribution obligations. Do not claim a license for unselected dependency. License review is required before release.

## 18. Dependency Versions

After selection, record minimum supported version where required, recommended version, lock/pin policy, upgrade procedure, compatibility tests, and reason for version constraint. Do not invent versions now.

## 19. Dependency Removal

Remove dependency when unused, replaced by platform API, duplicative, too large, performance-harmful, security-risky, abandoned, or maintenance-problematic. Update dependency inventory and lock/configuration files after removal.

## 20. Offline/No-Internet Operation

Core classroom workflow should operate without internet: local discovery where network permits, OTP session, mouse/Trackpad, presentation, laser, files, and Screen Hosting use local Wi-Fi. Internet-dependent exceptions are currently none; any future exception is **TBD** and requires explicit approval.

## 21. Dependency Inventory

| Dependency | Component | Required | Platform | Purpose | Status |
|---|---|---:|---|---|---|
| Mobile framework | Mobile | Yes | TBD | App/UI/runtime | TBD |
| PC framework | PC | Yes | TBD | Receiver/tray/setup UI | TBD |
| Sensor capability | Mobile | Gyro feature | Supported mobile | IMU input | TBD |
| QR generation/scanning | Both | Yes | Mobile/PC | Room registration | TBD |
| Local networking | Both | Yes | Mobile/PC | Discovery/session/control | TBD |
| File transfer mechanism | Both | Yes | Mobile/PC | Authorized file data | TBD |
| Stream capture/encode/decode | Both | Screen Hosting | Mobile/PC | Real-time mirroring | TBD |
| OS integration adapter | PC | Yes | Selected PC OS | Mouse/presentation/media/tray | TBD |
| Local persistence | Both | Yes | Mobile/PC | Settings/configuration | TBD |
| Test stack | Both | Yes | Mobile/PC | Required test coverage | TBD |
| External service | N/A | No | N/A | Not needed | Confirmed absent |

## 22. AI Coding Rules

AI must read this document before adding package; check existing dependencies/framework capabilities; avoid unnecessary dependencies; never silently add large framework; record approved additions; check platform/license/security compatibility; prefer platform APIs when appropriate; keep optional items separate; never add cloud merely for convenience; and never add ESP32, Arduino, Bluetooth-controller hardware, or intermediary hardware.

Architecture remains:

```text
Mobile Phone → Wi-Fi → PC/System Receiver
```

## 23. TBD Decisions

- Final mobile framework/language/UI stack.
- Final PC receiver framework/language/tray stack.
- Networking/discovery/session/control libraries.
- QR generation/scanning library.
- Screen capture/encoding/streaming/decoding technology.
- PC OS integration libraries/adapters.
- Testing/linting/formatting/debugging stack.
- Packaging/installer tools.
- Exact versions and lockfile strategy.
- Supported desktop/mobile platform matrix.

## Dependency Summary

Air Mouse should stay lightweight, local-network-first, maintainable, secure, and practical. Dependencies are selected only when they directly enable documented features and meet maintenance/license/security requirements. No external service is required for normal classroom operation, and all technology choices currently not finalized remain **TBD**.

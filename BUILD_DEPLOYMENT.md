# Air Mouse for College Teaching — Build and Deployment

## 1. Purpose and Scope

This document defines how Air Mouse is built, packaged, installed, configured, updated, and prepared for development, testing, and classroom deployment. It covers the mobile application and PC system-tray receiver.

```text
Mobile Application → Local Wi-Fi → PC/System Receiver → Classroom PC
```

There is no ESP32, Arduino, Bluetooth controller, intermediary hardware, or required cloud deployment. Technology/build/package choices are **TBD** unless finalized in `TECH_STACK.md`. See `DEPENDENCIES.md`, `SETUP.md`, `PROJECT_STRUCTURE.md`, `NETWORK.md`, `SECURITY.md`, and `CONFIGURATION.md`.

## 2. Deployment Architecture

```text
Teacher Phone
   │ Local Wi-Fi
   ▼
Classroom PC Receiver
   ├─ Mouse Control
   ├─ Presentation Control
   ├─ Laser Pointer
   ├─ File Sharing
   ├─ Screen Hosting
   └─ Custom Commands
```

Mobile is primarily controller/UI. PC receiver is responsible for validated local OS-level actions, safe file handling, display/rendering, and system-tray/background operation.

## 3. Build Environments

| Environment | Purpose | Configuration | Debugging | Logging |
|---|---|---|---|---|
| Development | Feature implementation and local debugging | Test room/configuration; no production secrets | Enabled as appropriate | Sanitized diagnostic logging |
| Testing | Repeatable integration/network/device validation | Controlled test rooms/files/commands | Fault/recovery testing | Sanitized test logs |
| Classroom/release | Teaching deployment | Room configuration and production-safe settings | No unnecessary debug controls | Minimal sanitized operational logs |

No cloud infrastructure is required.

## 4. Mobile Build

Conceptual process:

```text
Source Code → Dependency Resolution → Build → Tests → Package → Install/Distribute
```

Mobile process must support development, testing, and release builds. Selected build system, commands, package format, signing approach, versioning, and distribution are **TBD**.

Release build must declare/use only required capabilities: local networking, sensor/IMU access for Gyro Mouse, camera for QR scanning, screen capture for Screen Hosting where supported, file access through safe user selection where applicable, and optional haptics. Permission behavior follows least privilege.

## 5. PC Receiver Build

Conceptual process:

```text
Source Code → Dependency Resolution → Build → Tests → Package → Install
→ Configure Room → Run in Background/System Tray
```

PC build supports debug and release variants. Packaging, installer, runtime dependencies, OS requirements, startup behavior, and permission model are **TBD**. Release receiver must support first-time room setup, QR generation, local discovery/session handling, compact tray UI, and authorized PC behavior.

## 6. First-Time PC Setup

1. Install PC receiver.
2. Launch receiver.
3. Enter classroom room number.
4. Validate/persist room configuration.
5. Generate QR containing only room number.
6. Display QR.
7. Teacher scans QR in mobile app.
8. Mobile stores room number.
9. PC becomes available for local discovery.
10. Teacher selects room.
11. PC displays temporary OTP.
12. Teacher enters OTP in mobile app.
13. Valid OTP establishes authorized session.

QR is room identification/registration only. OTP performs temporary authentication.

## 7. Classroom Deployment Checklist

- [ ] Receiver installed on classroom PC.
- [ ] Correct room number configured.
- [ ] Receiver permitted to run in background/system tray.
- [ ] Permitted local network available.
- [ ] Required permissions granted.
- [ ] QR available for room registration.
- [ ] Receiver status verified.
- [ ] Mobile app installed.
- [ ] Pairing/OTP tested.
- [ ] Mouse/Trackpad tested.
- [ ] Presentation Controls tested.
- [ ] Laser Pointer tested.
- [ ] File Sharing tested if enabled.
- [ ] Screen Hosting tested if enabled.
- [ ] Custom Commands tested if enabled.

## 8. Installation and Uninstallation

Installation, upgrade, repair/reinstall, and uninstallation mechanisms are **TBD**. Normal upgrade should preserve required room/settings/allowlist/destination configuration where compatible. Uninstallation must distinguish removing software from removing stored configuration/security data.

Configuration removal should be explicit; it may remove room setup, Custom Commands, file destination, preferences, calibration, or local session state depending on selected scope. Never silently remove classroom configuration during ordinary repair/upgrade.

## 9. Versioning

| Component | Version | Compatibility requirement |
|---|---|---|
| Mobile application | TBD | Must handle PC protocol/capability compatibility safely. |
| PC receiver | TBD | Must handle mobile protocol/capability compatibility safely. |
| Protocol | TBD | Unknown/unsupported versions fail safely. |

Versioning scheme and compatibility policy are **TBD**. Do not invent version numbers.

## 10. Mobile ↔ PC Compatibility

When mobile/PC versions differ, use protocol/capability checks where implemented. New mobile with old PC, old mobile with new PC, protocol mismatch, feature-only-on-one-side, and unsupported command must produce clear unsupported/update/retry behavior—not silent misbehavior.

Exact compatibility negotiation strategy is **TBD**. See `API_PROTOCOL.md`.

## 11. Network Deployment

Classroom deployment requires mobile and PC to be reachable on same permitted local Wi-Fi/network. Validate discovery, room visibility, OTP connection, interruption detection, and reconnection behavior. Guest Wi-Fi, AP isolation, VLANs, firewall restrictions, or policy may block peer communication; show teacher-friendly unavailable guidance.

Do not invent ports/protocols. See `NETWORK.md`.

## 12. Screen Hosting Deployment

```text
Mobile Capture → Encoding → Wi-Fi Streaming → PC Receiver → Decoding → Display
```

Deployment must consider capture permission/support, encoding/decoding capability, hardware acceleration where available, fallback/limitations, fullscreen, portrait/landscape, network interruption, CPU/GPU/battery/network usage. Streaming technology is **TBD**. See `SCREEN_MIRRORING.md`.

## 13. Permissions

| Permission/Capability | Mobile | PC | Purpose | Required? |
|---|---|---|---|---:|
| Local networking | Yes | Yes | Discovery/session/control/file/stream | Yes |
| Motion sensors | Gyro feature | No | Gyro Mouse | Feature-dependent |
| Camera | QR scanning | No | Room registration | Required for scan flow |
| File selection/access | Transfer feature | Safe filesystem access | File sharing | Feature-dependent |
| Screen capture/display | Screen Hosting capture | Screen display/decoder | Mirroring | Feature-dependent |
| OS input/presentation/media | No | Yes | Authorized PC actions | Feature/platform-dependent |
| Tray/background/notifications | No | Yes | Receiver operation/feedback | PC requirement |

Exact permission names and platform behavior are **TBD**. Follow least privilege.

## 14. Security During Deployment

Deployment must preserve OTP authentication, session authorization, Custom Command allowlisting, file-transfer validation, and local network protections. Never ship development authentication bypasses in classroom/release builds. Isolate demo/test modes from production behavior. Do not ship secrets, test credentials, fixed OTPs, or unsafe command paths.

## 15. Configuration Deployment

Classroom configuration establishes room number, receiver startup/tray behavior, file destination, approved Custom Commands, notification preferences, Screen Hosting display preferences, and relevant mobile settings/calibration. Follow `CONFIGURATION.md`; do not hardcode settings into build artifacts.

## 16. Logging and Diagnostics

Deployment logs may diagnose startup, network, pairing/authentication, file transfer, Screen Hosting, and unsupported command failures. Do not log OTP values, sensitive authentication/session data, private file contents, screen content, or unnecessary personal information. Logging mechanism/location is **TBD**.

## 17. Updates

Expected process:

```text
New Version → Compatibility Check → Preserve/Backup Required Configuration
→ Install Update → Validate → Resume Classroom Use
```

Apply separately to mobile and PC receiver. Preserve compatible configuration, verify protocol compatibility, and provide rollback strategy. Update distribution/rollback mechanism is **TBD**.

## 18. Rollback and Recovery

| Problem | Practical recovery |
|---|---|
| Installation fails | Restore prior known working package/configuration where available; exact mechanism TBD. |
| Receiver fails to start | Check sanitized logs/configuration; repair/reinstall without deleting config unless chosen. |
| Version incompatibility | Use compatible versions or rollback; do not bypass protocol safety. |
| Invalid configuration | Reset affected configuration safely; retain unrelated data. |
| Network failure | Verify permitted Wi-Fi/receiver/discovery; retry/reconnect. |
| Screen Hosting failure | Stop/restart hosting; verify permissions/capability/network/display. |

Recovery must be practical immediately before or during classroom use.

## 19. Packaging

| Component | Package Type | Target Platform | Status |
|---|---|---|---|
| Mobile app | TBD | TBD | TBD |
| PC receiver | TBD | TBD | TBD |
| PC installer/package | TBD | TBD | TBD |

Do not invent formats.

## 20. Automated Build / CI

Automated build/test pipeline may be added after tooling selection:

```text
Code → Lint → Unit Tests → Integration Tests → Build → Package → Release Artifact
```

CI provider, commands, artifact storage, signing, and release workflow are **TBD**. Automation is optional for prototype but tests/build verification remain required.

## 21. Release Checklist

### Code and dependencies

- [ ] Build succeeds.
- [ ] Relevant tests pass.
- [ ] No known blocking errors.
- [ ] Dependencies reviewed/licensed/verified.

### Mobile

- [ ] Pairing works.
- [ ] Gyro Mouse and Trackpad work.
- [ ] Presentation Controls work.
- [ ] Laser Pointer works.
- [ ] File Sharing works.
- [ ] Screen Hosting works.
- [ ] Custom Commands work.

### PC

- [ ] Receiver starts and tray UI works.
- [ ] Room configuration/QR generation work.
- [ ] OTP authentication works.
- [ ] OS control works.
- [ ] File receiving works.
- [ ] Screen Hosting display works.
- [ ] Notifications work.

### Network and security

- [ ] Discovery/reconnection works.
- [ ] Unavailable systems are handled clearly.
- [ ] Unauthorized requests are rejected.
- [ ] Custom Commands remain allowlisted.

## 22. Classroom Smoke Test

Immediately before lecture:

1. Start PC receiver and verify room number.
2. Open mobile app and select classroom.
3. Complete OTP authentication.
4. Move cursor and test Trackpad.
5. Test Next/Previous slide.
6. Test Laser Pointer.
7. Test file sharing if needed.
8. Test Screen Hosting if needed.
9. Verify disconnect/reconnect status.

Keep the smoke test brief; avoid large transfers/long stream tests immediately before teaching unless required.

## 23. Performance Considerations

Deployment must consider CPU/RAM, mobile battery, network bandwidth, Screen Hosting latency, startup time, and idle background resource usage. Numeric limits are **TBD** and established through `TESTING.md`. Prioritize responsive controls over maximum stream quality.

## 24. Developer vs Classroom Builds

| Build | Characteristics |
|---|---|
| Development | Safe developer diagnostics, controlled test configuration, debugging where appropriate; no production secret assumptions. |
| Classroom/release | Production configuration, secure authentication, minimal UI, stable tray/background operation, no unnecessary debug controls/bypasses. |

## 25. AI Coding Rules

AI must read this document before build/deployment changes; follow `TECH_STACK.md`/`DEPENDENCIES.md`; never add deployment/cloud infrastructure without requirement; keep development/classroom configuration separate; never ship authentication bypass; preserve room configuration during normal update; verify mobile/PC compatibility; and test complete classroom workflow after major changes.

## 26. Deployment Decision Log

| Decision | Status | Reason | Affected Components |
|---|---|---|---|
| Direct local Wi-Fi architecture | Confirmed | No cloud/intermediary hardware required | Mobile, PC, network |
| PC tray/background receiver | Confirmed | Unobtrusive classroom use | PC receiver |
| Mobile/PC package formats | TBD | Stack/platform not selected | Build/distribution |
| Signing/update/rollback mechanism | TBD | Tooling not selected | Release process |
| CI provider | TBD | No provider selected | Build/test automation |
| Streaming technology | TBD | Platform/performance decision pending | Screen Hosting |

## 27. Final Deployment Checklist

- [ ] Build.
- [ ] Test.
- [ ] Package.
- [ ] Install.
- [ ] Configure room.
- [ ] Pair mobile.
- [ ] Authenticate.
- [ ] Verify controls.
- [ ] Verify Screen Hosting.
- [ ] Verify File Sharing.
- [ ] Verify Custom Commands.
- [ ] Verify recovery.
- [ ] Release.

## Build & Deployment Summary

Air Mouse deployment remains simple: build/package mobile controller and PC tray receiver using selected stack, install locally, configure classroom room number and QR, pair by room discovery plus temporary OTP, validate core teaching controls, and retain safe update/rollback paths. No unnecessary cloud, hardware, or deployment complexity is required; all unselected build/package technologies remain **TBD**.

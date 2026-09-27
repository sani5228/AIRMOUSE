# Air Mouse for College Teaching — Configuration

## 1. Purpose and Scope

This document defines configuration/settings for mobile and PC receiver: ownership, exposure, persistence, runtime changes, validation, reset, synchronization, and recovery. It complements `UI_UX.md`, `UI_COMPONENTS.md`, `MOUSE.md`, `IMU.md`, `PRESENTATION.md`, `SCREEN_MIRRORING.md`, `FILE_SHARING.md`, `CUSTOM_COMMANDS.md`, `PAIRING.md`, `NETWORK.md`, `SECURITY.md`, `DATA_MODEL.md`, and `TECH_STACK.md`.

It does not select storage technology, framework, database, protocol, port, library, or numerical defaults. Unselected behavior is **TBD**.

## 2. Configuration Principles

Configuration is simple for teachers, safe by default, persistent where appropriate, easy to reset, clearly separated between preferences/system configuration, consistent across mobile/PC, resilient to invalid data, and suitable for a college prototype.

Normal users should not see unnecessary technical network/security settings. Room number, feature behavior, and classroom-relevant preferences use clear labels. Invalid settings never silently break feature behavior.

## 3. Configuration Ownership

| Configuration | Mobile | PC | Shared | Notes |
|---|---:|---:|---:|---|
| UI/theme preferences | Owns | Owns | No | Local appearance/notifications. |
| Room information | Saved room registration | Owns configured room number | No | Room is user-facing identity. |
| Paired/saved systems | Owns | No | No | QR registration only; not authorization. |
| Mouse/IMU settings | Owns | No | No | Mobile input behavior. |
| Presentation preferences | May own UI preferences | Owns local capability/mapping | Limited temporary context | Semantic commands remain shared contract. |
| Screen Hosting | Owns capture preferences | Owns display preferences | Temporary stream configuration | Do not auto-sync all choices. |
| File sharing | Owns selection preferences | Owns receive destination/policy | Temporary transfer context | No history. |
| Custom Commands | Displays/execution UI | Owns allowlist/action mapping | Definition availability as needed | PC is security authority. |
| Security/session | Temporary local session context | Owns validation/policy | Authorized temporary session | Never normal user preference. |
| Receiver/tray | No | Owns | No | PC-only behavior. |

## 4. Mobile Configuration

### Connection/System

Saved room number, selected room, connection/reconnect preference where supported, and local systems display preference. Mobile does not persist OTP as credential or expose technical network metadata.

### Mouse and IMU

Mobile owns selected mouse mode, overall/X/Y/Z sensitivity, axis mode, X/Y/Z inversion, acceleration, dead zone, filtering, drift compensation, stationary behavior where exposed, haptic feedback, Trackpad sensitivity/acceleration/gesture/scroll preferences, and mouse-lock behavior. Semantics are in `MOUSE.md` and `IMU.md`.

### Presentation

Mobile may store presentation UI/confirmation preferences where applicable. Semantic command definitions remain in `PRESENTATION.md` and `COMMAND_CATALOG.md`; PC owns OS/application execution mapping.

### Screen Hosting

Possible settings: quality, FPS, orientation/display behavior, and capture preferences where supported. Exact options/defaults are **TBD**. Changes may be local, temporary stream negotiation, or require restart depending on final implementation.

### File Sharing

Mobile may store transfer/file-selection preferences. PC destination configuration belongs to PC. No configuration creates transfer history.

### Custom Commands

Mobile displays command name, ID, enabled state, confirmation requirement, and safe availability received/configured through PC authority. Mobile must not become unrestricted command-execution interface.

## 5. PC Configuration

PC owns room number, receiver startup/tray behavior, receiver connection behavior, configured file download destination, screen-hosting display behavior, Custom Command allowlist/approved actions, security/session policy, and notification preferences.

Teacher-facing settings use simple room/status/feature terms. Administrator/developer settings may expose additional safe diagnostics/configuration only when needed. Exact separation is **TBD**.

## 6. Room Configuration

First run prompts for room number, validates it, persists it locally, and generates QR containing only room number. Changing room number updates PC teacher-facing identity and requires QR regeneration.

Existing mobile saved-room registrations may become stale after room change; exact migration/notification behavior is **TBD**. Changing room must not weaken OTP authentication. Duplicate room numbers require safe handling and must never silently select wrong PC. QR is registration only, never authentication.

## 7. Pairing and Session Configuration

```text
Room Number → Discovery → Room Selection → Temporary OTP
→ OTP Verification → Authorized Session
```

Configuration may support safe connection/reconnect preferences, but must not disable OTP, session authorization, expiry, or PC validation. OTP/session policy details are authoritative in `PAIRING.md` and `SECURITY.md`.

## 8. IMU Calibration Configuration

Calibration configuration includes stored calibration state, validity/version, reset, recalibration, and association with applicable local device/profile. Missing/invalid calibration prompts safe calibration/recalibration behavior; it must not silently cause uncontrolled cursor behavior.

Calibration is related to, but separate from, mouse sensitivity/axis/filter settings. Do not hardcode calibration values. See `IMU.md` and `DATA_MODEL.md`.

## 9. Defaults

| Setting | Default | User configurable | Owner | Notes |
|---|---|---:|---|---|
| Theme | TBD — determine through testing | Yes | Mobile/PC | Light/dark/system policy TBD. |
| Saved room | None initially | Yes | Mobile | Added by QR registration. |
| Room number | Required at PC setup | Yes | PC | Validation format TBD. |
| Axis mode | TBD — determine through testing | Yes | Mobile | XY/XZ/YZ. |
| Sensitivity | TBD — determine through testing | Yes | Mobile | Overall/per-axis. |
| Filtering/dead zone | TBD — determine through testing | Yes where exposed | Mobile | Preserve fine movement. |
| Screen quality/FPS | TBD — determine through testing | Yes where supported | Mobile/PC | Prioritize classroom responsiveness. |
| File destination | TBD | Yes | PC | Must be safe/allowed location. |
| Command enabled state | TBD | Yes through authorized management | PC | PC allowlist authoritative. |

Defaults favor reliable classroom experience, not maximum performance.

## 10. Persistence

| Category | Lifetime |
|---|---|
| Persistent preference | Survives relevant application restart: theme, mouse settings, saved rooms, PC room/configuration, commands, destination. |
| Calibration data | Persists while valid; cleared/reset/replaced through calibration lifecycle. |
| Session state | Temporary; expires/ends on disconnect/restart/policy event. |
| OTP | Temporary; never permanent credential. |
| Active transfer/stream | Temporary; cleared after completion/stop/failure/session end. |
| Discovery/connection health | Temporary; expires when stale/network changes. |

Storage implementation is **TBD**. See `DATA_MODEL.md`.

## 11. Configuration Validation

| Configuration | Validation |
|---|---|
| Room number | Non-empty and compliant with final room format; QR generation blocked if invalid. |
| Sensitivity/numeric input | Supported type/range; exact bounds TBD; invalid input rejected/reset safely. |
| Axis settings | Only XY/XZ/YZ and valid inversion state. |
| File destination | Valid allowed PC path; directory/permission/storage checked where needed. |
| Custom Command | Unique ID, valid name/state, approved action reference, permitted parameters only. |
| Screen Hosting options | Supported device/PC capability and valid compatible values. |
| Security/session policy | Cannot weaken required OTP/session/allowlist controls. |

Invalid/missing settings produce safe default, clear error, or settings correction path; never silent feature failure.

## 12. Reset and Recovery

| Action | Scope | Must not do |
|---|---|---|
| Reset individual setting | One preference/control | Delete unrelated settings. |
| Reset feature settings | Mouse/IMU/hosting/etc. group | Remove room/session unless explicitly selected. |
| Reset all preferences | Local application preferences | Delete PC setup or security state unintentionally. |
| Reset calibration | Calibration only | Remove mouse settings/saved rooms. |
| Remove saved room | Mobile registration | Reconfigure PC or delete other rooms. |
| Remove paired system/session | Temporary connection/registration as chosen | Delete unrelated feature configuration. |
| Restore PC defaults | Receiver configuration under explicit action | Silently erase allowed commands/destination/room. |

Reset configuration differs from removing security/session state. Exact confirmation requirements are **TBD**.

## 13. Runtime Changes

| Setting area | During active session |
|---|---|
| Mouse sensitivity/axis/inversion | May apply immediately where safe. |
| Calibration | Can start/recalibrate; Gyro state clearly pauses/updates. |
| Presentation controls | Must remain immediately usable. |
| Screen quality/FPS | May require stream restart/reconfiguration; exact behavior TBD. |
| File destination | Usually PC setting; active transfer impact TBD. |
| Custom Command state | PC validates current state; active request behavior TBD. |
| Network/security/session policy | May require reconnect/restart; exact behavior TBD. |
| Room number | PC configuration change likely requires rediscovery/QR regeneration; exact behavior TBD. |

## 14. Configuration Synchronization

Settings are local by default. Mobile settings remain mobile-local; PC receiver settings remain PC-local. Send temporary configuration only when a feature needs it, such as supported stream preferences or current command request context.

Do not automatically synchronize all settings. Synchronization mechanism/conflict policy is **TBD**.

## 15. Security Considerations

Configuration must not bypass OTP authentication, session authorization, Custom Command allowlisting, file-transfer authorization, path validation, or network security controls. Never store/expose sensitive session information unnecessarily. See `SECURITY.md`.

## 16. Configuration UI

Main screen contains frequent teaching controls only. Advanced settings are grouped under three-dot menu using `UI_COMPONENTS.md` reusable settings sections, toggle, slider, selector, number input, reset button, and information row. Do not create complex separate settings screens when simple reusable setting sections suffice.

## 17. Conceptual Configuration Structure

```text
Application Configuration
├── Connection
├── Mouse
├── IMU
├── Presentation
├── Screen Hosting
├── File Sharing
├── Custom Commands
├── Security
└── General
```

This is logical structure only; serialization/storage format is **TBD**.

## 18. Configuration Migration

Future updates handle new settings, removed settings, renamed settings, invalid old values, and missing values safely. Stored configuration has conceptual version information; compatible values migrate deliberately, invalid/incomplete values use safe defaults, and reset impact is communicated where visible. Migration/storage technology is **TBD**.

## 19. Configuration Logging

Log configuration creation/change/reset/validation failure using timestamp, component, safe setting category, error code, and outcome where useful. Do not log OTPs, authentication secrets, private file contents, unnecessary personal information, or sensitive paths. See `ERROR_HANDLING.md`.

## 20. Testing Requirements

Test default configuration, persistence across mobile/PC restart, invalid values, reset operations, room-number changes, QR regeneration, calibration reset, reconnect/session changes, unsupported settings, migration, and active-session runtime changes. See `TESTING.md`.

## 21. AI Coding Rules

AI must check this document before adding setting, avoid duplicate configuration systems, keep settings near feature ownership, separate security-sensitive configuration from normal preferences, validate input, provide safe defaults, never silently ignore invalid config, avoid unnecessary technical settings, reuse `UI_COMPONENTS.md`, follow feature authorities, never invent unspecified defaults, and mark unresolved choices **TBD**.

## 22. Configuration Inventory

| Category | Setting | Owner | Persistent | Runtime change | User visible | Default | Status |
|---|---|---|---:|---|---:|---|---|
| General | Theme | Mobile/PC | Yes | Yes | Yes | TBD | TBD |
| General | Notifications/haptics | Mobile/PC | Yes | Yes | Yes | TBD | TBD |
| Connection | Saved rooms | Mobile | Yes | Yes | Yes | None | Confirmed concept |
| Connection | Selected room | Mobile | Temporary | Yes | Yes | None | Confirmed concept |
| Connection | Reconnect preference | Mobile | Yes if supported | TBD | Yes | TBD | TBD |
| PC receiver | Room number | PC | Yes | Requires reconfiguration | Yes | Required | Confirmed concept |
| Mouse | Mode/axis/inversion/sensitivity | Mobile | Yes | Usually immediate | Yes | TBD | Confirmed settings |
| Mouse | Acceleration/dead zone/filtering/drift | Mobile | Yes | Usually immediate | Yes where exposed | TBD | Confirmed settings |
| IMU | Calibration state | Mobile | While valid | Recalibration/reset | Yes | Uncalibrated | Confirmed concept |
| Presentation | UI/confirmation preference | Mobile | Yes | Yes | Yes | TBD | TBD |
| Hosting | Quality/FPS/orientation/display | Mobile/PC | Yes where applicable | May require restart | Yes | TBD | TBD |
| File Sharing | PC receive destination | PC | Yes | TBD | Yes | TBD | Confirmed concept |
| File Sharing | Transfer preference | Mobile/PC | Yes where needed | TBD | Yes | TBD | TBD |
| Custom Commands | Definition/allowlist | PC | Yes | Yes under validation | Yes | TBD | Confirmed concept |
| Custom Commands | Confirmation requirement | PC | Yes | Yes | Yes | TBD | Confirmed concept |
| Security | OTP/session policy | PC | TBD | Usually reconnect/restart | Admin only | TBD | TBD |
| Runtime | OTP/session/transfer/stream | Both | No | State-driven | Status only | None | Confirmed temporary |

## 23. TBD Decisions

- Exact numerical defaults/ranges for mouse/IMU/hosting settings.
- Local storage/persistence and encryption-at-rest implementation.
- Configuration version/migration mechanism.
- Which active changes apply immediately versus require feature restart/reconnect/receiver restart.
- Supported screen-hosting options/capabilities by platform.
- Room-number format and duplicate-room conflict workflow.
- Administrator versus teacher configuration boundary.
- Configuration synchronization/conflict policy.
- Platform-specific limitations.

## Configuration Summary

Configuration keeps Air Mouse adaptable without complicating classroom use: frequent teaching actions remain on main screen, advanced feature settings remain grouped under three-dot menu, mobile and PC own only their relevant local preferences, and security/session rules cannot be disabled by ordinary configuration. Persistent settings are minimal, temporary session/transfer/stream state is cleaned up, and unresolved implementation choices remain explicitly **TBD**.

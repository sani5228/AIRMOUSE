# Air Mouse for College Teaching — Project Structure

## 1. Repository Overview

The repository separates mobile teacher controller, PC classroom receiver, shared logical contracts, tests, documentation, and build/deployment resources. It is intentionally small and feature-oriented for AI-assisted college-project development.

```text
project-root/
├── mobile/                         # Mobile application; framework layout TBD
│   ├── src-or-equivalent/           # TBD after mobile stack selection
│   ├── tests-or-equivalent/
│   └── README.md
├── pc/                              # PC receiver; desktop layout TBD
│   ├── src-or-equivalent/           # TBD after PC stack selection
│   ├── tests-or-equivalent/
│   └── README.md
├── shared/                          # Logical contracts/schema/constants; code sharing optional
├── tests/                           # Cross-app integration, network, end-to-end tests
├── docs/                            # Optional supporting docs; root specs remain authoritative
├── scripts/                         # Build/package/development helpers only when needed
├── README.md
├── TECH_STACK.md
├── ARCHITECTURE.md
├── DESIGN.md
├── API_PROTOCOL.md
├── TESTING.md
└── other project specification .md files
```

Actual source/build directories are **TBD** until `TECH_STACK.md` selects frameworks/languages. Do not create technology-specific directories before that decision. The repository must not add folders merely to look enterprise-scale.

## 2. Root-Level Files

| Item | Purpose |
|---|---|
| `README.md` | Project purpose, quick start after stack selection, repository map, contribution/development entry point. |
| `.gitignore` | Excludes generated builds, local configuration, secrets, caches, logs, and platform artifacts after tooling is selected. |
| Configuration templates | Added only when required; contain no real OTPs, credentials, addresses, or sensitive paths. |
| Dependency/build configuration | Added only after technology selection; one authoritative configuration per selected ecosystem where practical. |
| Root Markdown specs | Authoritative requirements documents; do not duplicate them under multiple folders. |
| `docs/` | Optional non-duplicative supporting material such as screenshots, setup guides, or generated references. |

## 3. Mobile Application Structure

Within `mobile/src-or-equivalent/`, use logical feature and core boundaries. Names adapt to selected technology conventions.

```text
mobile/src-or-equivalent/
├── ui/                 # Screens, components, theme, glassmorphism presentation
├── navigation/         # Screen/menu/navigation state
├── features/
│   ├── systems/        # Saved rooms, discovery-facing feature UI
│   ├── pairing/        # QR registration, OTP flow
│   ├── mouse/          # Gyro mode, Trackpad feature controller
│   ├── imu/            # Sensor/calibration/filtering logic
│   ├── presentation/   # Semantic presentation controls
│   ├── laser/          # Pointer controller/state
│   ├── file-transfer/  # File selection and transfer lifecycle
│   ├── screen-hosting/ # Capture/hosting lifecycle/state
│   └── custom-commands/# Command selection/result UI
├── core/
│   ├── session/        # Connection/session state and authorization gate
│   ├── discovery/      # Local discovery abstraction
│   ├── networking/     # Protocol transport abstraction
│   ├── persistence/    # Local settings/calibration/saved rooms
│   ├── permissions/    # Sensor/camera/file/capture permission boundary
│   ├── notifications/  # Notifications/haptics abstraction
│   └── errors/         # Error normalization/mapping
├── configuration/      # Defaults/configuration mapping, not hardcoded values
└── platform/           # Mobile platform capability adapters if needed
```

### Mobile boundaries

- `ui/` renders state and dispatches user intents; it does not process sensor mathematics or network payloads.
- `features/` owns feature business logic and feature state.
- `core/session` gates privileged actions.
- `core/networking` implements selected communication mechanism behind a stable feature-facing contract.
- `imu/` does not directly manipulate UI.
- `persistence/` does not contain UI/business transport logic.
- `platform/` isolates OS-specific permissions/capture/sensor/haptic behaviors if required.

## 4. PC Receiver Structure

Within `pc/src-or-equivalent/`, separate receiver core from OS/platform execution.

```text
pc/src-or-equivalent/
├── ui/
│   ├── setup/          # First-time room configuration and QR display
│   ├── tray/           # Compact tray/background panel
│   ├── otp/            # OTP display/waiting state
│   └── settings/       # PC configuration UI
├── receiver/
│   ├── lifecycle/      # Startup/background/shutdown coordination
│   ├── discovery/      # Local network announcement/discovery handling
│   ├── session/        # OTP, authorization, session lifecycle
│   ├── protocol/       # Incoming/outgoing contract handling
│   ├── input/          # Validated mouse/trackpad intent handling
│   ├── presentation/   # Semantic presentation/media handling
│   ├── laser/          # Pointer state/rendering coordination
│   ├── file-transfer/  # Transfer lifecycle and validation
│   ├── screen-hosting/ # Stream receiver/decode/display coordination
│   ├── custom-commands/# Allowlist and approved action executor
│   └── status/         # Receiver/feature status events
├── platform/
│   ├── input/          # OS mouse/keyboard adapter
│   ├── presentation/   # OS/application/media adapter
│   ├── tray/           # System-tray adapter
│   ├── notifications/  # Native notification adapter
│   ├── filesystem/     # Safe local storage/filesystem adapter
│   ├── display/        # Screen hosting/display/fullscreen adapter
│   └── commands/       # Approved local action adapter
├── core/
│   ├── configuration/  # Room/settings/local persistence
│   ├── logging/        # Sanitized diagnostics
│   ├── errors/         # Normalized errors
│   └── security/       # Validation policies/allowlist boundaries
└── application/        # PC composition root / dependency wiring
```

`platform/` must not contain session/network business logic. `receiver/` must validate session/request before platform adapters perform OS-level work.

## 5. Shared Components

`shared/` contains only logical artifacts genuinely usable by both sides. Code sharing is optional; it must not be forced if mobile and PC technology choices differ.

Potential shared categories:

- Semantic command definitions.
- Protocol/message schemas or generated contract inputs after selection.
- Error-code taxonomy.
- Capability names.
- Version constants.
- Conceptual data validation rules that can be implemented independently.

`API_PROTOCOL.md` remains authoritative; `shared/` must not silently redefine protocol rules.

## 6. Feature-to-Directory Mapping

| Feature | Mobile location | PC location | Main documentation |
|---|---|---|---|
| Gyro Mouse | `features/mouse`, `features/imu` | `receiver/input`, `platform/input` | `MOUSE.md`, `IMU.md` |
| Trackpad | `features/mouse` | `receiver/input`, `platform/input` | `MOUSE.md` |
| Calibration | `features/imu`, `core/persistence` | Usually no PC feature logic | `IMU.md` |
| Laser Pointer | `features/laser` | `receiver/laser`, `platform/display` | `FEATURES.md`, `DESIGN.md` |
| Presentation Controls | `features/presentation` | `receiver/presentation`, `platform/presentation` | `PRESENTATION.md` |
| File Sharing | `features/file-transfer` | `receiver/file-transfer`, `platform/filesystem` | `FILE_SHARING.md` |
| Screen Hosting | `features/screen-hosting`, `platform` | `receiver/screen-hosting`, `platform/display` | `SCREEN_MIRRORING.md` |
| Custom Commands | `features/custom-commands` | `receiver/custom-commands`, `platform/commands` | `CUSTOM_COMMANDS.md` |
| Pairing | `features/pairing`, `core/session` | `receiver/session`, `ui/setup`, `ui/otp` | `PAIRING.md` |
| Authentication | `core/session` | `receiver/session` | `PAIRING.md`, `SECURITY.md` |
| Discovery | `features/systems`, `core/discovery` | `receiver/discovery` | `PAIRING.md`, `NETWORK.md` |

## 7. UI Structure

### Mobile

`mobile/ui/` organizes main screen, feature views/panels, reusable components, theme, and advanced-settings views. Main screen directly exposes connected room/system, Gyro Mouse, Trackpad, Laser Pointer, Calibrate, Presentation Controls, Share File, Screen Hosting, and Custom Commands. Advanced configuration is grouped under three-dot menu.

### PC

`pc/ui/` organizes compact setup UI, QR display, OTP view, tray panel, settings, and non-blocking notifications. PC primarily operates as system-tray/background receiver and does not require a large teaching-time window. Visual rules are authoritative in `UI_UX.md`.

## 8. Platform-Specific Code

Use this boundary:

```text
Core/Feature Logic → Platform Abstraction → OS Implementation
```

Platform-specific adapters cover mouse movement/clicks, keyboard/presentation actions, volume/media, tray, notifications, windows/application interaction, filesystem operations, screen display, and approved action launch. Exact OS APIs remain **TBD** per `TECH_STACK.md`.

Mobile platform adapters may cover sensor availability, QR camera permission, file picker, screen capture permission, haptics, and lifecycle/background behavior.

## 9. Configuration Structure

Configuration belongs in local persistence/configuration modules, not hardcoded source values.

| Configuration | Location | Notes |
|---|---|---|
| User/UI preferences | Mobile/PC core configuration | Theme, notifications, basic preferences. |
| Room configuration | PC configuration; mobile saved systems | Room number is user-facing; internal identity opaque. |
| Security/session configuration | PC session/security boundary | OTP/session policies, exact form TBD. |
| Custom Commands | PC configuration | Allowlist definitions/approved mappings. |
| IMU calibration/settings | Mobile persistence | No raw sensor history. |
| File destination | PC configuration | Configured safe allowed destination. |

Reference `CONFIGURATION.md` if created and `DATA_MODEL.md` for logical data boundaries.

## 10. Testing Structure

```text
tests/
├── integration/        # Mobile↔PC discovery/session/feature interaction
├── network/            # Wi-Fi/reconnect/restriction/coexistence scenarios
├── end-to-end/         # Classroom workflow scenarios
├── fixtures-or-mocks/  # Sanitized controlled test inputs if required
└── README.md
```

Module-local unit/component tests stay under each platform’s selected framework convention, for example `mobile/tests-or-equivalent/` and `pc/tests-or-equivalent/`. Include sensor-processing tests near IMU logic, feature tests near feature boundaries, and platform adapter tests near adapter modules where practical. See `TESTING.md`.

## 11. Documentation Structure

Existing Markdown specifications remain at repository root unless a strong concrete reason to move them exists. `docs/` may contain non-duplicative supporting material, generated diagrams, setup/user documentation, and release notes.

Do not copy the same requirements into multiple locations. Update the authoritative document when a structural/requirement decision changes.

## 12. Build and Deployment Structure

`scripts/` is optional and only contains reusable build/package/development helpers after stack selection. Framework-native build configuration remains in its required mobile/PC location, **TBD**. Packaging/installer configuration belongs with its application or a clearly named deployment folder only if selected tooling requires it.

Keep deployment documentation in root `BUILD_DEPLOYMENT.md` or `docs/` without duplication. See `BUILD_DEPLOYMENT.md` if created.

## 13. Naming Conventions

Use naming conventions appropriate to selected technologies; exact casing is **TBD** until stack selection. Apply one consistent convention per platform.

- Directories: short, responsibility-oriented, no ambiguous utility dumping grounds.
- Source modules/classes/functions: descriptive names matching documented concepts.
- UI components: match feature/screen names.
- Tests: map clearly to module/feature and expected behavior.
- Configuration/assets: descriptive, environment-neutral names; never embed secrets.
- Avoid conflicting cross-platform conventions without a documented reason.

## 14. Dependency Boundaries

```text
UI
↓
Feature/Application Logic
↓
Core Services
↓
Network / Platform / Storage Adapters
```

Rules:

- Avoid circular dependencies.
- Sensor processing does not manipulate UI directly.
- UI does not directly execute OS-level commands.
- Mobile never contains PC OS integration.
- PC platform adapters do not contain networking/session business logic.
- Feature modules use core abstractions instead of directly coupling to unrelated features.
- Shared contracts flow downward; platform implementations depend on core contracts, not vice versa.

## 15. Security Boundaries

Security-sensitive functionality belongs at PC enforcement points:

- OTP/session validation: `pc/receiver/session` with core security policy.
- Incoming protocol/request validation: `pc/receiver/protocol` and relevant feature handler.
- OS action authorization: before `pc/platform/*` adapter calls.
- File/path validation: `pc/receiver/file-transfer` plus filesystem adapter.
- Custom Command allowlist: `pc/receiver/custom-commands`.
- Secure local configuration: `core/configuration`, mechanism TBD.

PC validates incoming requests before OS-level action. Reference `SECURITY.md`.

## 16. Data Flow Through the Structure

### Gyro Mouse

```text
Mobile Sensor → features/imu → features/mouse → core/networking
→ PC receiver/session → receiver/input → platform/input → OS
```

### Presentation

```text
Mobile UI → features/presentation → core/networking
→ PC receiver/protocol/session → receiver/presentation → platform/presentation → OS/Application
```

### Screen Hosting

```text
Mobile platform capture → features/screen-hosting encoder/stream boundary
→ streaming network layer → PC receiver/screen-hosting → decoder/display adapter
```

### File Sharing

```text
Mobile file picker → features/file-transfer → transfer network layer
→ PC receiver/file-transfer → validation → platform/filesystem configured storage
```

## 17. Repository Growth Rules

- Do not create a module without clear single responsibility.
- Do not create duplicate utilities/services.
- Reuse existing services and refactor common logic instead of copying.
- Keep feature logic inside feature boundary.
- Remove dead files and unused dependencies.
- Keep tests near modules or within defined tests structure.
- Update documentation when architecture/structure changes.
- Add platform-specific code only behind defined adapter boundary.

## 18. AI Coding Rules

The coding AI must:

- Inspect existing repository before creating/moving files.
- Follow `TECH_STACK.md`, `ARCHITECTURE.md`, and `DESIGN.md`.
- Never silently introduce a framework, language, package ecosystem, or new infrastructure.
- Never move existing files unnecessarily.
- Avoid rewriting working modules without clear reason.
- Preserve completed functionality.
- Update tests when behavior changes.
- Update documentation when structural decisions change.
- Explain major refactor/structural changes before applying them.

## 19. Structure Decision Log

| Decision | Current Structure | Status | Reason |
|---|---|---|---|
| Repository split | `mobile/`, `pc/`, `shared/`, `tests/`, root specs | Confirmed conceptual structure | Separates primary applications and contracts. |
| Root documentation | Existing specs remain root-level | Confirmed | Avoid documentation duplication/moves. |
| Shared code | Optional logical contracts only | Confirmed | Languages/frameworks are TBD; do not force sharing. |
| Mobile/PC source layout | Feature/core/platform boundaries | Confirmed conceptual structure | Supports testability and responsibility separation. |
| Technology-specific directories | TBD | TBD | Must follow selected stack. |
| Build/package layout | TBD | TBD | Depends on selected technology/deployment tooling. |

## 20. Final Proposed Repository Tree

```text
project-root/
├── mobile/
│   ├── src-or-equivalent/           # Technology-specific name TBD
│   │   ├── ui/
│   │   ├── navigation/
│   │   ├── features/
│   │   ├── core/
│   │   ├── configuration/
│   │   └── platform/
│   ├── tests-or-equivalent/
│   └── README.md
├── pc/
│   ├── src-or-equivalent/           # Technology-specific name TBD
│   │   ├── ui/
│   │   ├── receiver/
│   │   ├── platform/
│   │   ├── core/
│   │   └── application/
│   ├── tests-or-equivalent/
│   └── README.md
├── shared/
│   ├── contracts-or-equivalent/     # Optional after stack selection
│   └── README.md
├── tests/
│   ├── integration/
│   ├── network/
│   ├── end-to-end/
│   └── fixtures-or-mocks/
├── docs/
├── scripts/                         # Only when reusable scripts are needed
├── README.md
├── .gitignore
├── PRD.md
├── FEATURES.md
├── ARCHITECTURE.md
├── DESIGN.md
├── TECH_STACK.md
├── API_PROTOCOL.md
├── DATA_MODEL.md
├── ERROR_HANDLING.md
├── TESTING.md
├── DEVELOPMENT_PHASES.md
└── other authoritative specification files
```

## Project Structure Summary

The repository separates the mobile teacher controller from the PC background receiver, while retaining optional shared contracts and cross-application tests. Feature logic stays separate from UI, networking, persistence, and platform-specific code. PC platform adapters are isolated behind validated receiver logic. Root specifications remain authoritative, keeping the structure simple, incremental, maintainable, and suitable for a college prototype and AI-assisted implementation.

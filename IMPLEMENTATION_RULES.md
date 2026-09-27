# Air Mouse for College Teaching — Implementation Rules

## 1. Purpose

This document defines non-negotiable rules for AI coding agents implementing, modifying, debugging, and extending Air Mouse. The goal is AI-assisted development without unnecessary complexity, unsafe behavior, incompatible technologies, or accidental regressions.

Air Mouse is a practical college prototype: mobile teacher controller → local Wi-Fi → PC system-tray/background receiver. It has no ESP32, Arduino, Bluetooth controller, intermediary hardware, mandatory cloud service, or unrestricted remote shell feature.

## 2. Core Project Rules

The AI must:

- Read relevant project documentation before implementing major features.
- Follow `MASTER_SPEC.md` when it exists.
- Treat finalized requirements as authoritative.
- Inspect existing repository before creating/modifying files.
- Reuse existing functionality whenever possible.
- Keep implementation simple and practical.
- Avoid unnecessary abstractions and dependencies.
- Avoid rewriting working code without clear reason.
- Preserve working features when adding new ones.
- Avoid enterprise-scale infrastructure unless explicitly required.

## 3. Source of Truth Hierarchy

Use this authority order:

1. `MASTER_SPEC.md`
2. `PRD.md`
3. Feature-specific requirements
4. `ARCHITECTURE.md`
5. `TECH_STACK.md`
6. `DESIGN.md`
7. `API_PROTOCOL.md`
8. Other supporting documentation

If documents conflict: identify the conflict, do not silently choose, prefer the higher authority, and update documentation after resolution. If implementation and documentation conflict, inspect repository/history/context before changing either; determine which represents latest intentional decision.

## 4. Technology Rules

- Follow `TECH_STACK.md`.
- Do not introduce/replace framework without explicit justification and approved decision.
- Do not add libraries when existing stack already provides the capability.
- Prefer maintained, well-supported, open-source/free dependencies where practical.
- Avoid unnecessary third-party services and cloud infrastructure.
- If technology is **TBD**, do not silently decide it.

## 5. Repository Rules

Before modification:

- Inspect directory structure, relevant files, implementations, dependencies, tests, and run workflow.
- Do not assume repository is empty.
- Do not create duplicate modules because existing implementation was missed.
- Do not move files unnecessarily.
- Do not rename large repository portions without explicit reason.
- Follow `PROJECT_STRUCTURE.md`.

## 6. Architecture Rules

Maintain this separation:

```text
UI
↓
Application / Feature Logic
↓
Core Services
↓
Network / Storage / Platform
```

Do not allow UI to directly execute OS commands; sensor code to directly manipulate UI; networking to contain unnecessary feature business logic; mobile code to contain PC OS implementation; or platform-specific code to leak into shared/core logic. Follow `ARCHITECTURE.md`, `DESIGN.md`, and `PROJECT_STRUCTURE.md`.

## 7. Mobile Rules

Mobile is the teacher’s primary controller. Main screen must expose:

- Connected room/system.
- Gyro Mouse.
- Trackpad.
- Laser Pointer.
- Calibrate.
- Presentation Controls.
- Share File.
- Screen Hosting.
- Custom Commands.

Advanced configuration belongs under three-dot menu. Do not hide frequent teaching controls in deeply nested settings.

## 8. PC Receiver Rules

PC receiver primarily operates as background/system-tray application with minimal teaching-time UI. Do not require a large open window during teaching. Use compact notifications for meaningful events.

PC must remain responsive while receiving mouse input, processing presentation commands, transferring files, and receiving/displaying Screen Hosting stream.

## 9. Pairing Rules

Required flow:

```text
PC Room Number → QR → Mobile scan → Discovery → Room selection
→ Temporary PC OTP → Mobile OTP entry → PC verification → Session
```

- QR identifies room and never authenticates user/device.
- QR contains only room number.
- OTP provides temporary authentication and expires.
- Invalid/expired OTP fails safely.
- Sessions invalidate on appropriate disconnect/logout/restart conditions.
- Room number is primary user-facing system identity.
- Internal IDs may exist but should not unnecessarily appear in UI.

## 10. Network Rules

Separate traffic by purpose:

| Traffic | Rule |
|---|---|
| Control | Low-latency small commands. |
| File transfer | Reliable file data/progress/cancellation path. |
| Screen Hosting | Continuous real-time streaming path. |

Do not send screen frames through ordinary control messages. Do not block UI waiting for network operations. Handle disconnect, reconnect, timeout, duplicate/stale/invalid messages. Follow `NETWORK.md` and `API_PROTOCOL.md`.

## 11. Mouse Rules

Support Gyro/IMU Mouse and touch-based Trackpad. Gyro behavior respects axis selection/inversion, sensitivity, acceleration, dead zone, filtering, drift compensation, stationary detection, and calibration.

Do not discard every small movement merely to remove jitter. Preserve intentional fine movement while reducing sensor noise. Follow `MOUSE.md` and `IMU.md`.

## 12. Presentation Rules

Use semantic commands:

```text
NEXT_SLIDE, PREVIOUS_SLIDE, START_PRESENTATION, END_PRESENTATION,
BLANK_SCREEN, VOLUME_UP, VOLUME_DOWN, MUTE, PLAY_PAUSE
```

Mobile sends intent; PC maps it to supported local OS/application behavior. Do not allow arbitrary keyboard/shell commands as shortcut.

## 13. Custom Command Security Rules

Required chain:

```text
Command ID → Validation → Allowlist → Approved Local Action → Execution → Result
```

Never enable unrestricted arbitrary shell/terminal execution by default. Validate command ID, parameters, authorized session, and enabled/disabled state. Do not permit arbitrary executable paths or scripts without a future explicit security specification.

## 14. File Sharing Rules

Support Phone → PC and PC → Phone with progress, cancellation, completion, failure recovery, and configurable destination. Do not implement transfer history unless explicitly requested.

Validate filename, path, file size where appropriate, destination, permissions, storage, and session. Never allow uncontrolled path traversal.

## 15. Screen Hosting Rules

Screen Hosting is full real-time mirroring:

```text
Mobile Screen Capture → Encode → Streaming Transport → PC Receiver → Decode → Display
```

Require low latency, portrait/landscape, start/stop, fullscreen, quality/FPS configuration, interruption recovery, efficient CPU/battery use, and hardware acceleration where available. Do not replace mirroring with file sharing, screenshots, or periodic image uploads.

## 16. UI/UX Rules

Both interfaces use consistent glassmorphism: translucent surfaces, background blur, rounded corners, subtle borders/shadows, readable typography/icons, restrained transitions, light/dark themes, and accessibility.

Never add effects that materially reduce readability or performance. Follow `UI_UX.md`.

## 17. Error Handling Rules

Every feature considers success, failure, timeout, cancellation, disconnect, invalid input, and recovery. Errors must be understandable, appropriately logged, recoverable where safe, and non-destructive. Do not silently swallow important errors. Follow `ERROR_HANDLING.md`.

## 18. Security Rules

Never:

- Hardcode secrets.
- Log OTPs, passwords, or authentication/session secrets.
- Trust unauthenticated commands.
- Execute arbitrary remote commands.
- Trust client-provided file paths.
- Bypass validation to make a demo work.
- Disable security checks because development is easier.

PC is final enforcement point for OS-level actions. Follow `SECURITY.md`.

## 19. Performance Rules

Prioritize low latency for Gyro/Trackpad cursor movement, laser updates, and presentation commands. Avoid unnecessary polling, UI blocking, excessive allocations/logging, repeated connections, and unnecessary payloads.

Optimize screen streaming independently from control communication. Use `TESTING.md` to establish measured targets; do not invent performance numbers.

## 20. State Management Rules

Represent important states explicitly, for example:

```text
Disconnected, Discovering, Authenticating, Connected, Reconnecting, Failed
```

Do not rely on scattered booleans when a state model is needed. Avoid contradictory states such as disconnected but authenticated/streaming unless explicitly valid and documented.

## 21. Configuration Rules

Do not hardcode room numbers, paths, addresses, credentials, secrets, or preferences. Use project configuration/persistence mechanism. Separate development, test, and classroom/production configuration. Follow `CONFIGURATION.md` if present and `DATA_MODEL.md`.

## 22. Logging Rules

Log startup/shutdown, connection/authentication outcomes, feature start/stop, errors, and recovery using sanitized diagnostics.

Do not log OTP values, passwords, authentication secrets, file contents, unnecessary personal information, screen content, or sensitive paths. Follow `ERROR_HANDLING.md`.

## 23. Testing Rules

Whenever a feature changes:

1. Build/compile it.
2. Run relevant unit tests.
3. Run relevant integration tests.
4. Test actual behavior where possible.
5. Fix failures before moving on.

Compilation alone does not prove a feature works. Follow `TESTING.md`.

## 24. Change Management

For significant changes, explain:

- What changed.
- Why it changed.
- Files changed.
- Existing behavior affected.
- Tests performed.

Avoid unrelated changes in same step. Preserve working behavior and document structural decisions.

## 25. Dependency Rules

Before adding dependency: check existing code/framework capability, maintenance, license, complexity cost, security impact, and whether it introduces cloud/hardware/services. Document important approved dependencies in `DEPENDENCIES.md`.

## 26. Do Not List

The AI must not:

- Add ESP32, Arduino, Bluetooth controllers, or intermediary hardware.
- Add unnecessary cloud infrastructure/databases.
- Add arbitrary shell execution.
- Replace Screen Hosting with screenshots.
- Hide frequent controls in settings.
- Remove working functionality without reason.
- Introduce unapproved frameworks.
- Hardcode secrets.
- Ignore errors/tests.
- Create duplicate implementations.
- Over-engineer the project.

## 27. Definition of Done

A feature is complete only when requirements, UI behavior, network behavior, error cases, security rules, relevant tests, and regression safety are addressed, and needed documentation is updated. Code compiling is not sufficient.

## 28. AI Agent Working Procedure

```text
Read requirements → Inspect repository → Identify modules → Plan minimal change
→ Implement → Build → Test → Debug → Re-test → Review security/performance
→ Update documentation → Mark complete
```

Do not jump from requirement directly to large-scale code generation.

## 29. Rule Conflicts

When requirements conflict: identify conflict, do not guess, apply source-of-truth hierarchy, preserve working behavior where possible, mark unresolved decisions **TBD**, and ask for clarification when conflict materially affects implementation.

## Implementation Rules Summary

The AI must make the smallest safe change needed for each requirement, preserve existing functionality, validate all privileged PC actions on the receiver, keep teaching UI simple, and verify behavior through relevant tests before declaring work complete.

# Air Mouse for College Teaching — UI Components

## 1. Purpose and Design Goals

`UI_COMPONENTS.md` defines reusable mobile and PC UI building blocks. `UI_UX.md` defines visual language and overall experience; this document defines component behavior, states, interactions, and consistency rules.

Components must be clean, simple, fast to understand, classroom-suitable, touch-friendly on mobile, unobtrusive on PC, consistent across both sides, glassmorphism-based, readable in light/dark themes, and free of unnecessary complexity. UI framework implementation is **TBD**.

## 2. Component Principles

- Reuse a small set of components rather than duplicate cards/buttons/dialogs.
- Apply consistent spacing, typography, icons, state feedback, and animation.
- Include default, active, pressed, hover where applicable, disabled, loading, success, error, and reconnecting states as relevant.
- Make error/retry/disabled states understandable.
- Meet accessible contrast/touch-target requirements from `UI_UX.md`.
- Use one component per clear responsibility; avoid giant unrelated components.
- Do not create multiple components with essentially identical roles.

## 3. Global Design Tokens

Use conceptual tokens; exact values are **TBD** through visual testing.

| Token group | Tokens |
|---|---|
| Color | Primary, Secondary, Success, Warning, Error, Background, Surface, Text, Muted Text. |
| Typography | Display, Heading, Body, Label, Caption, Status. |
| Spacing | Compact, standard, spacious gaps/insets. |
| Shape | Card, control, dialog corner radius. |
| Glass | Surface opacity, blur, subtle border, elevation shadow. |
| Icons | Small, standard, prominent action icon sizes. |
| Touch | Minimum touch target and control spacing. |
| Motion | Short transition, progress motion, reduced-motion alternative. |

Tokens adapt to light/dark themes without fixed arbitrary colors/numbers.

## 4. Glassmorphism Components

| Component | Purpose | States |
|---|---|---|
| Glass Card | Groups related teaching controls/status | Default, active, disabled, error. |
| Glass Button | Labeled primary/secondary action | Default, pressed, disabled, loading, success/error where meaningful. |
| Glass Icon Button | Compact recognizable action | Default, pressed, hover on PC, active, disabled. |
| Glass Navigation Element | Main navigation/section selection | Default, selected, disabled. |
| Glass Dialog | Focused confirmation/error/setup action | Entering, active, loading, error, dismissed. |
| Glass Bottom Sheet/Panel | Mobile contextual actions/settings | Collapsed, expanded, loading, error. |
| Glass Status Indicator | Connection/feature state | Connected, warning, reconnecting, error, inactive. |
| Glass Settings Section | Groups advanced controls | Default, expanded/collapsed, disabled. |

Glass effects must preserve contrast/readability; excessive blur/layers are prohibited.

## 5. Mobile Navigation Components

Mobile navigation includes Main Screen, Three-Dot Menu, grouped Settings sections, Back navigation, feature panels/views, and Connection Status component.

Main Screen directly contains connected room/system, Gyro Mouse, Trackpad, Laser Pointer, Calibrate, Presentation Controls, Share File, Screen Hosting, and Custom Commands. Do not place these primary teaching actions only in advanced settings.

Three-dot menu groups Mouse Settings, IMU, Presentation, Screen Hosting, Systems, File Sharing, Custom Commands, Security, and General/App Settings.

## 6. Connection Status Component

Reusable connection component shows room number, connection/authentication state, connected/disconnected/reconnecting state, and optional quality indication. It uses room number as primary identity and never exposes IP, ports, MAC addresses, or internal service identifiers in ordinary teacher UI.

States: Disconnected, Discovering, System Selected, Waiting for OTP, Authenticating, Connected, Reconnecting, Session Expired, Failed.

## 7. System Selection Component

System selection displays discovery progress, saved rooms, reachable rooms, unavailable rooms, refresh action, and duplicate-room warning.

| State | UI behavior |
|---|---|
| Discovering | “Looking for classroom systems…” with bounded progress. |
| Available | Room-number card/action, selectable. |
| Unavailable | Saved room remains visible but disabled/retryable. |
| Duplicate room | Warn and require safe disambiguation, TBD. |
| Empty | No systems found with retry and Wi-Fi guidance. |

## 8. OTP Component

PC OTP view displays room number, prominent temporary OTP, expiry indication, and waiting/authenticating state. Mobile OTP input displays selected room, secure temporary input, progress, invalid/expired state, and retry/back actions.

OTP is never permanently displayed/stored unnecessarily. Success leads to connected/ready state; failure explains next action without exposing security details.

## 9. Gyro Mouse Components

| Component | Behavior |
|---|---|
| Gyro activation card/button | Starts/stops Gyro Mouse; shows active/inactive state. |
| Mouse lock/pause | Clearly indicates movement is paused without disconnecting. |
| Quick sensitivity control | Optional immediate adjustment where practical. |
| Calibrate action/status | Direct main-screen action and calibration state. |
| Axis selector | Advanced XY/XZ/YZ selection. |
| Axis inversion controls | Advanced independent X/Y/Z inversion. |
| IMU status feedback | Sensor/calibration/drift/error feedback. |

Detailed behavior follows `MOUSE.md` and `IMU.md`.

## 10. Trackpad Components

Trackpad surface resembles familiar laptop touchpad. It has large bounded touch area, optional compact mode/exit status, subtle press/drag feedback, and no distracting overlay.

It supports visual feedback for tap/click, drag active state, two-finger scroll recognition where useful, and disconnected overlay. Gestures remain defined by `MOUSE.md`; component must not make ambiguous input look successfully executed.

## 11. Presentation Controls

Use large direct-access controls for Previous, Next, Start, End, Blank Screen, Volume Up/Down, Mute, and Play/Pause. Group Slide Navigation, Presentation State, and Media/System controls.

Controls show pressed state immediately. Toggle state is shown only when reliably known. Avoid confirmation for normal low-risk actions; confirmation may be used for disruptive End Presentation if justified. Presentation Controls remain on main screen.

## 12. Laser Pointer Components

Laser UI includes Start/Stop control, persistent active indicator, pointer control surface/state, and optional status/sensitivity indicator. The active state must be unmistakable. On disconnect/error, component becomes inactive and indicates pointer stopped/unavailable.

## 13. Calibration Components

Calibration flow:

```text
Calibrate → Instruction → Stable-surface message → Countdown
→ Sampling/Progress → Success or Failure
```

Components: instruction dialog/panel, countdown/progress indicator, cancel action where appropriate, success feedback, failure/retry feedback, recalibrate action, and advanced reset-calibration action. Status always communicates running/completed/failed/cancelled.

## 14. File Sharing Components

| Component | Behavior |
|---|---|
| File picker entry | Starts user-selected upload/download flow. |
| Selected file list | Shows current selected files, not persistent history. |
| Transfer progress | File name, percentage/equivalent, status, cancel. |
| Completion state | Transient success notification. |
| Error state | Clear reason/retry/return action. |
| Destination selector | PC configuration/settings destination where appropriate. |

Do not create a transfer-history UI.

## 15. Screen Hosting Components

Components include Start Screen Hosting button, Stop button, active streaming indicator, connection/recovery state, quality/FPS controls where supported, and PC fullscreen/window state where applicable.

The UI explicitly states active full real-time mobile mirroring. It distinguishes Starting, Active, Reconnecting, Failed, and Stopped. See `SCREEN_MIRRORING.md`.

## 16. Custom Command Components

Custom Command list presents enabled command buttons/cards with display name, optional description/icon, disabled state, optional confirmation cue, pending state, and success/failure feedback.

Advanced settings provide add/edit/remove/enable/disable/reorder where supported. Do not provide generic shell/terminal input field. Behavior follows `CUSTOM_COMMANDS.md`.

## 17. Settings Components

Advanced settings use grouped reusable sections: Mouse Settings, IMU, Presentation, Screen Hosting, Systems, File Sharing, Custom Commands, Security, and General/App Settings.

Reusable controls: Toggle, Slider, Dropdown/Select, Segmented Control, Number Input, Reset Button, Information Row, and Settings Section. Each has label, current value/state, accessibility description, disabled/error feedback, and reset/help behavior where relevant.

## 18. PC Setup Components

PC first-time setup uses Welcome screen, Room Number Input, QR Display, Setup Completion, and Configuration Status components. Keep flow simple, focused, and non-technical. QR component clarifies registration only.

## 19. PC Tray Components

Tray UI includes Tray Icon, Compact Status Panel, Room Number, Connection Status, Connected Device where available, Feature Status, Disconnect, Settings, and Exit. It remains lightweight and unobtrusive during teaching.

## 20. PC Notifications

Reusable notification component handles connection, authentication completion, file received, Screen Hosting start/stop, disconnection, and important failure. Do not notify for every mouse/pointer update. Notifications are concise, non-blocking, and avoid presentation obstruction.

## 21. Dialogs and Confirmation

Use dialogs for first-time setup, OTP, calibration instruction/failure, destructive configuration changes, permission/action-required errors, and disruptive actions where justified. Do not require confirmation for every normal teaching command.

## 22. Loading and Progress

Reusable loading/progress states cover discovery, authentication, transfer, hosting startup, calibration, and reconnection. Every long operation has meaningful status/progress, failure handling, and cancellation where appropriate. Avoid indefinite uninformative spinners.

## 23. Error Components

Use Inline Error for local field/settings issue, Toast/Snackbar for brief outcomes, Dialog for blocking/action-required failure, Retry Action for recoverable errors, and Connection-Loss Banner for persistent session disruption.

Each error states what happened and next action without stack traces, internal paths, addresses, or secrets. See `ERROR_HANDLING.md`.

## 24. Empty States

| Empty state | Required content |
|---|---|
| No systems discovered | Wi-Fi/retry guidance and refresh action. |
| No saved rooms | QR registration guidance. |
| No Custom Commands | Explain no enabled commands; link to management where authorized. |
| No files selected | File-picker entry/action. |
| No active transfer | Neutral state; no historical transfer list. |

## 25. Accessibility

Components require readable text, adequate contrast, sufficient touch targets, icons plus labels when ambiguous, non-color-only state cues, keyboard accessibility on PC where applicable, predictable navigation, and reduced-motion consideration. Glass effects never reduce critical readability.

## 26. Responsive Behavior

Components adapt to phone size, portrait/landscape, small/large displays, PC window resizing, and fullscreen. Use flexible layout/cards/spacing rather than hardcoded dimensions. Exact breakpoints/sizes are **TBD**.

## 27. Common Component State Model

| State | Behavior |
|---|---|
| Default | Available neutral state. |
| Active | Current selected/running/toggled feature; persistent visual indicator. |
| Pressed | Immediate interaction feedback. |
| Hover | PC pointer feedback where applicable. |
| Disabled | Not actionable; explains why where useful. |
| Loading | Operation ongoing; clear bounded status. |
| Success | Brief confirmation/state update. |
| Error | Clear failure plus recovery action. |
| Reconnecting | Temporarily unavailable; do not imply action succeeds. |

## 28. Naming and Organization

Component naming/casing follows selected `TECH_STACK.md` conventions, currently **TBD**. Components have single responsibility, predictable names, documented inputs/outputs, minimal internal state, and feature-specific location unless truly shared. Avoid giant components containing unrelated features.

## 29. UI Performance Rules

Avoid excessive blur/animation, unnecessary rebuilds/rerenders, oversized assets, blocking UI logic, and continuous visual updates that do not help teacher. Glassmorphism must not make application noticeably slow. Streaming/sensor/network processing remains outside rendering/UI layer.

## 30. Component-to-Feature Mapping

| Component | Feature | Mobile/PC | Reusable | Source of Behavior |
|---|---|---|---:|---|
| Connection Status | Pairing/session | Both | Yes | `PAIRING.md`, `UI_UX.md` |
| System Selection | Discovery | Mobile | Yes | `PAIRING.md`, `NETWORK.md` |
| OTP Input/Display | Authentication | Both | Yes | `PAIRING.md`, `SECURITY.md` |
| Trackpad Surface | Trackpad | Mobile | Yes | `MOUSE.md` |
| Calibration Flow | IMU | Mobile | Yes | `IMU.md`, `MOUSE.md` |
| Presentation Button Group | Presentation/media | Mobile | Yes | `PRESENTATION.md` |
| Transfer Progress | File Sharing | Both | Yes | `FILE_SHARING.md` |
| Hosting Status | Screen Hosting | Both | Yes | `SCREEN_MIRRORING.md` |
| Command Card | Custom Commands | Mobile | Yes | `CUSTOM_COMMANDS.md` |
| Tray Status Panel | Receiver | PC | Yes | `UI_UX.md`, `FEATURES.md` |
| Error/Retry Pattern | All | Both | Yes | `ERROR_HANDLING.md` |

## 31. AI Coding Rules

AI must reuse existing components, check this document before adding component, avoid near-duplicate buttons/cards/dialogs, follow `UI_UX.md`, preserve glassmorphism/readability, keep main screen minimal, keep advanced configuration in three-dot menu, test non-happy states, test responsive behavior, and avoid visual complexity without requirement.

## 32. Component Decision Log

| Component/Area | Decision | Status | Notes |
|---|---|---|---|
| Visual language | Shared glassmorphism | Confirmed | Light/dark readability required. |
| Main teaching controls | Direct main-screen access | Confirmed | Do not hide in settings. |
| Advanced configuration | Grouped three-dot menu | Confirmed | Settings sections reusable. |
| UI framework | TBD | TBD | Must follow `TECH_STACK.md`. |
| Token exact values | TBD | TBD | Determine through visual/accessibility testing. |
| Responsive breakpoints | TBD | TBD | Determine after selected mobile/PC platforms. |

## UI Component Summary

Air Mouse should be assembled from a small consistent set of reusable glass components—status, cards, controls, panels, dialogs, progress, errors, and settings—rather than independently designed screens. Shared component states and behavior keep mobile and PC readable, predictable, classroom-friendly, and maintainable.

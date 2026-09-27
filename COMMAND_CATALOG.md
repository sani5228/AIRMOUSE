# Air Mouse for College Teaching — Command Catalog

## 1. Purpose

This document is the authoritative semantic definition of commands used between the mobile controller and PC receiver. It defines command identity, meaning, conceptual parameters, validation, authorization, and expected result. It does not define transport, port, serialization, framework APIs, or OS-specific execution.

The mobile expresses teacher intent. The PC validates intent and decides how to perform approved local behavior. For example, mobile sends `NEXT_SLIDE`; PC maps it to supported local presentation action.

## 2. Command Design Principles

Commands must be semantic, explicit, validatable, versionable when necessary, small/predictable, OS-independent, and safe after authentication/validation. Mobile must not need to know keyboard/API details. PC must not execute arbitrary shell/terminal text, arbitrary paths, or arbitrary OS requests.

## 3. Categories

- Mouse
- Trackpad
- Presentation
- Media/Volume
- Laser Pointer
- Screen Hosting
- File Sharing
- Custom Commands
- Session/Connection
- Calibration
- System/Status

## 4. Master Catalog

| Command ID | Category | Purpose | Parameters | Response | Auth required |
|---|---|---|---|---|---:|
| `MOUSE_CURSOR_DELTA` | Mouse | Relative cursor movement | x/y delta; optional freshness context | No per-update response normally | Yes |
| `MOUSE_LEFT_CLICK` | Mouse | Left click | Request context | Accepted/result/error | Yes |
| `MOUSE_RIGHT_CLICK` | Mouse | Right click | Request context | Accepted/result/error | Yes |
| `MOUSE_DOUBLE_CLICK` | Mouse | Double click | Request context | Accepted/result/error | Yes |
| `MOUSE_BUTTON_DOWN` | Mouse | Press primary button for drag | Button/context | Accepted/result/error | Yes |
| `MOUSE_BUTTON_UP` | Mouse | Release pressed button | Button/context | Accepted/result/error | Yes |
| `MOUSE_DRAG_START` | Mouse | Start drag lifecycle | Request context | Accepted/status/error | Yes |
| `MOUSE_DRAG_MOVE` | Mouse | Relative movement while drag active | x/y delta; freshness context | No per-update response normally | Yes |
| `MOUSE_DRAG_END` | Mouse | Finish drag lifecycle | Request context | Accepted/status/error | Yes |
| `MOUSE_SCROLL_VERTICAL` | Mouse | Vertical scroll | vertical delta | No per-update response normally | Yes |
| `MOUSE_SCROLL_HORIZONTAL` | Mouse | Horizontal scroll | horizontal delta | No per-update response normally | Yes |
| `MOUSE_PAUSE` | Mouse | Pause/lock mouse movement | paused state | Status/error | Yes |
| `TRACKPAD_CURSOR_DELTA` | Trackpad | Relative touch movement | x/y delta; optional freshness | No per-update response normally | Yes |
| `TRACKPAD_TAP` | Trackpad | Mobile-recognized left tap | Request context | Accepted/result/error | Yes |
| `TRACKPAD_DOUBLE_TAP` | Trackpad | Mobile-recognized double tap | Request context | Accepted/result/error | Yes |
| `TRACKPAD_TWO_FINGER_TAP` | Trackpad | Mobile-recognized right tap | Request context | Accepted/result/error | Yes |
| `TRACKPAD_SCROLL_VERTICAL` | Trackpad | Mobile-recognized vertical scroll | vertical delta | No per-update response normally | Yes |
| `TRACKPAD_SCROLL_HORIZONTAL` | Trackpad | Mobile-recognized horizontal scroll | horizontal delta | No per-update response normally | Yes |
| `TRACKPAD_DRAG_START` | Trackpad | Start mobile-recognized drag | Request context | Accepted/status/error | Yes |
| `TRACKPAD_DRAG_MOVE` | Trackpad | Move active drag | x/y delta; freshness | No per-update response normally | Yes |
| `TRACKPAD_DRAG_END` | Trackpad | Release active drag | Request context | Accepted/status/error | Yes |
| `NEXT_SLIDE` | Presentation | Advance slide/step | Request context | Accepted/result/error | Yes |
| `PREVIOUS_SLIDE` | Presentation | Go back slide/step | Request context | Accepted/result/error | Yes |
| `START_PRESENTATION` | Presentation | Start/activate presentation | Request context | Accepted/result/error | Yes |
| `END_PRESENTATION` | Presentation | End/exit presentation | Request context | Accepted/result/error | Yes |
| `BLANK_SCREEN` | Presentation | Activate/toggle blank presentation state where supported | Request context | Accepted/result/state/error | Yes |
| `VOLUME_UP` | Media | Raise system audio | Request context | Accepted/result/error | Yes |
| `VOLUME_DOWN` | Media | Lower system audio | Request context | Accepted/result/error | Yes |
| `MUTE` | Media | Toggle/set mute where supported | Request context | Accepted/result/state/error | Yes |
| `PLAY_PAUSE` | Media | Toggle current supported media state | Request context | Accepted/result/state/error | Yes |
| `LASER_START` | Laser Pointer | Start PC pointer rendering | Request context | Status/error | Yes |
| `LASER_STOP` | Laser Pointer | Stop/hide pointer | Request context | Status/error | Yes |
| `LASER_POSITION` | Laser Pointer | Update pointer position | x/y position; freshness context | No per-update response normally | Yes |
| `SCREEN_HOSTING_START` | Screen Hosting | Request live mirroring start | Request/config context | Accepted/ready/rejected | Yes |
| `SCREEN_HOSTING_STOP` | Screen Hosting | Stop live mirroring | Request context | Stopped/error | Yes |
| `SCREEN_HOSTING_UPDATE_CONFIG` | Screen Hosting | Request supported quality/FPS change | Requested settings | Accepted/rejected/status | Yes |
| `TRANSFER_REQUEST` | File Sharing | Request phone→PC or PC→phone transfer | transfer ID, direction, metadata | Accepted/rejected | Yes |
| `TRANSFER_START` | File Sharing | Begin accepted transfer lifecycle | transfer ID | Status/error | Yes |
| `TRANSFER_ACCEPT` | File Sharing | Accept incoming transfer where required | transfer ID | Start/status | Yes |
| `TRANSFER_REJECT` | File Sharing | Reject incoming transfer | transfer ID, safe reason | Rejected status | Yes |
| `TRANSFER_CANCEL` | File Sharing | Cancel active transfer | transfer ID | Cancelled/error | Yes |
| `TRANSFER_PROGRESS` | File Sharing | Report active transfer progress | transfer ID, progress/state | UI status | Yes |
| `TRANSFER_COMPLETE` | File Sharing | Confirm finalized transfer | transfer ID | Completion status | Yes |
| `TRANSFER_FAILURE` | File Sharing | Report transfer failure | transfer ID, safe error | Failure status | Yes |
| `CUSTOM_COMMAND_REQUEST` | Custom Commands | Request approved local action | command ID; permitted parameters | Result/rejection | Yes |
| `CUSTOM_COMMAND_RESULT` | Custom Commands | Report execution result | command ID; request context; status | UI status | Yes |
| `CUSTOM_COMMAND_REJECTED` | Custom Commands | Report rejected command | command ID; safe reason | UI status | Yes |
| `AUTH_REQUEST` | Session | Submit PC OTP for authentication | selected room context; OTP | Success/failure | No prior session |
| `AUTH_SUCCESS` | Session | Establish authorized session | session context/capabilities | Connected state | N/A |
| `AUTH_FAILURE` | Session | Reject authentication | safe reason | Retry/return state | N/A |
| `SESSION_TERMINATE` | Session | Explicit disconnect/end session | session context | Ended acknowledgement | Yes |
| `SESSION_ENDED` | Session | Notify session ended | safe reason | Disabled controls | N/A |
| `SESSION_STATUS` | Session | Report session state | state/context | UI status | Context-dependent |
| `HEARTBEAT` | Session | Minimal health/keepalive where needed | session context | Optional status | Yes |
| `CALIBRATION_START` | Calibration | Begin local mobile calibration flow | optional local profile context | Local status | No PC action normally |
| `CALIBRATION_RESULT` | Calibration | Report calibration outcome where needed | success/state/safe summary | UI status | Context-dependent |
| `CALIBRATION_RESET` | Calibration | Reset local calibration | local profile context | Local status | No PC action normally |
| `PC_STATUS` | System | Receiver room/availability/feature status | room/status/capabilities | UI status | No for discovery-safe subset |
| `FEATURE_STATUS` | System | Report feature state | feature/state/safe detail | UI status | Context-dependent |
| `CAPABILITY_STATUS` | System | Report supported feature capability | capability set/version | UI state | Context-dependent |

## 5. Mouse Commands

Mouse commands are continuous delta input and discrete action input. `MOUSE_CURSOR_DELTA` and `MOUSE_DRAG_MOVE` are delta-based, not absolute screen coordinates. Units are conceptual relative movement units; exact numeric range/scaling is **TBD**.

Movement may carry timestamp/sequence/freshness context under `API_PROTOCOL.md`. PC validates session, type/range, current drag/pause state, and freshness. High-frequency movement normally has no individual acknowledgement.

`MOUSE_BUTTON_DOWN`/`MOUSE_BUTTON_UP` support explicit press/release behavior; `MOUSE_DRAG_*` provides higher-level drag lifecycle. Final choice between them for Trackpad/Gyro implementation must avoid duplicate button lifecycle and is **TBD**.

## 6. Trackpad Commands

Mobile interprets touch gestures and sends higher-level semantic events. This keeps PC independent of phone touch implementation. `TRACKPAD_TAP`, `TRACKPAD_DOUBLE_TAP`, and `TRACKPAD_TWO_FINGER_TAP` map to PC mouse action after validation; movement/scroll/drag use semantic relative data.

Gesture thresholds/conflicts belong in `MOUSE.md`. Do not duplicate raw touch events unnecessarily or require PC to infer mobile gestures.

## 7. Presentation Commands

`NEXT_SLIDE`, `PREVIOUS_SLIDE`, `START_PRESENTATION`, `END_PRESENTATION`, and `BLANK_SCREEN` are established semantic commands. PC owns local application/OS mapping and reports unsupported/unavailable/failure truthfully. Mobile never supplies arbitrary keyboard shortcut or shell text.

## 8. Media Commands

`VOLUME_UP`, `VOLUME_DOWN`, `MUTE`, and `PLAY_PAUSE` express semantic classroom media/system intent. PC maps action to supported local OS/application behavior. Mobile only displays true state when PC provides it.

## 9. Laser Pointer Commands

`LASER_START` enables pointer, `LASER_STOP` hides it, and `LASER_POSITION` sends conceptual position. Position representation/scaling is **TBD**. Position updates are ordered/freshness-aware where final protocol requires; stale updates after stop, disconnect, or new session are ignored. PC hides pointer if session ends unexpectedly. Rendering technology is **TBD**.

## 10. Screen Hosting Commands

`SCREEN_HOSTING_START`, `SCREEN_HOSTING_STOP`, and optional `SCREEN_HOSTING_UPDATE_CONFIG` control stream lifecycle. Actual encoded video/frame stream is not an ordinary command and must use separate streaming-oriented path. Quality/FPS controls are accepted only if PC/mobile capability supports them. See `SCREEN_MIRRORING.md`.

## 11. File Sharing Commands

`TRANSFER_REQUEST` identifies direction, transfer ID, safe metadata, and destination context where applicable. Receiver accepts/rejects; active lifecycle reports start/progress/cancel/completion/failure. Actual bytes are separate reliable transfer data, not ordinary control commands.

No transfer-history command exists. Transfer ID is temporary active-operation identity. See `FILE_SHARING.md`.

## 12. Custom Commands

A request contains command ID and only command-permitted parameters. User-facing name, enabled state, and optional confirmation are command-definition data, not permission for mobile to choose arbitrary action.

```text
CUSTOM_COMMAND_REQUEST → validate session → validate command ID/parameters
→ PC allowlist → approved local action → result or rejection
```

PC rejects unknown/disabled/unauthorized/invalid requests. Arbitrary shell/terminal execution is not part of catalog.

## 13. Session Commands

`AUTH_REQUEST`, `AUTH_SUCCESS`, `AUTH_FAILURE`, `SESSION_TERMINATE`, `SESSION_ENDED`, `SESSION_STATUS`, and optional `HEARTBEAT` represent logical session lifecycle. Complete authentication detail belongs in `PAIRING.md`, `SECURITY.md`, and `API_PROTOCOL.md`.

## 14. Calibration Commands

Calibration is predominantly local mobile sensor processing. `CALIBRATION_START`, `CALIBRATION_RESULT`, and `CALIBRATION_RESET` may be local events and require PC communication only when UI/session state needs it. PC must not be required to calculate IMU calibration unless future requirement explicitly adds it. See `IMU.md`.

## 15. Common Parameters

| Parameter type | Conceptual fields |
|---|---|
| Cursor delta | X delta, Y delta, optional timestamp/sequence/freshness. |
| Pointer position | X position, Y position, optional timestamp/sequence/freshness. |
| Scroll | Vertical and/or horizontal delta. |
| Request context | Request ID, session context, optional timestamp. |
| File transfer | Transfer ID, filename, file size, direction, safe source/destination context, state. |
| Custom command | Command ID and permitted parameters only. |
| Screen config | Supported quality/FPS/resolution preference fields where implemented. |

Exact syntax/serialization is defined only in `API_PROTOCOL.md`.

## 16. Validation Rules

PC validates every incoming command before OS-level action:

- Command ID exists and is valid for protocol/capability version.
- Required parameters exist and types/ranges are valid.
- Optional parameters are accepted only where command allows them.
- Authorized active session is required for privileged actions.
- Command is authorized for feature/capability.
- Sequence/freshness is checked where movement/state lifecycle needs it.
- Payload size and rate are bounded where applicable.
- Custom Commands require allowlist/enabled/parameter validation.
- File commands require transfer/session/path/metadata validation.

Exact maximums, rate limits, sequence formats, and message size rules are **TBD**.

## 17. Command Responses

Conceptual response categories:

| Response | Meaning |
|---|---|
| Success | Operation completed. |
| Accepted | Operation accepted/started but final result may follow. |
| Rejected | Operation not accepted by policy/state. |
| Invalid command | Command ID/type unsupported or malformed. |
| Invalid parameters | Required/allowed parameter validation failed. |
| Unauthorized | No valid authorized session/permission. |
| Unsupported | PC capability/platform/application does not support action. |
| Busy | Feature/receiver currently cannot accept operation. |
| Timeout | Operation/peer did not complete in defined period. |
| Execution failure | PC accepted intent but local action failed. |

Response syntax is defined in `API_PROTOCOL.md`.

## 18. Command Lifecycle

```text
Mobile UI/Sensor → Command Creation → Local Validation → Network Transmission
→ PC Authentication Check → Command Validation → Authorization
→ Local Execution → Result → Mobile Feedback
```

High-frequency movement/pointer/scroll commands prioritize current-state responsiveness. They do not require individual acknowledgement because per-update response adds latency and overhead. Discrete, state-changing, file/session, presentation, and Custom Command operations require correlated result/acknowledgement where appropriate.

## 19. Ordering and Stale Commands

Conceptually handle out-of-order, duplicate, stale, post-reconnect, and old-session commands safely:

- Ignore/reject commands associated with ended/expired/unknown session.
- Do not replay stale movement after reconnect.
- Use request/transfer/session identity for discrete operation correlation.
- Avoid duplicate non-idempotent slide/custom/file/session actions from retries/double taps.
- Respect lifecycle order for drag, transfer, hosting, and session transitions.
- Ignore pointer update after pointer stop/session end.

Exact sequence/request/timestamp implementation remains **TBD** in `API_PROTOCOL.md`.

## 20. Rate and Frequency

High-frequency commands include cursor movement, drag movement, pointer updates, and scrolling. Process efficiently, avoid individual acknowledgements/UI notifications, and prevent receiver overload. Screen Hosting control is low frequency; screen media is separate stream traffic. Exact rate limits are **TBD**.

## 21. Security Classification

| Command category | Risk | Authentication | Additional validation |
|---|---|---:|---|
| Discovery-safe status | Low | No authorization | Limit exposed metadata. |
| Mouse/Trackpad/Laser | OS input | Yes | Session/freshness/state/rate validation. |
| Presentation/Media | OS/application control | Yes | Semantic command/capability/duplicate validation. |
| Custom Commands | High | Yes | Allowlist, enabled state, parameters, approved local action. |
| File Sharing | High | Yes | Transfer/session/file/path/storage/permission validation. |
| Screen Hosting | High/privacy | Yes | Stream/session/capability authorization and stop cleanup. |
| Session operations | High | Authentication-specific | OTP/session lifecycle/replay controls. |
| Calibration | Low/local | Usually no PC auth | Local sensor/profile validity. |

No unauthenticated OS-level action is allowed. See `SECURITY.md`.

## 22. Capability Handling

PC may report supported capabilities for presentation/media, volume, Screen Hosting, file transfer, laser, Custom Commands, and other feature availability using `CAPABILITY_STATUS`/safe system status. Mobile should avoid presenting a feature as usable when connected PC explicitly reports it unsupported. Capability-negotiation mechanism is **TBD**.

## 23. Versioning

Commands evolve through protocol/catalog versioning:

- Add new command IDs without redefining existing meaning.
- Deprecate rather than silently rename/remove where compatibility matters.
- Maintain aliases only if formally documented.
- Reject unknown command safely.
- Do not break older clients unnecessarily.
- Coordinate command changes with `API_PROTOCOL.md` version/capability rules.

## 24. Complete Command Registry

### MOUSE

`MOUSE_CURSOR_DELTA`, `MOUSE_LEFT_CLICK`, `MOUSE_RIGHT_CLICK`, `MOUSE_DOUBLE_CLICK`, `MOUSE_BUTTON_DOWN`, `MOUSE_BUTTON_UP`, `MOUSE_DRAG_START`, `MOUSE_DRAG_MOVE`, `MOUSE_DRAG_END`, `MOUSE_SCROLL_VERTICAL`, `MOUSE_SCROLL_HORIZONTAL`, `MOUSE_PAUSE`

### TRACKPAD

`TRACKPAD_CURSOR_DELTA`, `TRACKPAD_TAP`, `TRACKPAD_DOUBLE_TAP`, `TRACKPAD_TWO_FINGER_TAP`, `TRACKPAD_SCROLL_VERTICAL`, `TRACKPAD_SCROLL_HORIZONTAL`, `TRACKPAD_DRAG_START`, `TRACKPAD_DRAG_MOVE`, `TRACKPAD_DRAG_END`

### PRESENTATION

`NEXT_SLIDE`, `PREVIOUS_SLIDE`, `START_PRESENTATION`, `END_PRESENTATION`, `BLANK_SCREEN`

### MEDIA

`VOLUME_UP`, `VOLUME_DOWN`, `MUTE`, `PLAY_PAUSE`

### LASER_POINTER

`LASER_START`, `LASER_STOP`, `LASER_POSITION`

### SCREEN_HOSTING

`SCREEN_HOSTING_START`, `SCREEN_HOSTING_STOP`, `SCREEN_HOSTING_UPDATE_CONFIG` (supported configuration handling is TBD)

### FILE_SHARING

`TRANSFER_REQUEST`, `TRANSFER_START`, `TRANSFER_ACCEPT`, `TRANSFER_REJECT`, `TRANSFER_CANCEL`, `TRANSFER_PROGRESS`, `TRANSFER_COMPLETE`, `TRANSFER_FAILURE`

### CUSTOM_COMMAND

`CUSTOM_COMMAND_REQUEST`, `CUSTOM_COMMAND_RESULT`, `CUSTOM_COMMAND_REJECTED`

### SESSION

`AUTH_REQUEST`, `AUTH_SUCCESS`, `AUTH_FAILURE`, `SESSION_TERMINATE`, `SESSION_ENDED`, `SESSION_STATUS`, `HEARTBEAT` (if required)

### CALIBRATION

`CALIBRATION_START`, `CALIBRATION_RESULT`, `CALIBRATION_RESET`

### SYSTEM

`PC_STATUS`, `FEATURE_STATUS`, `CAPABILITY_STATUS`

## 25. Cross-Document Authority

- `COMMAND_CATALOG.md` defines semantic command meaning.
- `API_PROTOCOL.md` defines wire/message format.
- `NETWORK.md` defines networking behavior.
- `SECURITY.md` defines authorization/security rules.
- `PLATFORM_INTEGRATION.md`, if created, defines OS-specific execution.
- Feature documents define user-facing behavior.

No document silently redefines another document’s responsibility.

## Command Catalog Summary

This catalog is the authoritative semantic reference for Air Mouse command IDs. Mobile sends validated teacher intent; PC validates authorization, parameters, capability, and allowlist before performing approved local behavior. Transport and OS-specific execution remain defined elsewhere.

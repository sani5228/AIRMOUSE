# Air Mouse for College Teaching — Error Handling Requirements

## 1. Purpose

This document defines how the mobile app and PC receiver detect, communicate, display, recover from, and log errors. The goal is teacher-friendly failure handling that is safe, actionable, and non-technical.

It does not define implementation code, logging frameworks, protocols, ports, libraries, or final serialization. Such implementation choices are **TBD**. Protocol-level errors must align with `API_PROTOCOL.md`; security-sensitive behavior must align with `SECURITY.md`.

## 2. Error-Handling Principles

- **Fail safely:** Stop or reject unsafe action rather than guessing.
- **No unintended action:** Never silently perform a stale, duplicate, unauthorized, or ambiguous operation.
- **Clear feedback:** Tell the teacher what happened and, where useful, what to do next.
- **No sensitive detail:** Do not expose stack traces, raw exception messages, secrets, internal paths, addresses, or credentials.
- **Safe automatic recovery:** Retry/reconnect automatically only when repetition cannot cause unintended effect.
- **Low friction:** Avoid unnecessary confirmation dialogs for normal recovery or ordinary actions.
- **Preserve recoverable session:** Keep active session when temporary failure can safely recover.
- **State clarity:** Distinguish temporary/reconnecting/degraded state from permanent/unavailable/failed state.

## 3. Error Categories

| Category | Scope |
|---|---|
| Discovery | Finding/reaching classroom PC systems. |
| Pairing | Room registration/selection/QR-related failures. |
| Authentication | OTP validation and session-creation failures. |
| Session | Authorized session expiry, invalidation, restart, reconnect. |
| Network | Wi-Fi/reachability/timeout/address-change failures. |
| Mouse/IMU | Sensor, calibration, drift, sensor-data validity. |
| Trackpad | Touch/gesture/drag/control delivery. |
| Presentation | Semantic presentation/media command execution. |
| Laser pointer | Pointer lifecycle/rendering/updates. |
| Custom command | Allowlist/parameters/target execution. |
| File transfer | Metadata/path/storage/data/cancellation/interruption. |
| Screen mirroring | Capture/encoder/network/decoder/display/stream lifecycle. |
| Configuration | Invalid local room/settings/destination configuration. |
| Permission | Required sensor/capture/filesystem/local action access denied. |
| Unsupported feature | Device/PC/OS capability unavailable. |
| Internal/system | Receiver/application unexpected failure without technical exposure. |

## 4. Common Error Structure

Every error is represented conceptually with common data; final wire/storage format is **TBD**.

| Field | Purpose |
|---|---|
| Error code | Stable category-specific identifier. |
| Human-readable message | Short teacher-facing explanation. |
| Severity | Info, Warning, Recoverable Error, Error, or Critical. |
| Retryability | Automatic retry, manual retry, action required, or not retryable. |
| Related context | Request, session, transfer, stream, command, or room identifier where appropriate. |
| Recommended action | Retry, reconnect, enter new OTP, settings, switch feature, or dismiss. |
| Sanitized diagnostics | Optional internal detail for logs/support; never ordinary teacher UI. |

## 5. User-Facing Presentation

| Severity/use | Example | UI behavior |
|---|---|---|
| Informational | “Screen hosting stopped.” | Brief non-blocking notification. |
| Warning | “Disconnected.” | Visible status update; no technical detail. |
| Recoverable | “Connection interrupted. Reconnecting…” | Persistent temporary status; automatic recovery where safe. |
| Action required | “Invalid OTP.” / “Screen capture permission is required.” | Concise explanation and direct next action. |
| Feature failure | “Unable to start screen hosting.” | Feature-level error with Retry/Stop/Return. |
| System failure | “Air Mouse receiver is unavailable.” | Disable affected controls; offer reconnect/room selection. |

Messages remain short and understandable. Do not display stack traces, raw exception text, IP addresses, internal filesystem paths, or sensitive internal information.

## 6. Discovery Errors

| Condition | Code | User behavior and recovery |
|---|---|---|
| No Air Mouse PC found | `DISCOVERY_NO_SYSTEMS` | Show “No classroom PCs found. Check Wi-Fi and try again.” Permit refresh. |
| Room unavailable | `DISCOVERY_ROOM_UNAVAILABLE` | Keep saved room; label unavailable; offer retry/select another room. |
| Discovery timeout | `DISCOVERY_TIMEOUT` | Show search timed out; manual retry. Exact timeout is **TBD**. |
| Stale system entry | `DISCOVERY_STALE_RESULT` | Mark saved system unavailable and refresh; do not present it as connected. |
| Duplicate room number | `DISCOVERY_DUPLICATE_ROOM` | Warn teacher; require safe disambiguation, **TBD**. |
| PC unavailable after discovery | `DISCOVERY_SYSTEM_LOST` | Cancel selection/authentication as applicable; return to discovery/unavailable state. |

Automatic refresh is appropriate after network return or transient scanning failure where it does not create authentication/control effects. Teacher can always retry manually.

## 7. Pairing and Authentication Errors

| Condition | Code | Required behavior |
|---|---|---|
| Invalid room/QR data | `PAIRING_INVALID_ROOM` | Do not save/register invalid data; explain and allow rescan/correction. |
| PC unavailable | `AUTH_PC_UNAVAILABLE` | Cancel authentication safely; return to room selection/discovery. |
| Invalid OTP | `AUTH_INVALID_OTP` | Reject; show concise retry guidance. |
| Expired OTP | `AUTH_OTP_EXPIRED` | Reject; ask for currently displayed new OTP. |
| Too many attempts | `AUTH_TOO_MANY_ATTEMPTS` | Apply configured temporary protection; explain later retry is needed. Policy is **TBD**. |
| Authentication timeout | `AUTH_TIMEOUT` | Show authentication did not complete; permit retry/return. |
| Session creation failure | `AUTH_SESSION_CREATE_FAILED` | Do not enable controls; show retry/reconnect. |
| Already-active session | `SESSION_ALREADY_ACTIVE` | Apply final concurrency policy; clear safe explanation. Policy is **TBD**. |

OTP length, expiration duration, and retry limits remain **TBD**. Invalid/expired OTP never authorizes access.

## 8. Session Errors

| Condition | Code | Recovery behavior |
|---|---|---|
| Session expired | `SESSION_EXPIRED` | Disable privileged features; require reconnect/authentication as policy requires. |
| Session rejected/invalid | `SESSION_INVALID` | Reject request; return mobile to appropriate connection/authentication state. |
| PC restart | `SESSION_PC_RESTARTED` | End prior session; rediscover/re-authenticate. |
| Mobile restart | `SESSION_MOBILE_RESTARTED` | Do not assume old session valid; restore saved rooms/settings only. |
| Network disconnection | `SESSION_CONNECTION_LOST` | Enter reconnecting state when safe; stop stale input. |
| Session invalidation | `SESSION_TERMINATED` | End authorization; notify teacher. |
| Reconnection failure | `SESSION_RECONNECT_FAILED` | Show unavailable; offer retry/room selection/authentication. |
| Stale session | `SESSION_STALE` | PC rejects request; no control/transfer/stream action occurs. |

Automatic reconnection is appropriate only for safe temporary loss. Re-authentication is required when prior authorization cannot safely be retained under final session policy, which is **TBD**.

## 9. Mouse and IMU Errors

| Condition | Code | Required behavior |
|---|---|---|
| Sensor/gyroscope unavailable | `IMU_SENSOR_UNAVAILABLE` / `IMU_GYRO_UNAVAILABLE` | Disable Gyro Mouse; explain availability issue; offer Trackpad. |
| Calibration failure | `IMU_CALIBRATION_FAILED` | Do not save bad calibration; show Retry/Cancel. |
| Calibration missing | `IMU_CALIBRATION_REQUIRED` | Prompt calibrate before/when needed; preserve clear status. |
| Invalid calibration | `IMU_CALIBRATION_INVALID` | Ignore invalid calibration; request recalibration. |
| Excessive drift | `IMU_EXCESSIVE_DRIFT` | Suggest recalibration; prevent misleading uncontrolled behavior. |
| Sensor data timeout | `IMU_DATA_TIMEOUT` | Stop Gyro safely; show sensor unavailable/retry. |
| Invalid sensor readings | `IMU_INVALID_DATA` | Reject readings; prevent uncontrolled cursor movement. |

Do not invent exact thresholds. Detailed sensor requirements are in `IMU.md`; mouse behavior is in `MOUSE.md`.

## 10. Trackpad Errors

| Condition | Code | Required behavior |
|---|---|---|
| Touch input unavailable | `TRACKPAD_INPUT_UNAVAILABLE` | Disable/exit Trackpad; show concise error. |
| Invalid gesture state | `TRACKPAD_GESTURE_INVALID` | Ignore ambiguous input safely; do not perform unintended click/drag. |
| Connection lost during gesture | `TRACKPAD_CONNECTION_LOST` | Stop outgoing gesture; PC safely ends drag/click state as applicable. |
| Trackpad command failure | `TRACKPAD_COMMAND_FAILED` | Show concise feature error and maintain safe state. |

A connection failure must not leave PC in unintended continuous drag/click state. Drag lifecycle is safely terminated/cleared according to final PC controller policy.

## 11. Presentation Errors

| Condition | Code | Required behavior |
|---|---|---|
| Presentation application unavailable | `PRESENTATION_APP_UNAVAILABLE` | Explain no supported active target; keep app usable. |
| Start failure | `PRESENTATION_START_FAILED` | Show concise failure; no false active state. |
| Slide command failure | `PRESENTATION_COMMAND_FAILED` | Show command could not execute; avoid duplicate automatic replay. |
| Media command failure | `MEDIA_COMMAND_FAILED` | Show media action unavailable/failed. |
| Unsupported action | `PRESENTATION_UNSUPPORTED` | Explain current PC/application does not support action. |
| OS/application limitation | `PRESENTATION_PLATFORM_LIMITATION` | Explain PC could not perform action without technical detail. |

Semantic command result/status is returned where practical. Detailed controls are in `PRESENTATION.md`.

## 12. Laser-Pointer Errors

| Condition | Code | Required behavior |
|---|---|---|
| Pointer start failure | `LASER_START_FAILED` | Keep pointer inactive; show retry/return. |
| Renderer unavailable | `LASER_RENDERER_UNAVAILABLE` | Explain pointer unavailable on PC. |
| Update failure | `LASER_UPDATE_FAILED` | Stop/mark pointer unavailable if reliable updates cannot continue. |
| Stop failure | `LASER_STOP_FAILED` | PC must attempt safe hide/cleanup; report state accurately. |
| Connection loss active pointer | `LASER_CONNECTION_LOST` | PC safely stops/hides pointer when session ends unexpectedly. |

## 13. Custom Command Errors

| Condition | Code | Required behavior |
|---|---|---|
| Unknown command ID | `COMMAND_UNKNOWN` | PC rejects; mobile says command not allowed/unavailable. |
| Disabled command | `COMMAND_DISABLED` | Reject; show disabled state. |
| Invalid parameters | `COMMAND_INVALID_PARAMETERS` | Reject without exposing validation internals. |
| Unauthorized command | `COMMAND_NOT_ALLOWED` | Reject; require valid session where relevant. |
| Execution failure | `COMMAND_EXECUTION_FAILED` | Concise command-failed message. |
| Target unavailable | `COMMAND_TARGET_UNAVAILABLE` | Explain target application/folder unavailable. |
| Permission denied | `COMMAND_PERMISSION_DENIED` | Explain PC could not perform action. |
| Timeout | `COMMAND_TIMEOUT` | Do not claim success; permit retry where safe. |
| Duplicate/stale request | `COMMAND_DUPLICATE` / `COMMAND_STALE` | Avoid repeated local execution; report safely if needed. |

Never expose arbitrary shell execution as fallback. Requirements align with `CUSTOM_COMMANDS.md`.

## 14. File-Transfer Errors

| Condition | Code | Required behavior |
|---|---|---|
| Invalid/unsupported file | `FILE_INVALID` / `FILE_UNSUPPORTED` | Reject selection/request; allow another file. |
| Destination unavailable | `FILE_DESTINATION_UNAVAILABLE` | Do not start/continue; offer settings/retry. |
| Insufficient storage | `FILE_INSUFFICIENT_STORAGE` | Stop/reject safely; do not claim completion. |
| Permission denied | `FILE_PERMISSION_DENIED` | Do not access/write; concise error. |
| Duplicate filename | `FILE_NAME_CONFLICT` | Apply defined conflict policy; exact policy **TBD**. |
| Transfer cancelled | `FILE_TRANSFER_CANCELLED` | Show cancelled, not completed. |
| Network interruption | `FILE_TRANSFER_INTERRUPTED` | Mark interrupted/failed; retry policy **TBD**. |
| Timeout | `FILE_TRANSFER_TIMEOUT` | Mark failure/interruption; offer retry. |
| Partial transfer | `FILE_TRANSFER_PARTIAL` | Do not present as complete; cleanup policy **TBD**. |
| Corruption/failure | `FILE_TRANSFER_FAILED` | Do not finalize as success; report retry option. |
| Unauthorized transfer | `FILE_TRANSFER_UNAUTHORIZED` | Reject and require authorized session. |

No persistent transfer history is maintained. Full behavior is in `FILE_SHARING.md`.

## 15. Screen-Mirroring Errors

| Condition | Code | Required behavior |
|---|---|---|
| Capture permission failure | `SCREEN_CAPTURE_PERMISSION_DENIED` | Explain permission required/unavailable; offer settings/retry/stop. |
| Capture unavailable | `SCREEN_CAPTURE_UNAVAILABLE` | Explain device cannot currently host screen. |
| Encoder failure | `SCREEN_ENCODER_FAILED` | Stop/avoid falsely active stream; retry/return. |
| Decoder failure | `SCREEN_DECODER_FAILED` | PC stops display safely; notify mobile/PC. |
| Network interruption | `SCREEN_STREAM_INTERRUPTED` | Show reconnecting/interrupted; do not imply live display. |
| PC display failure | `SCREEN_DISPLAY_FAILED` | Explain PC cannot show stream; retry/stop. |
| Unsupported orientation | `SCREEN_ORIENTATION_UNSUPPORTED` | Explain limitation and safe fallback where available. |
| Unsupported configuration | `SCREEN_CONFIGURATION_UNSUPPORTED` | Explain selected quality/display setting unavailable. |
| Stream timeout | `SCREEN_STREAM_TIMEOUT` | Clear falsely active state; offer restart/retry. |
| Reconnection failure | `SCREEN_RECONNECT_FAILED` | Stop stream safely; offer manual restart. |

Both devices show clear **Starting**, **Active**, **Reconnecting**, **Failed**, and **Stopped** states. See `SCREEN_MIRRORING.md`.

## 16. Network Recovery

| Condition | Safe recovery |
|---|---|
| Temporary packet loss/Wi-Fi degradation | Maintain current feature if safe; show degraded/recovering only when usability is affected. |
| Network disconnect | Stop/suspend active operations safely; do not queue stale controls. |
| Network reconnect | Rediscover/reconnect under session policy; show ready only after valid recovery. |
| Changed network address | Treat old reachability as stale; rediscover rather than exposing technical address. |
| PC receiver restart | End old session/operations; rediscover and authenticate as required. |
| Mobile app restart | Restore persistent configuration only; do not replay transient commands/transfers/streams. |

Automatically retry only idempotent/safe state checks, discovery refresh, and permitted connection recovery. Do **not** automatically repeat potentially non-idempotent Custom Commands, presentation actions, transfer creation/completion, or session-creation operations merely because an earlier result was uncertain.

## 17. Logging and Diagnostics

Logs support debugging without creating a privacy/security risk.

### Loggable information

- Timestamp.
- Component/category.
- Error code.
- Operation/feature.
- Severity.
- Sanitized request, transfer, stream, or session identifier where appropriate.
- Sanitized technical detail needed for diagnosis.
- Recovery action/outcome where useful.

### Do not log

- OTP values.
- Credentials/session secrets.
- Sensitive file contents or unnecessary filenames/paths.
- Screen contents/frames/recordings.
- Unnecessary personal information.
- Raw unrestricted command text, stack traces, or sensitive network details in teacher-visible logs.

Log retention, diagnostic export, access control, and storage mechanism are **TBD**. Logs should be bounded/retained only as long as operationally justified.

## 18. Severity Levels

| Level | Meaning | User notification/recovery |
|---|---|---|
| Info | Normal meaningful lifecycle change, not a failure. | Brief non-blocking notification or status update. |
| Warning | Degraded/non-fatal condition. | Visible status; continue if safe. |
| Recoverable Error | Current operation interrupted but safe automatic/manual recovery exists. | Reconnecting/retry state and clear action. |
| Error | Feature/request cannot complete without user action or changed conditions. | Clear feature error with Retry/Settings/Return. |
| Critical | Receiver/session safety or core system unavailable. | Disable affected control; clear unavailable/reconnect guidance; safe cleanup. |

## 19. Error-Code Conventions

Use uppercase, category-prefixed, underscore-separated conceptual codes:

```text
<CATEGORY>_<SPECIFIC_CONDITION>
```

Examples:

- `AUTH_INVALID_OTP`
- `AUTH_OTP_EXPIRED`
- `SESSION_EXPIRED`
- `NETWORK_DISCONNECTED`
- `IMU_CALIBRATION_FAILED`
- `FILE_TRANSFER_FAILED`
- `SCREEN_STREAM_FAILED`
- `COMMAND_NOT_ALLOWED`

Codes remain stable enough for mobile/PC/protocol correlation, while teacher UI uses simple localized human-readable messages. Exact registry/governance is **TBD**.

## 20. Testing Requirements

Test every major error category and verify both system behavior and teacher-facing message quality.

| Area | Required tests |
|---|---|
| Discovery/pairing | No PC, stale room, duplicate room, invalid QR/room, PC loss during selection. |
| Authentication/session | Wrong/expired/repeated OTP, timeout, session creation failure, expiry, stale session, restart. |
| Network | Temporary degradation, disconnect, reconnect, address change, PC/mobile restart. |
| Mouse/IMU | Missing sensor, permission failure, invalid readings, calibration failure, drift/data timeout. |
| Trackpad | Invalid gesture, connection loss during drag, command failure. |
| Presentation/laser | Unsupported/unavailable app/rendering, command failure, session loss. |
| Custom Commands | Unknown/disabled/unauthorized/invalid parameter, target unavailable, duplicate/stale request. |
| File transfer | Invalid file, destination/storage/permission/name failure, cancellation, interruption, partial file, unauthorized request. |
| Screen Hosting | Permission/capture/encoder/decoder/display/configuration failure, interruption, timeout, recovery failure. |
| UX/logging | Temporary versus permanent messaging, automatic/manual retry, log sanitization, no sensitive UI details. |

## 21. Documentation Boundaries

- `SECURITY.md` defines authorization/privacy boundaries.
- `API_PROTOCOL.md` defines protocol-level message/error contract.
- `PAIRING.md` and `NETWORK.md` define connection/recovery lifecycle.
- `MOUSE.md` and `IMU.md` define mouse/sensor behavior.
- `PRESENTATION.md`, `FILE_SHARING.md`, `SCREEN_MIRRORING.md`, and `CUSTOM_COMMANDS.md` define feature-specific requirements.

## 22. Acceptance Criteria

- [ ] Error categories cover discovery, pairing/authentication, session, network, mouse/IMU, Trackpad, presentation, laser, Custom Commands, file transfer, Screen Hosting, configuration, permission, unsupported features, and system failures.
- [ ] Common conceptual error structure is defined.
- [ ] Teacher-facing messages are concise, actionable, and non-sensitive.
- [ ] Temporary and permanent failures are clearly distinguished.
- [ ] Safe automatic recovery and manual retry rules are defined.
- [ ] Authentication/session/restart/stale-session behavior is defined.
- [ ] Mouse/IMU and Trackpad errors fail safely without unintended PC input.
- [ ] Presentation, laser, and Custom Command failures are handled without arbitrary execution fallback.
- [ ] File transfer never marks incomplete data as completed and maintains no transfer history.
- [ ] Screen Hosting accurately shows starting, active, reconnecting, failed, and stopped states.
- [ ] Network recovery avoids duplicate commands, sessions, and transfers.
- [ ] Logs are useful but sanitized and bounded conceptually.
- [ ] Severity levels and consistent error-code conventions are defined.
- [ ] Practical error, recovery, UX, and log-sanitization tests are defined.

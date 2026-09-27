# Air Mouse for College Teaching — API and Communication Protocol Contract

## 1. Protocol Goals

This document defines the technology-independent communication contract between the mobile Air Mouse application and the Air Mouse PC receiver over direct local Wi-Fi.

The protocol supports:

- PC discovery and room identification.
- OTP authentication, session establishment, expiry, and termination.
- Gyro/IMU mouse control, Trackpad control, clicks, drag, and scrolling.
- Presentation/media controls.
- Laser pointer control.
- Custom Commands.
- File-transfer control.
- Screen-mirroring session control.
- Status, health, acknowledgements, and errors.

It clearly separates control messages, file-transfer data, and screen-stream data. Programming language, framework, transport, port, serialization format, networking library, encryption mechanism, and final wire representation are **TBD**.

## 2. Communication Model

```text
Mobile App ↔ Air Mouse PC Receiver
```

| Interaction | Meaning |
|---|---|
| Request | Sender asks receiver to perform, validate, or begin an operation. |
| Response | Direct outcome for a request where a response is applicable. |
| Event | Asynchronous state/status update sent when something changes. |
| Acknowledgement | Confirms receipt/acceptance where confirmation is needed. |
| Error | Structured rejection/failure with safe application-readable details. |
| Session state | Connection/authorization state governing whether privileged messages are accepted. |

Not every high-frequency Gyro, Trackpad, laser-position, or other movement message needs an individual response. Per-message acknowledgement for such traffic can introduce unnecessary latency. Those messages prioritize current-state responsiveness and stale-input prevention; discrete actions use acknowledgement/result behavior where appropriate.

## 3. Traffic Classes

| Class | Includes | Requirement |
|---|---|---|
| Discovery | Discovery query/advertisement/result | Local reachability and room identification; no authorization. |
| Authentication/session | OTP/session requests, results, lifecycle events | Authorizes privileged usage; reliable clear outcome. |
| Low-latency control | Mouse deltas, clicks, scrolling, laser, presentation, Custom Command requests | Small/timely messages; duplicate/stale protection where needed. |
| File-transfer control | Request, acceptance, metadata, progress, cancellation, completion | Reliable state management; actual bytes are separate. |
| File-transfer data | File contents | Appropriate reliable data-transfer mechanism; not ordinary controls. |
| Screen-stream control | Start/stop/status/recovery | Authorized stream lifecycle management. |
| Screen-stream data | Encoded frames/video | Streaming-oriented path; not ordinary controls. |
| Status/health | PC/session/feature state, heartbeat if needed | Minimal overhead; no unnecessary continuous traffic. |

## 4. Conceptual Message Envelope

Every ordinary control-plane message uses a conceptual envelope. Exact serialization is **TBD**.

| Field | Required | Purpose |
|---|---:|---|
| Protocol version | Yes | Identifies compatible protocol contract version. |
| Message type | Yes | Identifies semantic request, response, event, acknowledgement, or error. |
| Message ID / Request ID | Context-dependent | Correlates discrete request/result and supports duplicate handling. |
| Session ID | Required for privileged session operations | Associates message with current authorized session. Not required for discovery/initial authentication as defined by final design. |
| Timestamp | Context-dependent | Supports freshness/ordering where useful, especially stateful/discrete requests. |
| Payload | Yes when message needs data | Contains message-specific semantic fields. |
| Error information | Error messages only | Safe code/category and non-sensitive details. |

The envelope must not require implementation-specific fields in the conceptual contract. Exact field names, encoding, size limits, timestamp format, and validation rules are **TBD**.

## 5. Message Categories

| Category | Direction | Purpose | Required fields | Response/result |
|---|---|---|---|---|
| Discovery | Mobile ↔ PC | Find reachable room systems | Version, type, room/status data as applicable | Discovery result/event |
| Authentication | Mobile → PC / PC → Mobile | OTP validation and session outcome | Type, selected room, OTP when submitting | Success or failure |
| Session | Both directions | Terminate/expire/report authorized session state | Type, session ID where applicable | Ack/event/error |
| Mouse/Trackpad | Mobile → PC | Authorized pointer/click/scroll behavior | Session ID, control payload | No per-movement response required; errors/status as needed |
| Presentation | Mobile → PC | Semantic presentation/media action | Session ID, request ID, command ID | Ack/result/error |
| Laser | Mobile → PC / PC → Mobile | Pointer lifecycle and position/status | Session ID, pointer state/position | Status/error as needed |
| Custom Command | Mobile → PC / PC → Mobile | Request allowlisted local action and report result | Session ID, request ID, command ID, permitted parameters | Success/failure/rejection |
| File-transfer control | Both directions | Transfer lifecycle | Session ID, transfer ID, metadata/state | Acceptance/progress/completion/failure |
| Screen-stream control | Both directions | Stream lifecycle | Session ID, stream state/request ID as applicable | Ready/status/failure/stop |
| Status/health | Both directions | Connection/session/feature health | Type, status payload, session ID where applicable | Event/ack where required |

## 6. Discovery Messages

Discovery is local-network focused and does not grant access. Teacher-facing result uses room number and reachability/status, not IP addresses, ports, MAC addresses, or internal IDs.

| Message | Direction | Purpose | Required conceptual payload | Result |
|---|---|---|---|---|
| `DISCOVERY_QUERY` | Mobile → local discovery environment | Look for reachable Air Mouse PCs | Protocol version; optional discovery scope | One or more `DISCOVERY_RESULT` messages/events |
| `DISCOVERY_ANNOUNCEMENT` | PC → local discovery environment | Advertise reachable receiver | Room number; reachability/status; internal identity only if protocol requires it | Discoverable receiver |
| `DISCOVERY_RESULT` | PC → Mobile | Identify reachable receiver | Room number; availability/reachability; optional opaque internal identity | Display/selectable system |
| `DISCOVERY_EXPIRED` | Mobile-local state / optional PC event | Mark stale result unavailable | Prior result identity; unavailable status | UI updates room unavailable |

Duplicate room numbers must not be silently resolved. Discovery result handling must identify conflict and require safe disambiguation; exact mechanism is **TBD**. Stale results expire/refresh rather than remaining falsely available.

## 7. Authentication and Session Messages

| Message | Direction | Purpose | Required conceptual payload | Response/result |
|---|---|---|---|---|
| `ROOM_SELECTED` | Mobile → PC | Indicates teacher selected reachable room/receiver | Selected room and receiver context as needed | PC prepares authentication state |
| `OTP_AVAILABLE` | PC → Mobile/UI state | Indicates temporary OTP is displayed on PC | Room number; expiry/status; OTP itself is displayed on PC for teacher entry and need not be transmitted to mobile before submission | Mobile OTP input state |
| `AUTH_REQUEST` | Mobile → PC | Submit OTP for selected room | Room context; submitted OTP; request ID; context required by final design | `AUTH_SUCCESS` or `AUTH_FAILURE` |
| `AUTH_SUCCESS` | PC → Mobile | Confirm authorized session | Session context/ID; session status; permitted feature/capability information if supported | Mobile enables authorized features |
| `AUTH_FAILURE` | PC → Mobile | Reject authentication | Error category: invalid OTP, expired OTP, too many attempts, PC unavailable, authentication failed | Mobile retry/return UI |
| `SESSION_TERMINATE` | Mobile ↔ PC | Explicitly end session | Session ID; request ID where applicable | Acknowledgement/session-ended event |
| `SESSION_ENDED` | PC → Mobile | Notify session is no longer usable | Session context; reason category | Mobile disables privileged controls |
| `SESSION_EXPIRED` | PC → Mobile | Notify authorization expiry | Session context; expiry state | Reconnect/re-authenticate flow |
| `SESSION_INVALID` | PC → Mobile | Reject request with invalid/stale session | Session context when safely reportable; error category | Mobile returns to appropriate connection state |

Exact OTP length, generation, expiry duration, retry rules, session ID form, token design, and transport protection are **TBD**. Authentication must not be inferred from QR scan, room selection, or local Wi-Fi membership.

## 8. Mouse and Trackpad Protocol

Mouse messages are authorized control messages. High-frequency movement should be optimized for low latency and generally does not require a response for every update.

| Message | Direction | Purpose | Required conceptual payload | Response/result |
|---|---|---|---|---|
| `GYRO_MODE_START` | Mobile → PC | Start/activate Gyro Mouse control | Session ID; optional mode/config state | Status/error if needed |
| `GYRO_MODE_STOP` | Mobile → PC | Stop Gyro Mouse control | Session ID | Status/error if needed |
| `GYRO_CURSOR_DELTA` | Mobile → PC | Relative cursor movement from processed gyro input | Session ID; relative delta; freshness/order context as needed | No per-message ack required |
| `MOUSE_PAUSE` | Mobile → PC | Lock/pause gyro cursor movement | Session ID; paused state | Status/error if needed |
| `CALIBRATION_STATUS` | Mobile ↔ PC where needed | Report/request calibration-related state | Session ID when applicable; state/result | Status/error; detailed IMU processing is outside protocol |
| `TRACKPAD_CURSOR_DELTA` | Mobile → PC | Relative touchpad cursor movement | Session ID; relative delta; freshness/order context | No per-message ack required |
| `MOUSE_LEFT_CLICK` | Mobile → PC | Left click | Session ID; request ID | Ack/result/error where applicable |
| `MOUSE_RIGHT_CLICK` | Mobile → PC | Right click | Session ID; request ID | Ack/result/error where applicable |
| `MOUSE_DOUBLE_CLICK` | Mobile → PC | Double click | Session ID; request ID | Ack/result/error where applicable |
| `MOUSE_DRAG_START` | Mobile → PC | Begin drag | Session ID; request ID/context | Ack/status/error |
| `MOUSE_DRAG_MOVE` | Mobile → PC | Continue drag with relative movement | Session ID; delta; freshness context | No per-message ack required |
| `MOUSE_DRAG_END` | Mobile → PC | Release drag | Session ID; request ID/context | Ack/status/error |
| `MOUSE_SCROLL_VERTICAL` | Mobile → PC | Vertical scrolling | Session ID; relative scroll amount | No per-message ack required |
| `MOUSE_SCROLL_HORIZONTAL` | Mobile → PC | Horizontal scrolling | Session ID; relative scroll amount | No per-message ack required |

Exact coordinate unit, delta scaling, movement frequency, ordering, safety bounds, and retransmission behavior are **TBD**. PC rejects mouse/control input from unauthorized or invalid sessions. Details of IMU processing belong in `IMU.md`; user behavior is in `MOUSE.md`.

## 9. Presentation Protocol

Presentation commands are semantic. PC maps them to local OS/application behavior; mobile must not send arbitrary keyboard shortcuts or shell commands as default protocol.

| Message/command ID | Direction | Purpose | Required conceptual payload | Result |
|---|---|---|---|---|
| `NEXT_SLIDE` | Mobile → PC | Advance presentation one slide/step | Session ID; request ID | Ack/result/error |
| `PREVIOUS_SLIDE` | Mobile → PC | Move back one slide/step | Session ID; request ID | Ack/result/error |
| `START_PRESENTATION` | Mobile → PC | Start/activate presentation | Session ID; request ID | Ack/result/error |
| `END_PRESENTATION` | Mobile → PC | End/exit presentation | Session ID; request ID | Ack/result/error |
| `BLANK_SCREEN` | Mobile → PC | Activate/toggle blank screen as supported | Session ID; request ID | Ack/result/error/state if available |
| `VOLUME_UP` | Mobile → PC | Raise PC/system volume | Session ID; request ID | Ack/result/error |
| `VOLUME_DOWN` | Mobile → PC | Lower PC/system volume | Session ID; request ID | Ack/result/error |
| `MUTE` | Mobile → PC | Toggle/set mute as supported | Session ID; request ID | Ack/result/error/state if available |
| `PLAY_PAUSE` | Mobile → PC | Toggle media play/pause | Session ID; request ID | Ack/result/error/state if available |

Application focus, mapping, capability detection, and PC execution are defined in `PRESENTATION.md` and remain **TBD** where platform-specific.

## 10. Laser Pointer Protocol

| Message | Direction | Purpose | Required conceptual payload | Result |
|---|---|---|---|---|
| `LASER_START` | Mobile → PC | Start laser pointer rendering/control | Session ID; request ID | Pointer status/error |
| `LASER_STOP` | Mobile → PC | Stop laser pointer | Session ID; request ID | Pointer status/error |
| `LASER_POSITION` | Mobile → PC | Update pointer position | Session ID; position data; freshness/order context | No per-update ack required |
| `LASER_STATUS` | PC → Mobile | Report pointer active/inactive/failure | Session ID; state; error if relevant | UI state update |

Pointer updates prioritize low latency. Exact coordinate representation, scaling, coordinate frame, and update strategy are **TBD**.

## 11. Custom Command Protocol

Custom Commands are semantic allowlisted PC actions, never arbitrary shell/terminal execution.

| Message | Direction | Purpose | Required conceptual payload | Result |
|---|---|---|---|---|
| `CUSTOM_COMMAND_REQUEST` | Mobile → PC | Request approved command execution | Session ID; request ID; command ID; optional permitted parameters | `CUSTOM_COMMAND_RESULT` or error |
| `CUSTOM_COMMAND_RESULT` | PC → Mobile | Report execution outcome | Request ID; command ID; success/failure/rejection status; safe user-facing detail | UI notification/state |
| `CUSTOM_COMMAND_REJECTED` | PC → Mobile | Explicit reject | Request ID; error category: unknown, disabled, invalid parameters, unauthorized | UI error |

PC validates authorized session, command ID, allowlist membership, enabled state, and permitted parameters. Protocol must not accept raw shell text, terminal text, script text, or arbitrary executable path as a normal request payload. Detailed behavior is in `CUSTOM_COMMANDS.md`.

## 12. File-Transfer Protocol

This section defines file-transfer **control-plane** contract. Actual file bytes use an appropriate reliable transfer mechanism separate from ordinary control messages; final transfer transport is **TBD**.

| Message | Direction | Purpose | Required conceptual payload | Result |
|---|---|---|---|---|
| `TRANSFER_REQUEST` | Mobile ↔ PC | Request Phone→PC or PC→Phone transfer | Session ID; request ID; transfer ID; direction; metadata; destination context where appropriate | Accept/reject |
| `TRANSFER_ACCEPTED` | Receiver → Sender | Accept transfer | Transfer ID; active state; approved destination context as safe to reveal | Start data transfer |
| `TRANSFER_REJECTED` | Receiver → Sender | Reject transfer | Transfer ID/request ID; safe error category | No data transfer |
| `TRANSFER_START` | Sender ↔ Receiver | Signal data transfer beginning | Transfer ID; state | Active transfer |
| `TRANSFER_PROGRESS` | Sender/receiver → peer | Report meaningful progress | Transfer ID; progress/size/state | UI progress update |
| `TRANSFER_CANCEL` | Either direction | Request cancellation | Transfer ID; request ID; reason if applicable | Cancelled result |
| `TRANSFER_CANCELLED` | Either direction | Confirm cancellation | Transfer ID; cancelled state | UI cancelled state |
| `TRANSFER_COMPLETE` | Receiver → Sender | Confirm successful finalized transfer | Transfer ID; completed state | Completion notification |
| `TRANSFER_FAILURE` | Either direction | Report failed/interrupted transfer | Transfer ID; error category; safe detail | Failure/retry UI |

Useful metadata includes transfer ID, filename, file size, direction, destination information where appropriate, and transfer state. PC validates session, filename/path safety, configured destination, permissions, and storage conditions. Do not expose arbitrary filesystem paths. Detailed requirements are in `FILE_SHARING.md`.

## 13. Screen-Mirroring Protocol

Screen Hosting control is separate from actual encoded frame/video data.

| Message | Direction | Purpose | Required conceptual payload | Result |
|---|---|---|---|---|
| `SCREEN_HOSTING_START` | Mobile → PC | Request full mobile-screen stream start | Session ID; request ID; offered stream/config context as applicable | Accepted/ready/rejected |
| `SCREEN_STREAM_READY` | PC ↔ Mobile | Indicate authorized stream path is ready | Session ID; stream/session context; safe capability/status data | Mobile starts/sustains stream |
| `SCREEN_STREAM_STATUS` | PC ↔ Mobile | Report starting, active, degraded, stopped, or recovery state | Session ID; state; safe metrics/status where appropriate | UI update |
| `SCREEN_HOSTING_STOP` | Mobile ↔ PC | Stop current stream | Session ID; request ID | Stopped status |
| `SCREEN_STREAM_FAILURE` | PC ↔ Mobile | Report capture/encoder/network/decoder/display failure | Session ID; safe error category | Retry/stop/recovery UI |
| `SCREEN_STREAM_RECOVER` | Mobile ↔ PC | Coordinate recovery/restart where permitted | Session ID; recovery context | Ready/failure status |

Actual screen video/frame stream uses streaming-oriented communication and must not be embedded in ordinary control messages. Codec, transport, bitrate/FPS/resolution details, and recovery mechanics are **TBD**. See `SCREEN_MIRRORING.md`.

## 14. Status and Health Messages

| Message | Direction | Purpose | Required conceptual payload | Response/result |
|---|---|---|---|---|
| `PC_STATUS` | PC → Mobile | Receiver availability, room/status, supported feature availability | Room context; availability; safe feature status | Mobile UI update |
| `SESSION_STATUS` | PC ↔ Mobile | Connected, authenticating, expired, reconnecting, ended state | Session ID where applicable; state | UI update |
| `FEATURE_STATUS` | PC ↔ Mobile | Feature state such as mouse, presentation, transfer, hosting | Session ID; feature name; state | UI update |
| `HEARTBEAT` | Mobile ↔ PC | Minimal keep-alive/health signal where required | Session context; optional timestamp | Optional acknowledgement/status |
| `HEALTH_STATUS` | PC ↔ Mobile | Report receiver/session health where useful | State; safe health category | UI/recovery update |

Heartbeat/keep-alive is used only where required by final connection design. Frequency, timeout, and failure detection are **TBD**. Avoid unnecessary heartbeat traffic, especially when no active session/feature requires it.

## 15. Acknowledgement and Error Model

### Acknowledgements

Acknowledgements are used for session operations, discrete presentation actions, clicks/drag boundaries where needed, Custom Commands, transfer lifecycle, and Screen Hosting lifecycle. High-frequency movement/position updates generally do not receive individual acknowledgement.

### Error structure

Conceptual error information contains an error category/code, safe application-readable message/detail, related message/request/transfer context where applicable, and recommended next action where useful. Exact structure is **TBD**.

| Error category | Meaning |
|---|---|
| `INVALID_MESSAGE` | Envelope/payload malformed or missing required fields. |
| `UNSUPPORTED_VERSION` | Protocol version unsupported. |
| `UNAUTHORIZED_SESSION` | No authorized session for sensitive operation. |
| `INVALID_SESSION` | Session unknown, stale, ended, or otherwise unusable. |
| `SESSION_EXPIRED` | Authorization/session expired. |
| `INVALID_OTP` | Submitted OTP incorrect. |
| `OTP_EXPIRED` | Submitted OTP expired. |
| `TOO_MANY_ATTEMPTS` | Authentication temporarily limited under final policy. |
| `UNKNOWN_COMMAND` | Command ID unrecognized. |
| `COMMAND_DISABLED` | Known command currently disabled. |
| `INVALID_PARAMETERS` | Request parameters not allowed/valid. |
| `RESOURCE_UNAVAILABLE` | Required PC/app/sensor/display resource unavailable. |
| `TRANSFER_FAILURE` | Transfer cannot complete. |
| `SCREEN_STREAM_FAILURE` | Hosting/stream cannot start or continue. |
| `TIMEOUT` | Operation/peer did not respond in defined time. |
| `NETWORK_FAILURE` | Network/reachability interruption. |

Errors help applications recover but must not leak sensitive internal details, credentials, raw filesystem paths, stack traces, or unnecessary network metadata.

## 16. Reliability, Ordering, and Idempotency

### Request IDs and duplicates

Discrete requests use request IDs where appropriate to correlate outcomes and reduce duplicate execution from retries, delay, or UI double taps. Exact ID generation/storage/expiry is **TBD**.

### Retry and stale behavior

| Traffic | Reliability behavior |
|---|---|
| High-frequency mouse/laser movement | Prioritize most-current state and low latency. Do not retransmit obsolete movement indefinitely. |
| Click/drag boundaries | Require safe ordering/result handling as needed; avoid duplicate clicks/drag start/end. |
| Presentation commands | Avoid automatic duplicate/replay; correlate request/result where applicable. |
| Custom Commands | Do not execute duplicate/stale request more than intended; use deduplication/idempotency policy. |
| File-transfer operations | Lifecycle requests use transfer/request identity; avoid duplicate transfer creation/completion. |
| Session operations | Avoid duplicate session creation/termination effects; reject stale auth/session requests. |
| Screen-hosting lifecycle | Avoid duplicate stream starts/stops and stale recovery after session end. |

Commands that cause non-idempotent local effects—such as slide navigation, launching approved applications, or ending a presentation—must not be automatically repeated merely because delivery/result is uncertain. Exact retry policy is **TBD**.

Ordering matters for drag lifecycle, session transitions, transfer lifecycle, Screen Hosting lifecycle, and state transitions. Exact ordering mechanism is **TBD**.

## 17. Versioning and Capability Negotiation

Every message includes conceptual protocol version. The system must:

- Reject/handle unsupported versions safely.
- Preserve backward compatibility where practical.
- Permit optional feature capability negotiation, such as Screen Hosting availability or supported command features.
- Avoid enabling unsupported behavior merely because a peer is discovered.
- Evolve by adding optional message types/fields in a compatible way where possible.

Exact version syntax, compatibility rules, negotiation message, and deprecation policy are **TBD**.

## 18. Security Requirements

The protocol respects `SECURITY.md`:

- Discovery is not authorization.
- QR room registration is not authentication.
- Sensitive operations require authorized current session.
- Session context is validated by PC.
- Custom Commands are allowlisted and parameter-validated.
- File transfers are authorized and path-safe.
- Screen streaming is authorized.
- Invalid, unknown, unauthorized, expired, or stale messages are rejected.
- Arbitrary shell/terminal execution is not part of default protocol.

Encryption, authentication algorithms, transport protection, session-token construction, replay protection, and peer-verification implementation are **TBD** and must be selected consistently with the local-network threat model.

## 19. Conceptual Examples

The following JSON-like examples are conceptual only. JSON is not selected as final serialization format.

### Authentication request and success

```text
Mobile → PC
{
  protocolVersion: <supported version>,
  type: AUTH_REQUEST,
  requestId: <request identifier>,
  payload: {
    roomNumber: "Room 204",
    otp: "<teacher-entered temporary OTP>"
  }
}

PC → Mobile
{
  protocolVersion: <supported version>,
  type: AUTH_SUCCESS,
  requestId: <same request identifier>,
  payload: {
    sessionId: <authorized session identifier>,
    status: "CONNECTED"
  }
}
```

### Presentation command

```text
Mobile → PC
{
  protocolVersion: <supported version>,
  type: NEXT_SLIDE,
  requestId: <request identifier>,
  sessionId: <authorized session identifier>,
  payload: {}
}

PC → Mobile
{
  protocolVersion: <supported version>,
  type: ACKNOWLEDGEMENT,
  requestId: <same request identifier>,
  sessionId: <authorized session identifier>,
  payload: { status: "ACCEPTED" }
}
```

### Custom Command request

```text
Mobile → PC
{
  protocolVersion: <supported version>,
  type: CUSTOM_COMMAND_REQUEST,
  requestId: <request identifier>,
  sessionId: <authorized session identifier>,
  payload: {
    commandId: "OPEN_CALCULATOR",
    parameters: <only command-permitted parameters>
  }
}
```

PC validates allowlist/parameters and returns `CUSTOM_COMMAND_RESULT` or `CUSTOM_COMMAND_REJECTED`.

### File-transfer request

```text
Mobile → PC
{
  protocolVersion: <supported version>,
  type: TRANSFER_REQUEST,
  requestId: <request identifier>,
  sessionId: <authorized session identifier>,
  payload: {
    transferId: <transfer identifier>,
    direction: "PHONE_TO_PC",
    filename: "<selected file name>",
    fileSize: <known size if available>
  }
}
```

Actual file bytes are not included in this ordinary control-plane example.

## 20. Documentation Boundaries

- `ARCHITECTURE.md` defines components and communication planes.
- `PAIRING.md` defines room registration, discovery, OTP, and connection lifecycle.
- `NETWORK.md` defines network behavior and traffic priorities.
- `SECURITY.md` defines security requirements.
- `MOUSE.md` and `IMU.md` define mouse/IMU behavior.
- `PRESENTATION.md` defines presentation/media behavior.
- `FILE_SHARING.md` defines transfer behavior/path safety.
- `SCREEN_MIRRORING.md` defines screen-hosting behavior.
- `CUSTOM_COMMANDS.md` defines allowlisted command behavior.

## 21. Acceptance Criteria

A developer can use this contract to determine:

- [ ] What conceptual messages exist and who sends them.
- [ ] What each message means, which fields it carries, and which result/error applies.
- [ ] Which traffic is low-latency control, file-transfer data, or screen-stream data.
- [ ] How discovery, OTP authentication, session establishment, expiry, and termination work.
- [ ] How mouse, Trackpad, presentation, laser, Custom Commands, file transfer, and Screen Hosting are represented.
- [ ] Why high-frequency movement does not require individual acknowledgement.
- [ ] Which operations require reliable acknowledgement/result handling.
- [ ] How errors avoid exposing sensitive implementation details.
- [ ] How request IDs, stale messages, ordering, duplicates, retries, and non-idempotent actions are handled conceptually.
- [ ] How version compatibility and future capability changes can evolve.
- [ ] Which implementation choices remain **TBD**.

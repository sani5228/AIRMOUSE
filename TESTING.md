# Air Mouse for College Teaching — Testing Strategy

## 1. Testing Objectives

Testing verifies that Air Mouse is reliable, secure, responsive, and usable in a real college classroom. It covers both the mobile application and PC receiver.

Objectives:

- Correct feature functionality.
- Low-latency control and responsive cursor/pointer behavior.
- Reliable local Wi-Fi communication and recovery.
- Secure pairing, OTP authentication, and session boundaries.
- Stable sessions and safe disconnect/reconnect behavior.
- Accurate Gyro Mouse and usable Trackpad behavior.
- Presentation/media controls, laser pointer, file sharing, real-time Screen Hosting, and Custom Commands.
- Error recovery and truthful status/error messages.
- Teacher-friendly UI/UX during an actual lecture.

Testing is practical for a college-project prototype. Numerical targets are **TBD** until established experimentally; tests should record observed values and usability outcomes.

## 2. Test Environments

| Area | Required coverage | Status/details |
|---|---|---|
| Mobile | Supported phone classes, screen sizes, portrait/landscape, sensor availability, and relevant Android/platform versions. | Supported devices/versions are **TBD**. |
| PC | Supported desktop operating systems/environments, display configurations, and varied hardware capability levels. | Supported OS/platform matrix is **TBD**. |
| Network | Normal classroom Wi-Fi, strong Wi-Fi, weak Wi-Fi, temporary loss, reconnection, different Wi-Fi networks, guest network, and AP/client isolation where available. | Exact infrastructure is **TBD**. |
| Classroom setup | Teacher standing away from PC/projector, typical lecture movement, room-number registration, and presentation display. | Use available classroom/lab environment. |

Record environment details for every significant test run without storing sensitive addresses, credentials, OTPs, or personal information.

## 3. Test Levels

| Level | Purpose |
|---|---|
| Unit testing | Validate isolated logical components and state transitions. |
| Integration testing | Verify mobile↔PC communication and feature interaction. |
| System testing | Verify complete behavior on real devices/network. |
| Regression testing | Re-run critical flows after major changes. |
| Acceptance testing | Verify classroom-oriented end-to-end teaching workflow. |
| Exploratory usability testing | Observe teacher interaction, discover confusion, and validate simple UI. |

No specific testing framework is required. Framework/tool choices are **TBD**.

## 4. Unit Testing

Unit tests cover logical behavior without requiring complete device-to-PC operation.

| Component | Required unit tests |
|---|---|
| Room validation | Valid/invalid/empty room values; QR registration data contains room number only. |
| Session state | Valid state transitions, expiry, termination, stale-state rejection. |
| OTP validation | Correct/incorrect/expired/reused-attempt logic according to final policy. |
| IMU calculation | Calibration calculation, bias/offset application, timestamp handling. |
| Sensor processing | Filtering, drift handling, stationary detection, invalid sample handling. |
| Axis mapping | XY, XZ, YZ selection. |
| Inversion | Independent X/Y/Z inversion. |
| Sensitivity/acceleration | Individual/global sensitivity and acceleration transformation. |
| Dead zone | Below/above threshold behavior while retaining useful small movement. |
| Gesture recognition | Tap, double tap, two-finger tap, drag, vertical/horizontal scroll, ambiguity handling. |
| Command validation | Semantic command validity/session checks. |
| Custom Command allowlist | Known/unknown/disabled command and permitted parameter validation. |
| File/path validation | Filename validation, destination boundary, traversal/path escape rejection. |
| Transfer state | Valid prepare/start/progress/cancel/complete/failure transitions. |
| Error mapping | Feature condition maps to consistent code/severity/user message. |

## 5. Integration Testing

Integration testing verifies communication and feature interoperability.

| Integration | Required verification |
|---|---|
| Mobile ↔ PC discovery | Saved/reachable rooms appear correctly; stale/duplicate results are handled. |
| Mobile ↔ PC authentication | OTP success/failure creates or rejects authorized session correctly. |
| Mobile ↔ PC session | Session gates controls and terminates safely. |
| Mobile → mouse control | Gyro/Trackpad events reach PC only through authorized session. |
| Mobile → presentation | Semantic commands execute or fail truthfully. |
| Mobile → laser pointer | Start/update/stop behavior and active status. |
| Mobile ↔ file transfer | Both directions, progress, cancellation, failure, no history. |
| Mobile ↔ Screen Hosting | Start/control/stop/recovery and display state. |
| Mobile → Custom Commands | PC allowlist validation and result handling. |

Test feature coexistence: for example, mouse control during file transfer, presentation controls during Screen Hosting, and session termination during active transfer/stream.

## 6. Pairing and Authentication Tests

| Test focus | Required tests |
|---|---|
| First-time setup | PC first launch, room-number entry, QR generation/display, waiting state. |
| QR registration | QR scanning, room saved, invalid QR, re-scan existing room. |
| Discovery | Saved room reachable/unreachable, multiple rooms, duplicate room number. |
| OTP | Generation/display, correct OTP, incorrect OTP, expired OTP, repeated incorrect attempts. |
| Session | Successful creation, feature enablement, explicit termination, expiry/invalidation. |
| Restart/recovery | PC restart, mobile restart, network reconnect, re-authentication behavior. |
| Security | QR-only scan never grants cursor/control/file/stream access. |

OTP lengths, expirations, retry thresholds, and lockout policy are **TBD**; tests verify the policy ultimately selected.

## 7. Gyro/IMU Mouse Testing

Test accuracy, stability, responsiveness, and safe degradation.

| Area | Required tests |
|---|---|
| Calibration | Initial calibration, recalibration, failed calibration, reset. |
| Axis modes | XY, XZ, YZ. |
| Inversion | Independent X, Y, Z inversion and combinations. |
| Sensitivity | Individual X/Y/Z and overall sensitivity changes. |
| Movement | Slow, fast, large, and small intentional movement. |
| Processing | Acceleration, dead zone, filtering, drift compensation, stationary detection. |
| Stability | Stationary phone, sensor noise, long-duration use, excessive drift. |
| Failure | Sensor unavailable, permission denied, invalid data, data timeout. |
| UX | Calibration main-screen access, clear active/paused state, Trackpad fallback. |

Do not define arbitrary numerical pass/fail thresholds before experimental validation. Record observed cursor smoothness, jitter, drift, latency, and teacher usability.

## 8. Trackpad Testing

| Test area | Required tests |
|---|---|
| Cursor movement | One-finger relative movement, sensitivity, acceleration, smoothing. |
| Clicks | Tap → left click; two-finger tap → right click; double tap → double click. |
| Scrolling | Two-finger vertical and horizontal scrolling. |
| Dragging | Tap/hold + movement → drag; release; cancellation. |
| Conflicts | Tap vs movement, click vs drag, scroll vs cursor, single vs double tap. |
| Accidental input | Ambiguous/accidental gestures should not trigger unintended action. |
| Connection loss | Disconnect during gesture/drag must not leave PC stuck dragging/clicking. |

## 9. Presentation Testing

Test semantic presentation/media controls with supported presentation/media applications where applicable.

| Control | Required tests |
|---|---|
| Next/Previous | Correct single-action behavior, rapid deliberate repeated taps, duplicate prevention. |
| Start/End | Supported start/end behavior, no active application, failed execution. |
| Blank screen | Activation and return/toggle where supported. |
| Audio | Volume Up, Volume Down, Mute. |
| Media | Play/Pause. |
| Limits | Unsupported action, unavailable target application, PC/OS limitation. |

Verify mobile does not claim confirmed PC state unless PC supplies it and stale commands do not execute after reconnection.

## 10. Laser-Pointer Testing

Test:

- Start and stop.
- Pointer movement, visibility, and responsiveness.
- Portrait/landscape behavior where relevant.
- Connection loss during active pointer.
- Session termination.
- PC receiver restart.
- Renderer unavailable/failure.

Verify pointer safely disappears/stops when session ends unexpectedly.

## 11. File-Sharing Testing

Test both Phone → PC and PC → Phone transfers.

| Area | Required tests |
|---|---|
| Normal | Small file and large file. |
| Multi-file | Multiple files where feature is implemented. |
| Naming | Duplicate/existing filename, invalid/unsupported filename. |
| Destination | Configured destination, unavailable destination, permission failure, insufficient storage. |
| Lifecycle | Start, progress, completion, cancel, retry/resume where supported. |
| Network | Weak Wi-Fi, interruption, disconnect, timeout, PC/mobile restart. |
| Integrity/state | Partial transfer cleanup and no incomplete file treated as complete. |
| Security | Unauthorized transfer and path traversal attempt. |
| Privacy | No persistent file-transfer history is created. |

## 12. Screen-Hosting Testing

| Area | Required tests |
|---|---|
| Lifecycle | Start, active state, stop from mobile, stop from PC. |
| Mirroring | Full mobile screen display; teacher continues using phone. |
| Orientation | Portrait, landscape, portrait→landscape, landscape→portrait. |
| Configuration | Quality, FPS, resolution settings where implemented. |
| Display | PC fullscreen; multiple PC displays where supported. |
| Network | Degradation, interruption, reconnect, changed network. |
| Failure | Capture permission, capture unavailable, encoder, decoder, PC display, timeout. |
| Restart/endurance | PC restart, mobile restart, long-duration stream. |
| Security | Unauthorized device/session cannot start or receive stream. |

Verify the PC never remains falsely shown as actively mirroring after stop, failure, timeout, restart, or session termination.

## 13. Custom Command Testing

| Area | Required tests |
|---|---|
| Management | Create, edit, delete, enable/disable, reorder where implemented. |
| Validity | Execute valid approved command; result feedback. |
| Rejection | Unknown command, disabled command, invalid parameters, unauthorized command. |
| Execution | Application unavailable, target unavailable, permission denied, timeout. |
| Reliability | Duplicate request, stale request, network interruption, reconnect. |
| Security | Attempt arbitrary shell/terminal execution, arbitrary path, script-like input, and command injection through normal UI/protocol. |

Verify arbitrary shell/terminal execution cannot be triggered through normal Custom Commands interface.

## 14. Security Testing

Test realistic local-network threats without prescribing a penetration-testing tool.

- Unknown device attempting connection/control.
- Invalid and expired OTP.
- Repeated OTP attempts and final selected rate-limit behavior.
- QR-only access attempt.
- Replayed authentication request.
- Replayed/duplicate presentation or Custom Command request.
- Unknown command ID and command-injection attempt.
- Path traversal/file-system abuse attempt.
- Unauthorized file transfer.
- Unauthorized Screen Hosting request/stream access attempt where testable.
- Stale session and session reuse after disconnect/logout.
- Session behavior after PC/mobile restart and network reconnect.

Verify rejected requests do not perform PC actions, create files, start streams, or expose sensitive error details.

## 15. Network and Reliability Testing

| Condition | Required verification |
|---|---|
| PC unavailable | Room unavailable status and retry behavior. |
| Mobile unavailable | PC safely ends/marks feature/session state. |
| Wi-Fi disconnect/reconnect | Safe stop, accurate reconnecting status, no stale input replay. |
| Network address change | Re-discovery works; teacher does not manage addresses. |
| Receiver restart | Prior session invalidated; recovery requires valid policy path. |
| Mobile restart | Persistent settings/rooms restore; transient operations do not replay. |
| Simultaneous features | Mouse + file transfer; Screen Hosting + controls; presentation + laser. |
| Long classroom session | Stable session/status/resource behavior. |

Confirm mouse stays responsive, large transfers do not block control traffic, Screen Hosting does not make presentation commands unusable, and stale commands never execute unexpectedly.

## 16. Performance Testing

Measure where practical and record observed values. Initial targets are **TBD**.

| Measure | Purpose |
|---|---|
| Control latency | Gyro/Trackpad input to observable PC response. |
| Cursor/pointer responsiveness | Subjective and observable smoothness/jitter/lag. |
| Presentation response time | Tap to PC command effect where measurable. |
| File-transfer throughput | Practical transfer capability under network conditions. |
| Screen-mirroring latency | Phone action to PC display delay. |
| Screen-mirroring FPS stability | Smoothness and degradation behavior. |
| Mobile CPU/battery | Prolonged teaching feasibility. |
| PC CPU/memory | Receiver/display/stream impact. |
| Network bandwidth | Feature load and coexistence behavior. |

Use measurements to establish later project targets; do not claim unsupported thresholds.

## 17. UI/UX Testing

Observe whether a teacher can quickly:

1. Connect to a room.
2. Start Gyro Mouse.
3. Switch to Trackpad.
4. Calibrate.
5. Use Presentation Controls.
6. Start Laser Pointer.
7. Share a file.
8. Start/stop Screen Hosting.
9. Use Custom Commands.
10. Disconnect.

Verify:

- Main screen remains simple and teaching-focused.
- Frequently used controls are visible/direct.
- Presentation Controls remain outside advanced settings.
- Advanced settings remain under three-dot menu.
- Glassmorphism remains readable; no visual effect reduces contrast.
- Light and dark themes are usable.
- Buttons are sufficiently easy to operate while teaching.
- Error/reconnecting states clearly communicate next action.

## 18. Regression Testing

Run this critical regression suite after major changes:

- PC setup and room-number QR registration.
- Discovery and saved-room selection.
- OTP authentication/session creation.
- Gyro Mouse and calibration.
- Trackpad gesture set.
- Presentation/media controls.
- Laser Pointer.
- File sharing in both directions.
- Screen Hosting start/stop/rotation.
- Custom Command allowlist execution/rejection.
- Disconnect/reconnect/session termination.

Automate only where practical; manual regression remains acceptable for hardware, classroom, gesture, streaming, and usability behavior in a college prototype.

## 19. Classroom Acceptance Testing

### Scenario: Start a lecture

1. PC receiver is configured with room number and running in system tray.
2. Teacher opens mobile app on classroom Wi-Fi.
3. Teacher selects reachable saved room.
4. Teacher enters PC-displayed OTP.
5. Authorized session is established.
6. Teacher controls cursor using Gyro Mouse.
7. Teacher switches to Trackpad for precise interaction.
8. Teacher advances and returns slides using presentation controls.
9. Teacher starts/uses/stops Laser Pointer.
10. Teacher shares a teaching file as needed.
11. Teacher starts Screen Hosting and continues using the phone.
12. Teacher rotates phone and PC display updates correctly.
13. Teacher stops Screen Hosting.
14. Teacher runs an approved Custom Command.
15. Teacher disconnects.

Successful completion means all intended actions work through authorized session, statuses are understandable, PC remains unobtrusive, no stale control persists after disconnect, and no critical feature requires technical network knowledge.

## 20. Test-Case Format

Use this format for executed test cases:

| Test ID | Feature | Preconditions | Steps | Expected Result | Status |
|---|---|---|---|---|---|
| PAIR-01 | QR registration | PC first setup; mobile app available | Configure room; generate QR; scan QR | Mobile saves room number only; no session/control granted | TBD |
| AUTH-01 | OTP authentication | Reachable saved room; PC receiver running | Select room; enter valid OTP | Authorized session created; controls enabled | TBD |
| AUTH-02 | OTP rejection | OTP entry state | Enter expired/invalid OTP | Session not created; actionable error shown | TBD |
| IMU-01 | Calibration | Authorized session; IMU available | Start calibration; keep phone still | Valid calibration stored; success feedback | TBD |
| IMU-02 | Stationary stability | Gyro active/calibrated | Hold phone still | No noticeable cursor drift; small movement remains possible | TBD |
| TRACK-01 | Drag safety | Authorized Trackpad active | Start drag; interrupt connection | PC does not remain stuck in drag state | TBD |
| PRES-01 | Next slide | Authorized session; supported presentation active | Tap Next once | Exactly one semantic next action occurs | TBD |
| LASER-01 | Session-end safety | Laser active | Disconnect session | Pointer stops/hides on PC | TBD |
| FILE-01 | Phone → PC | Authorized session; valid PC destination | Select file; transfer | Progress; completed file in configured destination; no history entry | TBD |
| FILE-02 | Path safety | Authorized session | Attempt unsafe filename/path metadata | Transfer rejected; no path escape/write | TBD |
| SCREEN-01 | Start/stop | Authorized session; capture/display supported | Start then stop hosting | Full screen mirrors; clear active/stopped state; phone remains usable | TBD |
| CMD-01 | Allowed command | Authorized session; command enabled | Run approved command | PC validates allowlist and executes once; result shown | TBD |
| CMD-02 | Shell protection | Authorized session | Attempt arbitrary shell-like command input | Rejected; no arbitrary local execution | TBD |
| NET-01 | Reconnect | Active session/control | Disconnect then restore Wi-Fi | Safe reconnect/re-auth path; no stale action replay | TBD |

## 21. Bug Reporting

Record defects with non-sensitive, reproducible information:

- Test ID and feature.
- Environment: mobile/PC class, relevant software build/version, display/network condition without credentials/addresses.
- Preconditions and exact reproduction steps.
- Expected behavior.
- Actual behavior.
- Severity.
- Reproducibility/frequency.
- Sanitized logs/diagnostics where safe.
- Screenshots or recordings where useful and permitted; do not include sensitive screen/file/credential content.

Do not include OTPs, session secrets, credentials, sensitive filenames/content, raw network addresses, or internal stack traces in ordinary bug reports.

## 22. Acceptance Criteria

- [ ] Practical unit testing covers core validation, state, sensor, gesture, command, transfer, and error logic.
- [ ] Integration tests cover discovery, authentication, session, mouse, presentation, laser, files, Screen Hosting, and Custom Commands.
- [ ] Pairing tests verify QR alone never authorizes control.
- [ ] Gyro/IMU tests cover calibration, axes, sensitivity, filtering, drift, stationary operation, and sensor failure.
- [ ] Trackpad tests cover required gestures and safe drag behavior after disconnect.
- [ ] Presentation and laser tests cover normal, unsupported, and loss-of-session behavior.
- [ ] File-sharing tests cover both directions, cancellation, storage/path safety, interruption, authorization, and no history.
- [ ] Screen-Hosting tests cover live mirroring, rotation, quality, recovery, restarts, display modes, and continued phone use.
- [ ] Custom Command tests verify management, allowlist execution, failure handling, duplicate/stale handling, and no arbitrary shell execution.
- [ ] Security tests cover OTP/session/replay/unauthorized/file/path/stream threats.
- [ ] Network/reliability tests cover disconnects, restarts, address changes, concurrent feature use, and long sessions.
- [ ] Performance data is measured practically with numerical targets initially marked **TBD**.
- [ ] UI/UX, regression, and full classroom lecture acceptance scenarios are defined.

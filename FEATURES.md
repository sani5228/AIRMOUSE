# Air Mouse for College Teaching — Feature Specification

## 1. Overview

Air Mouse is a teacher-focused wireless presentation and PC-control system. A mobile phone is the teacher's controller and a PC runs the Air Mouse receiver primarily in the background/system tray. The architecture is **mobile phone → Wi-Fi → PC receiver**; there is no ESP32, Arduino, Bluetooth controller, intermediary hardware, or required physical remote.

The product is for classroom teaching and prioritizes simplicity, low latency, reliability, and ease of use. The phone provides mouse control, presentation controls, laser pointing, file sharing, mobile screen hosting, and approved custom commands. The PC receives and carries out authorized requests.

## 2. Feature Categories

- System Pairing & Connection
- Gyro/IMU Mouse
- Trackpad Mouse
- Laser Pointer
- Presentation Controls
- File Sharing
- Screen Hosting
- Custom Commands
- Calibration & IMU Management
- PC/System Tray
- Notifications & Status
- Settings
- Security & Session Management

## 3. System Pairing & Connection

### First-time PC setup

1. The PC receiver shows a welcome screen with Air Mouse branding.
2. The PC asks for a room number.
3. The PC generates and displays a QR code containing **only the room number**.
4. The teacher scans the QR code in the mobile app.
5. The mobile app stores the room number for future use.

The QR code is for room identification and registration only. It is **not** an authentication mechanism and must not contain network credentials, authentication data, or unnecessary network metadata.

### Subsequent connection

1. The teacher opens the mobile app.
2. The app discovers reachable Air Mouse PCs on the current Wi-Fi network.
3. Saved and reachable systems are presented by room number.
4. The teacher selects a room.
5. The selected PC displays a temporary OTP.
6. The teacher enters the OTP in the mobile app.
7. The PC verifies the OTP and starts an authorized session on success.

Do not expose IP addresses, ports, MAC addresses, or similar network details to the teacher.

| Situation | Expected behavior |
|---|---|
| Room unavailable or PC not discovered | Show that the PC is not found, retain the saved room, offer retry, and direct the teacher to check that both devices are on the current Wi-Fi network. |
| Wrong OTP | Reject the attempt and show a concise invalid-code message. |
| Expired OTP | Reject the code, show that it expired, and require the currently displayed PC OTP. |
| Network change | End the active connection when reachability is lost; show reconnecting/unavailable status. |
| Connection loss | Suspend control safely, notify the teacher, and attempt reconnection where configured. |
| Reconnection | Resume only when the PC is reachable and session authorization remains valid; otherwise require room selection and OTP authentication. |
| Manual disconnect | Immediately terminate the session and return the PC to a waiting state. |

## 4. Gyro/IMU Mouse

Gyro Mouse uses phone orientation and movement to control the PC cursor in real time with low perceived latency. Calibration must be directly accessible from the mobile main screen.

### User interaction and configuration

- Select Gyro Mouse from the main screen; move or orient the phone to move the cursor.
- Pause or lock mouse movement without leaving the feature.
- Start calibration, recalibration, or reset directly from the main screen.
- Select controlling axes: XY, XZ, or YZ.
- Independently invert X, Y, and Z axes.
- Configure individual X/Y/Z sensitivity and overall sensitivity.
- Configure cursor acceleration, dead zone, noise filtering, drift compensation, stationary detection, and optional haptic feedback.

### Expected behavior

Sensor input is filtered and interpreted relative to calibration, then scaled by axis and overall sensitivity. Acceleration may increase displacement for faster movements. The resulting movement is sent as cursor movement to the PC. Filtering, stationary detection, and drift compensation must reduce unintended movement while retaining deliberate fine movement. Noise handling must **not** simply discard every small movement, because small movements can be intentional for precise cursor placement.

If the sensor is unavailable, disable Gyro Mouse with a clear message while leaving Trackpad available. If drift occurs, compensate where possible and allow immediate recalibration. If the phone is still, stationary detection should prevent cursor creep without making deliberate small adjustments impossible.

## 5. Trackpad Mouse

Trackpad Mouse turns the phone display into a familiar laptop-style touchpad where practical.

| Gesture | Required result |
|---|---|
| Touch and move | Cursor movement |
| Tap | Left click |
| Two-finger tap | Right click |
| Double tap | Double click |
| Two-finger vertical swipe | Vertical scrolling |
| Two-finger horizontal swipe | Horizontal scrolling |
| Tap/hold and move | Drag; release drops |

Support configurable cursor sensitivity and acceleration. Recognize gestures consistently, provide a clear active-drag state, and avoid unintended actions when input is ambiguous. When a touch sequence cannot be classified reliably, prefer no action over an unintended click, drag, or scroll.

## 6. Laser Pointer

The teacher can start and stop a laser pointer from the main screen and move its visible pointer in real time. The PC must render a classroom-visible pointer with low-latency position updates. Laser mode should fit alongside slide navigation and teaching, so it can be toggled without interrupting the presentation workflow.

The rendering mechanism, pointer style, and exact control mapping are **TBD**. Laser Pointer requires an authenticated PC session.

## 7. Presentation Controls

Presentation Controls must be directly available on the main mobile screen, using large touch-friendly controls suitable for use while teaching.

| Control | Semantic action |
|---|---|
| Next slide | Advance slide or presentation step |
| Previous slide | Return to previous slide or step |
| Start presentation | Start presentation mode |
| End presentation | End presentation mode |
| Black/blank screen | Toggle a blank presentation screen |
| Volume up | Raise volume |
| Volume down | Lower volume |
| Mute | Toggle mute |
| Play/Pause | Toggle media playback |

The phone sends semantic commands; the PC handles any OS- or application-specific execution. Each tap receives immediate mobile feedback. Commands require an authenticated connection. If the PC cannot execute one, show a concise failure notice and keep the app usable.

## 8. File Sharing

File sharing supports both directions and requires an authenticated session by default. It provides active-transfer status, progress, cancellation, completion feedback, and clear errors. It intentionally provides **no file-sharing history**.

### Phone → PC

1. Teacher selects a file on the phone.
2. Teacher starts transfer.
3. Phone and PC show progress and transfer state.
4. Either applicable side can cancel the transfer.
5. On success, show completion notification; on failure, show the reason where available.

### PC → Phone

1. Teacher selects a PC file from the PC receiver interface.
2. Teacher starts transfer; the phone is prompted as required.
3. Both devices show progress and transfer state.
4. Transfer can be cancelled.
5. Both devices show completion or failure notification.

The PC download location is configurable. File handling must use safe destination paths and safe filename handling. If a transfer is interrupted, report it clearly and do not present an incomplete file as successfully transferred. If the destination is unavailable or storage is insufficient, fail safely with a specific user-facing message.

## 9. Screen Hosting

Screen Hosting is full real-time mobile screen mirroring, not merely content or file sharing:

**mobile screen capture → encode → Wi-Fi transmission → PC receiver → decode → display**

Required behavior:

- Start and stop hosting from the main screen.
- Mirror the full mobile screen in real time with low latency.
- Support portrait and landscape orientation.
- Allow fullscreen display on the PC.
- Provide quality and FPS configuration.
- Use hardware-accelerated encoding and decoding where available.
- Allow the teacher to continue using the phone while mirroring.
- Use a streaming-oriented communication path rather than ordinary control messages.

Codec, streaming protocol, bitrate strategy, and exact quality-adaptation mechanism are **TBD**. On rotation, update the displayed stream to match the phone orientation. On network interruption, pause or end the stream safely, notify the teacher, and attempt recovery where configured. If recovery is not possible, require hosting to be restarted. If PC display fails, notify the teacher clearly. Quality adaptation should reduce the stream requirement when network conditions degrade; exact policy is **TBD**.

## 10. Custom Commands

Custom Commands are accessible from the main screen. The mobile app sends a semantic command ID/request; the PC validates it against an allowlist and performs only the approved local action. Examples include Open Calculator, Open Browser, and Open Teaching Folder.

Each command may contain a unique command ID, display name, enabled/disabled state, approved local action, and optional confirmation requirement. Users can create, edit, enable, disable, and execute commands through command management settings.

There is no unrestricted arbitrary shell or terminal execution by default. A disabled command is unavailable. A non-allowlisted or unauthorized request is rejected, with clear failure feedback on the mobile app. Successful execution receives success feedback; execution failure receives a concise failure notification.

## 11. Mobile Main Screen

The main screen is intentionally minimal and contains only frequent teaching controls:

- Connected system and room number
- Gyro Mouse
- Trackpad
- Laser Pointer
- Calibrate
- Presentation Controls
- Share File
- Screen Hosting
- Custom Commands

Advanced configuration belongs in a three-dot (⋮) menu. Presentation Controls must not move into that menu. This structure reduces cognitive load and keeps common classroom actions one tap away.

## 12. Advanced Settings / Three-Dot Menu

These areas are for configuration rather than frequent classroom actions:

| Group | Scope |
|---|---|
| Mouse Settings | Mouse mode, sensitivity, acceleration, dead zone, filtering, haptics |
| IMU | Axes, inversion, calibration, reset, drift-related settings |
| Presentation | Presentation behavior/settings where applicable |
| Screen Hosting | Quality, FPS, orientation, display behavior |
| Systems | Saved rooms, current system, reconnect and disconnect behavior |
| File Sharing | PC download location and transfer behavior |
| Custom Commands | Create, edit, enable, disable, and confirmation settings |
| Security | Session and authentication preferences where applicable |
| General/App Settings | Light/dark theme and other general options, TBD |

## 13. PC/System-Tray Features

The PC receiver primarily runs as a background/system-tray application and stays out of the teacher's way during teaching.

### Setup

The first-run experience includes welcome text, Air Mouse branding, room-number input, QR generation, and QR display.

### Normal tray panel

The tray panel shows Air Mouse, room number, system status, connection status, connected device where available, mouse status, presentation status, Screen Hosting status, File Transfer status, Disconnect, and Settings/three-dot menu.

### OTP screen

When a phone selects the PC, display the room number, temporary OTP, OTP expiry, and waiting-for-mobile state.

Use small non-intrusive notifications for Air Mouse connected, file received, Screen Hosting started, and disconnected events.

## 14. Notifications & Status

Notifications are concise and non-intrusive.

| Event | Expected feedback |
|---|---|
| Connection established | Connected to the selected room |
| Connection lost | Connection lost; reconnecting or unavailable state |
| Reconnecting | Reconnecting to selected room |
| OTP generated | PC shows new code and expiry |
| OTP rejected | Invalid code |
| OTP expired | Code expired; enter current code |
| Calibration started/completed | Calibration status |
| File transfer started/completed/failed | Transfer state, completion, or concise error |
| Screen hosting started/stopped | Hosting state |
| Custom command executed/rejected | Command result |
| PC unavailable | PC not found or unavailable |

## 15. Security & Session Management

- A room number identifies a classroom PC; it does not authenticate a user.
- QR registration is not authentication.
- A temporary OTP authenticates a requested connection.
- Control features require session authorization.
- File sharing requires an authenticated session by default.
- Custom Commands require PC-side allowlist validation.
- Unrestricted remote shell/terminal execution is not enabled by default.
- The PC and teacher can terminate a session through disconnect.
- OTPs expire after a temporary period; exact duration is **TBD**.
- Unauthorized control requests must be rejected.
- Cryptographic, transport-protection, session-token, and related technical mechanisms are **TBD**.

## 16. Settings

| Area | User-configurable settings |
|---|---|
| Mouse | Mode, axis, X/Y/Z inversion, individual/overall sensitivity, acceleration, dead zone, filtering, haptics |
| Calibration | Start calibration, recalibrate, reset |
| Connection | Saved rooms, current system, reconnect behavior, disconnect |
| Presentation | Presentation behavior/settings where applicable, TBD |
| Screen Hosting | Quality, FPS, orientation/display behavior |
| File Sharing | PC download location and transfer behavior |
| Custom Commands | Command management |
| Appearance | Light theme and dark theme |

Unspecified persistence, defaults, and detailed settings behavior are **TBD**.

## 17. Feature Dependencies

| Feature | Depends on |
|---|---|
| PC discovery | PC receiver running + current Wi-Fi reachability |
| OTP connection | Room selection + PC receiver + temporary OTP |
| Authorized session | Successful OTP verification |
| Gyro Mouse | Mobile IMU + calibration + authenticated connection |
| Trackpad | Mobile touch input + authenticated connection |
| Presentation Controls | Authenticated connection + PC control capability |
| Laser Pointer | Authenticated connection + PC pointer rendering |
| File Sharing | Authenticated connection + transfer capability + available storage/path |
| Screen Hosting | Mobile screen-capture capability + streaming connection + PC display/decoding capability |
| Custom Commands | Authenticated connection + PC allowlist |
| Calibration | Mobile IMU |
| Notifications | Session state and feature event state |
| System Tray | Running PC receiver |

## 18. Main User Flows

### First-time room registration

1. Open the PC receiver setup.
2. Enter the room number.
3. Generate and display the room QR.
4. Scan the QR in the mobile app.
5. Confirm the room is saved on the phone.

### Connecting to a saved room

1. Open the mobile app.
2. Select a discovered saved room.
3. Read the temporary OTP on the PC.
4. Enter the OTP on the phone.
5. Use the authorized session after confirmation.

### Using Gyro Mouse

1. Tap Gyro Mouse.
2. Calibrate if needed.
3. Move the phone to control the cursor.
4. Lock/pause or exit when finished.

### Using Trackpad

1. Tap Trackpad.
2. Move a finger for cursor movement.
3. Use supported tap, two-finger, scroll, and drag gestures.

### Calibrating IMU

1. Tap Calibrate on the main screen.
2. Hold the phone in the intended reference orientation.
3. Wait for completion feedback.

### Controlling a presentation

1. Use the main-screen presentation controls.
2. Confirm command feedback.
3. If a command cannot execute, read the concise failure state and continue using other controls.

### Using Laser Pointer

1. Start Laser Pointer.
2. Move the phone to position the pointer.
3. Stop Laser Pointer when finished.

### Sharing phone → PC

1. Tap Share File.
2. Select a phone file.
3. Start the transfer.
4. Watch progress, cancel if necessary, and receive completion/failure notification.

### Sharing PC → phone

1. Select a file in the PC receiver.
2. Start transfer and respond to the phone prompt if shown.
3. Watch progress and completion/failure status.

### Starting and stopping Screen Hosting

1. Tap Screen Hosting, then Start.
2. Use the phone normally while its screen appears on the PC.
3. Tap Stop Hosting when finished.

### Executing a Custom Command

1. Open Custom Commands from the main screen.
2. Select an enabled command.
3. Receive execution or rejection feedback.

### Disconnecting

1. Tap Disconnect on the phone or PC.
2. Confirm that the session ended and the PC returned to waiting state.

## 19. Edge Cases

| Edge case | Expected user-facing behavior |
|---|---|
| Multiple PCs on one network | List reachable systems by room number; teacher selects the intended room. |
| Same room-number conflict | Indicate that multiple systems use the same room number and require a disambiguation approach; exact approach is TBD. |
| PC unavailable | Show PC unavailable/not found; preserve saved room and permit retry. |
| Mobile changes Wi-Fi | End active control safely and show reconnecting or unavailable status. |
| PC changes Wi-Fi | End session and require discovery/reconnection when reachable. |
| OTP expires or is wrong | Reject authentication with the appropriate message; accept only current valid OTP. |
| Connection drops during mouse control | Stop outgoing control safely; cursor does not continue moving from stale input; notify and reconnect where possible. |
| Connection drops during file transfer | Mark transfer interrupted/failed; do not claim success for incomplete data. |
| Connection drops during Screen Hosting | Pause/end stream safely; show recovery status and require restart if recovery fails. |
| Phone rotation during hosting | Update stream orientation. |
| IMU drift/noise | Apply configured compensation/filtering; permit recalibration; retain intentional fine movement. |
| Sensor unavailable | Explain Gyro Mouse is unavailable; leave Trackpad usable. |
| PC app closed | End session and show PC unavailable on phone. |
| Custom command disabled/rejected | Do not execute; show disabled or not-allowed feedback. |
| File destination unavailable | Fail safely with destination-unavailable message. |
| Insufficient storage | Fail safely with insufficient-storage message. |
| Unsupported screen capture | Explain Screen Hosting is unavailable on that phone/device. |

## 20. Feature Priority

This classification organizes development scope only; it is not a quality ranking.

| Scope | Features |
|---|---|
| Core | System Pairing & Connection, authorized sessions, Gyro/IMU Mouse, Trackpad Mouse, Presentation Controls, PC/System Tray, Notifications & Status, essential settings/security |
| Supporting | Calibration & IMU Management, Laser Pointer, File Sharing, main-screen design and configuration organization |
| Advanced | Screen Hosting, Custom Commands, advanced hosting and command configuration |

## 21. Non-Goals

- No ESP32 or Arduino hardware.
- No Bluetooth controller.
- No unrestricted remote shell execution.
- No file-sharing history.
- No unnecessary cloud dependency.
- No requirement for a separate physical remote.
- No replacement of the teacher's phone as the primary controller.

## 22. Acceptance Criteria

The system is functionally complete when all of the following are true:

- [ ] A PC can be registered using a room number.
- [ ] A teacher can discover the PC on Wi-Fi.
- [ ] OTP authentication establishes an authorized session.
- [ ] Gyro Mouse works with calibration and configurable axes.
- [ ] Trackpad gestures work.
- [ ] Laser Pointer works.
- [ ] Presentation controls work.
- [ ] Files can be transferred in both directions.
- [ ] Full mobile screen mirroring works in real time.
- [ ] Custom commands execute only approved actions.
- [ ] PC receiver can operate from the system tray.
- [ ] Connection failures are handled clearly.
- [ ] Main-screen controls remain simple and teaching-focused.
- [ ] No file-sharing history is included.

# Air Mouse for College Teaching — Mouse Control Requirements

## 1. Overview

Air Mouse lets a teacher control the cursor of a classroom PC with a mobile phone over Wi-Fi:

**Mobile Phone → Wi-Fi → PC Receiver → OS Cursor**

The mobile phone provides input; the PC receiver converts authorized input into normal OS mouse behavior. There is no ESP32, Arduino, Bluetooth controller, or intermediary hardware.

Mouse control must prioritize low latency, smooth cursor movement, accurate small movements, minimal jitter, reliable clicks, and easy switching between modes. Mouse input is available only through an authenticated Air Mouse session.

## 2. Mouse Modes

| Mode | Purpose | Teacher interaction |
|---|---|---|
| Gyro Mouse | Controls the PC cursor from phone IMU/gyroscope movement and orientation. Best for standing, pointing, and air-mouse use during teaching. | Select Gyro Mouse; move/orient phone; calibrate or pause as needed. |
| Trackpad | Uses the mobile touchscreen like a familiar laptop touchpad. Best for precise pointing, clicking, dragging, and scrolling. | Select Trackpad; use the touch surface and supported gestures. |

The teacher switches modes directly from the mobile main screen. Switching is quick, visually obvious, and does not require re-authentication. The inactive mode immediately stops generating mouse movement. Avoid unnecessary extra modes or configuration steps.

## 3. Gyro Mouse

When Gyro Mouse is active, phone movement/orientation affects PC cursor movement. The cursor should feel responsive and natural, permit deliberate small movements, and remain stable when the phone is still.

Noise and jitter must be reduced without suppressing useful movement. A stationary phone should not cause noticeable cursor drift. The exact sensor-processing algorithm is **TBD** and belongs in `IMU.md`; this document defines observable behavior and configuration requirements.

## 4. Axis Configuration

Gyro Mouse supports axis pairs **XY**, **XZ**, and **YZ**. The selected pair determines which phone-motion axes influence cursor movement. Axis mapping is configurable independently of other mouse settings.

Support independent inversion for X, Y, and Z. For example, X can be normal, Y inverted, and Z normal. Inversion changes only the applicable axis direction; it must not reset sensitivity, acceleration, calibration, or unrelated configuration.

The active axis pair and inversion state must be visible in advanced mouse/IMU settings. Exact orientation conventions and mapping semantics are **TBD** and must be documented in `IMU.md` once chosen.

## 5. Sensitivity

### Overall sensitivity

Overall Sensitivity controls general cursor response. Increasing it makes a given processed motion create more cursor movement; decreasing it favors precision.

### Individual axis sensitivity

Separate X, Y, and Z sensitivity controls allow tuning each sensor axis. Only axes selected by the current XY/XZ/YZ pair affect cursor movement. For example, with XY active, X and Y sensitivity affect cursor response while Z sensitivity is retained for later use but does not affect current cursor movement.

Sensitivity changes should take effect without a full application restart unless technically necessary. Exact ranges, defaults, and persistence timing are **TBD**.

## 6. Cursor Acceleration

Gyro Mouse provides configurable cursor acceleration. Slow physical motion should support precise cursor placement; faster physical motion should move the cursor faster so the teacher can traverse the display efficiently.

Acceleration must not make the cursor uncontrollably fast or unpredictable. It acts after basic movement interpretation and sensitivity scaling as appropriate to the final pipeline. The exact acceleration curve and parameter range are **TBD**.

## 7. Dead Zone

A configurable dead zone reduces unwanted tiny movement caused by sensor noise.

| Input condition | Required behavior |
|---|---|
| Movement below configured threshold | Reduce or suppress unintended movement according to dead-zone behavior. |
| Movement above configured threshold | Allow intentional movement to pass into normal cursor processing. |

The dead zone must not be so aggressive that small intentional movements become impossible. The exact threshold model and mathematical implementation are **TBD**.

## 8. Noise Filtering

Gyro Mouse must reduce sensor noise and cursor jitter. The implementation may consider low-pass filtering, adaptive filtering, stationary detection, bias estimation, or other suitable techniques. No specific algorithm is mandated until testing determines one.

The outcome must provide stable cursor behavior while stationary, smooth movement during intentional motion, low latency, and preservation of small movements. Detailed filtering design belongs in `IMU.md`.

## 9. Drift Compensation

Gyroscope drift can cause cursor movement even when the phone is still. During stationary periods, the system should detect stillness, estimate bias/drift where appropriate, and minimize cursor movement caused by accumulated drift.

Drift compensation must not continuously force motion to zero in a way that suppresses legitimate movement. Detailed calibration and drift methods are defined separately in `IMU.md`.

## 10. Stationary Detection

Stationary detection distinguishes sufficiently still phone states from moving states.

| State | Functional behavior |
|---|---|
| Stationary | Reduce unwanted cursor movement; permit drift/bias handling where appropriate. |
| Moving | Process intentional motion for cursor control. |
| Transition | Change smoothly enough to avoid obvious cursor jumps, delay, or lost intended movement. |

Exact thresholds, sample durations, and transition rules are **TBD**.

## 11. Calibration

Calibration is directly accessible from the mobile main screen.

### Basic user flow

1. Teacher taps **Calibrate**.
2. The app instructs the teacher to place the phone on a stable surface and keep it still.
3. A countdown begins.
4. Stationary sensor data is collected.
5. Bias/offset is estimated as appropriate.
6. Calibration completes and data is stored.
7. The app reports success or failure.

Support recalibration and reset calibration. Recalibration replaces prior calibration only after successful completion. Reset returns calibration to the defined default/unconfigured state and may require confirmation. Detailed calibration methodology belongs in `IMU.md`.

## 12. Mouse Lock/Pause

Provide a Mouse Lock/Pause control for Gyro Mouse. When enabled, phone movement must not move the PC cursor, but the authenticated connection remains active. The mobile UI clearly indicates that movement is paused.

This feature lets a teacher hold, reposition, or set down the phone without disturbing the cursor. Unlocking resumes normal current input processing; it must not apply accumulated movement as a jump.

## 13. Haptic Feedback

Haptic feedback may be supported when available. Appropriate uses include mode change, calibration completion, click, mouse lock/pause, and other meaningful state changes.

Haptics are optional, configurable, and must not interfere with normal teaching. Their exact patterns are **TBD**.

## 14. Trackpad

Trackpad mode resembles a familiar laptop touchpad and uses relative movement rather than mapping the PC cursor directly to absolute positions on the phone screen.

| Gesture | Required action |
|---|---|
| One-finger movement | Cursor movement |
| Tap | Left click |
| Two-finger tap | Right click |
| Double tap | Double click |
| Two-finger vertical swipe | Vertical scroll |
| Two-finger horizontal swipe | Horizontal scroll |
| Tap/hold + move | Drag |

## 15. Trackpad Cursor Movement

Trackpad tracks touch movement and converts it to relative cursor movement. It supports configurable sensitivity, acceleration, and smoothing where appropriate. Movement should feel smooth and direct without forcing the PC cursor to correspond to the phone’s physical screen coordinates.

Smoothing must not add excessive delay or make small precise movement difficult. Exact processing details are **TBD**.

## 16. Clicking

- **Left Click:** single tap.
- **Right Click:** two-finger tap.
- **Double Click:** double tap.

Gesture recognition must avoid accidental clicks caused by ordinary finger movement. Tap timing, movement tolerance, double-tap window, and related thresholds are implementation details and remain **TBD**.

## 17. Dragging

Tap/hold + move starts a drag operation.

| Phase | Required behavior |
|---|---|
| Drag start | Recognize deliberate hold, enter drag-active state, and begin press/hold behavior. |
| Drag movement | Move the PC cursor while maintaining the drag. |
| Drag release | Release when touch ends and complete the drop. |
| Cancellation | If drag is cancelled or connection is lost, safely end drag state without continuing stale movement. |

Prevent accidental drag by distinguishing normal taps from hold-and-move input. Provide practical visual feedback when possible, such as a drag-active indicator.

## 18. Scrolling

Two-finger vertical swipe performs vertical scroll; two-finger horizontal swipe performs horizontal scroll. Direction must match the configured/expected touchpad direction, with any optional direction preference **TBD**.

Support configurable scroll sensitivity and smooth, proportional scrolling where appropriate. Two-finger scroll input must not be interpreted simultaneously as normal one-finger cursor movement.

## 19. Gesture Conflicts

Trackpad gesture recognition must distinguish tap versus movement, single tap versus double tap, right-click gesture versus normal movement, drag versus click, and scroll versus cursor movement.

Use practical thresholds and timing so the feature remains easy to operate. When input is ambiguous, avoid unintended actions in preference to aggressive interpretation. Exact gesture-recognition parameters are **TBD**.

## 20. Mouse Mode Switching

Switching between Gyro Mouse and Trackpad must be quick and available from the main screen. The current mode must be visually obvious. When a mode becomes inactive, it stops generating mouse movement immediately.

Changing modes does not end the authenticated session and does not require a new OTP. Any active Gyro Mouse pause/lock state remains a mode-local temporary state unless a later product decision specifies otherwise.

## 21. Main-Screen Controls

The mobile main screen provides direct access to:

- Gyro Mouse
- Trackpad
- Calibrate

Normal mouse operation must not require entering advanced settings. Advanced configuration is grouped under the three-dot menu.

## 22. Advanced Mouse Settings

| Area | Settings |
|---|---|
| Gyro | Axis selection; X/Y/Z inversion; X/Y/Z sensitivity; overall sensitivity; cursor acceleration; dead zone; noise filtering; drift compensation; stationary detection; haptic feedback; mouse lock. |
| Trackpad | Sensitivity; acceleration; scroll sensitivity; gesture behavior. |

Exact ranges, defaults, labels for complex controls, and reset behavior are **TBD** unless specified elsewhere.

## 23. Input Pipeline

The pipeline is conceptual and does not prescribe programming implementation.

### Gyro Mouse

```text
IMU Sensor
→ Calibration
→ Bias/Drift Compensation
→ Noise Filtering
→ Stationary Detection
→ Axis Mapping
→ Inversion
→ Sensitivity
→ Acceleration
→ Cursor Delta
→ PC Receiver
→ OS Cursor
```

Order may be adjusted if testing requires it, provided the observable requirements in this document remain true.

### Trackpad

```text
Touch Input
→ Gesture Recognition
→ Sensitivity/Acceleration
→ Cursor/Click/Scroll Event
→ PC Receiver
→ OS Mouse Action
```

## 24. Low-Latency Requirements

Mouse control should feel close to real time. Prioritize minimal transmission delay, efficient input processing, small control messages, smooth cursor updates, and avoidance of unnecessary buffering. Do not send large payloads through the mouse-control path.

The exact latency target and measurement method are **TBD**.

## 25. Connection Loss

When the connection is lost:

- Stop sending mouse-control data.
- PC must not continue applying stale movement indefinitely.
- Mobile shows disconnected or reconnecting state.
- Cursor stops safely.
- On reconnection, do not apply accumulated/stale sensor movement as a large sudden cursor jump.

Mouse input resumes only once the authorized connection is valid again. Reconnection and re-authentication policy is defined in `PAIRING.md`.

## 26. Sensor Failure

Handle gyroscope unavailable, IMU unavailable, sensor permission denied, sensor initialization failure, and sensor data stopping unexpectedly.

The app must clearly report the issue, prevent misleading cursor behavior, disable or stop Gyro Mouse safely, and offer Trackpad when available. It must not imply that gyro control is active when sensor input is unavailable.

## 27. Edge Cases

| Case | Expected behavior |
|---|---|
| Phone held at unusual orientation | Apply selected axis mapping/inversion; teacher can adjust settings or recalibrate if movement is not intuitive. |
| Phone rotated during operation | Continue according to configured mapping without sudden uncontrolled jump; exact orientation policy is TBD. |
| Very slow movement | Preserve deliberate fine movement where above configured processing thresholds. |
| Very fast movement | Apply acceleration and safety limits; avoid unbounded cursor velocity or extreme jump. |
| Small intentional movement | Preserve it despite filtering/dead zone configuration as far as practical. |
| Sensor jitter | Reduce through filtering/stationary handling without adding excessive latency. |
| Gyro drift | Minimize with stationary detection, drift compensation, and recalibration. |
| Phone placed on a table | Stationary behavior prevents noticeable cursor creep; teacher can pause/lock if desired. |
| Incorrect calibration | Report failure where detectable; otherwise allow simple recalibration/reset. |
| Accidental touches during Gyro mode | Do not treat them as Trackpad input unless user switches mode or a separate intentional control is defined. |
| Accidental gestures during Trackpad mode | Gesture rules avoid unintended clicks/drags/scrolls where input is ambiguous. |
| Connection interruption | Stop safely and do not replay stale movement after recovery. |
| PC receiver closes | End usable mouse control and show PC unavailable/disconnected. |
| Mobile app enters background | Suspend/control behavior safely according to platform capabilities, TBD; do not permit stale or unexpected input. |

## 28. Safety and Usability Limits

Protect against extremely large cursor jumps, unbounded cursor velocity, repeated accidental clicks, stale movement after reconnection, and unexpected input after session termination.

Specific numerical limits and algorithms are **TBD**. Safety behavior must favor predictable stop/limit behavior over uncontrolled input and must not prevent ordinary classroom use.

## 29. Configuration Persistence

Persistent configuration may include selected mouse mode, axis selection, inversion, sensitivity, acceleration, dead zone, filtering, haptics, Trackpad settings, and calibration data. Exact persistence policy is **TBD**.

Temporary session state includes active/inactive mode, current mouse lock/pause state, in-progress calibration, active touch/drag state, and connection status. Temporary state must not result in stale cursor actions after disconnection, restart, or session termination.

## 30. Acceptance Criteria

### Gyro Mouse

- [ ] Cursor responds to phone movement.
- [ ] XY, XZ, and YZ axis modes work.
- [ ] X, Y, and Z inversion work independently.
- [ ] Individual and overall sensitivity work.
- [ ] Cursor acceleration works.
- [ ] Dead zone reduces unwanted jitter.
- [ ] Small intentional movements remain possible.
- [ ] Stationary phone does not cause noticeable cursor drift.
- [ ] Calibration works.
- [ ] Recalibration and reset work.
- [ ] Mouse lock pauses cursor movement.
- [ ] Connection loss does not produce stale cursor movement.

### Trackpad

- [ ] One-finger movement controls cursor.
- [ ] Tap performs left click.
- [ ] Two-finger tap performs right click.
- [ ] Double tap performs double click.
- [ ] Two-finger vertical swipe scrolls vertically.
- [ ] Two-finger horizontal swipe scrolls horizontally.
- [ ] Tap/hold + move performs dragging.
- [ ] Gesture conflicts are handled reliably.

### General

- [ ] User can quickly switch between Gyro Mouse and Trackpad.
- [ ] Both modes work through the authenticated Wi-Fi session.
- [ ] Mouse controls remain responsive.
- [ ] Main-screen controls remain simple.
- [ ] Advanced configuration is available through the three-dot menu.

Detailed sensor mathematics, filtering algorithms, and calibration methodology belong in `IMU.md`.

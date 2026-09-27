# Air Mouse for College Teaching — UI/UX Requirements

## 1. Design Goals

Air Mouse must feel like a polished teaching tool, not a complicated technical utility. The mobile app is the teacher’s primary controller; the PC receiver is primarily an unobtrusive companion in the system tray.

The UI/UX must be:

- **Simple:** common actions require little reading or setup.
- **Fast to understand:** names, icons, and states are self-explanatory.
- **Minimal during teaching:** frequent controls are immediately available; configuration stays out of the way.
- **Easy to hit:** controls used while teaching are large and well-spaced.
- **Low clutter:** show only information needed for the current task.
- **Status-clear:** connection, active feature, progress, and failure states are visible without exposing technical network details.
- **Consistent:** mobile and PC use the same names, icon concepts, state meanings, and theme philosophy.
- **Accessible:** light and dark themes remain readable and usable.
- **Smooth:** animations are subtle, short, and purposeful.
- **Suitable for prolonged use:** avoid visually tiring effects, small controls, dense panels, and unnecessary interruptions.

## 2. Visual Design Language

Both interfaces use a clean, modern **glassmorphism** design language.

| Element | Requirement |
|---|---|
| Background | Layered, calm background that supports readable foreground surfaces. Exact imagery/color treatment is TBD. |
| Glass panels | Translucent surfaces with background blur; opacity must preserve readable content. |
| Borders | Subtle borders to distinguish glass surfaces from the background and each other. |
| Corners | Consistently rounded corners across cards, dialogs, fields, and panels. Exact radii are TBD. |
| Shadows | Soft, restrained shadows to establish elevation without making the interface heavy. |
| Layering | Clear base, content, elevated dialog, and transient-notification layers. |
| Typography | Clear hierarchy for room/status, primary actions, labels, supporting text, and errors. |
| Iconography | Consistent icon family and meaning across mobile and PC. Icons must be recognizable and paired with labels where ambiguity is possible. |
| Motion | Subtle transitions for state changes, panel appearance, progress, and confirmation. |

Glass effects must not reduce readability. Text, controls, and status indicators must maintain sufficient contrast against their current background. Avoid stacking so many translucent layers that content becomes visually confusing.

## 3. Theme Support

Support light and dark themes using semantic design tokens rather than fixed color prescriptions: **Primary, Secondary, Success, Warning, Error, Background, Surface, Text, Muted text**.

| Element | Light theme | Dark theme |
|---|---|---|
| Background | Bright, calm background with restrained depth | Dark, calm background with restrained depth |
| Glass panels | Light translucent surface and readable border | Dark translucent surface and readable border |
| Text | High-contrast dark Text; softer Muted text | High-contrast light Text; softer Muted text |
| Icons | Text-aligned contrast, with semantic status variants | Text-aligned contrast, with semantic status variants |
| Buttons | Primary/Secondary hierarchy remains distinct | Same hierarchy; contrast remains sufficient |
| Input fields | Clearly bounded glass/surface field and readable entered value | Equivalent readable field against dark surfaces |
| Status indicators | Success, Warning, Error, and neutral distinctions with non-color cues | Same semantic distinctions and non-color cues |
| Dialogs | Elevated glass/surface layer separated from content | Equivalent elevated layer with readable contrast |
| Notifications | Compact elevated surface with semantic icon/text | Equivalent compact elevated surface |

Theme selection behavior and system-theme synchronization are **TBD**. Status must never rely on color alone; use text, icon, shape, or state label as well.

## 4. Mobile Navigation Philosophy

The mobile app is the teacher’s primary controller. Its main screen contains only frequent teaching functions, and the three-dot (⋮) menu contains advanced configuration. The main screen must not become a settings dashboard.

Navigation should favor few steps, predictable back/close behavior, and rapid return to the main screen. Opening a feature should not hide the connected-room context or make it difficult to stop an active feature. Presentation Controls remain directly accessible from the main screen at all times.

## 5. Mobile Main Screen

The main screen is the teaching dashboard. It must show:

- Connected system / room number
- Gyro Mouse
- Trackpad
- Laser Pointer
- Calibrate
- Presentation Controls
- Share File
- Screen Hosting
- Custom Commands
- Three-dot (⋮) advanced menu

### Layout requirements

Use a compact connection area at the top, followed by prominent teaching controls arranged in responsive cards or action groups. Presentation Controls must be directly reachable on this screen, not hidden in the advanced menu. A logical arrangement is:

1. Connection/system indicator at the top.
2. Primary control-mode actions: Gyro Mouse, Trackpad, Laser Pointer, Calibrate.
3. Prominent Presentation Controls section with direct action buttons.
4. Teaching utilities: Share File, Screen Hosting, Custom Commands.
5. Three-dot menu for advanced configuration.

The exact pixel layout is not required. The screen must clearly show which room is connected, whether the system is ready, and whether a feature is active. Active features use a persistent but non-distracting state treatment, such as an active label, icon state, and/or highlighted card.

## 6. Connection/System Indicator

Use a compact, teacher-facing system area that shows:

- Room number as the primary system identifier.
- Connection status: disconnected, discovering, awaiting authentication, authenticating, connected, reconnecting, or unavailable.
- Connected/disconnected state at a glance.
- Optional connection-quality indication, if available.

Do not make internal addresses, ports, device IDs, or other technical network metadata primary user-facing information. A connected state should include the room name/number and a clear connected label. A disconnected state should provide the next action, such as Select Room or Retry.

## 7. Gyro Mouse UI

### Frequent controls

Gyro Mouse is launched from the main screen. Its entry and active view must provide:

- Start/stop control.
- Clear active/inactive state.
- Direct Calibrate action.
- Mouse lock/pause control.
- Simple sensitivity adjustment when practical.

When active, show a visible but restrained indicator that cursor control is live. Lock/pause must make it unambiguous that phone movement is not controlling the cursor.

### Advanced controls

The three-dot menu’s Mouse Settings and IMU areas contain:

- Sensitivity.
- Axis selection.
- Invert X, Y, and Z independently.
- Acceleration.
- Dead zone.
- Filtering.
- Drift compensation.
- Haptic feedback.

Advanced sensor controls must be clearly labeled, retain sensible current-value visibility, and not crowd the teaching screen. Any setting that changes how movement feels should provide immediate understandable feedback, but exact preview behavior is TBD.

## 8. Trackpad UI

Trackpad mode visually resembles a familiar laptop touchpad. It includes a large, clearly bounded touch area occupying the primary interaction region, with optional compact status/control strip for exit, sensitivity, or active mode indication.

| Interaction | UI behavior |
|---|---|
| Touch and move | The touch area tracks movement for cursor motion. |
| Tap | Brief visual pressed feedback; sends left click. |
| Two-finger tap | Brief visual feedback; sends right click. |
| Double tap | Feedback confirms the second tap without excessive animation; sends double click. |
| Two-finger scroll | Directional movement produces vertical or horizontal scroll behavior. |
| Tap/hold and move | Show a clear drag-active state until release. |

Visual feedback should be limited to useful cues: touch ripple/press state, drag state, and any error or disconnected overlay. It must not distract from teaching or cover the touch area unnecessarily.

## 9. Calibration UI

Calibration is a direct main-screen action.

### Standard flow

1. Teacher taps **Calibrate**.
2. The app explains that the phone must remain still and should be placed on a stable surface.
3. Teacher starts or confirms calibration.
4. A short countdown begins.
5. Calibration samples are collected.
6. Progress/status is visible throughout.
7. Completion shows a clear success state.
8. Calibration data is stored.

Use a focused overlay or dedicated view so the teacher knows calibration is in progress. The status should distinguish preparing, countdown, sampling, completed, cancelled, and failed states.

### Additional behavior

| Case | UX requirement |
|---|---|
| Cancel | Teacher can cancel before completion; no unclear partial-success state is shown. |
| Failed calibration | Explain that the phone moved, sensor data was unavailable, or calibration could not complete; provide Retry and Cancel. |
| Recalibration | Available from the main screen and IMU settings; overwrites the current calibration only after successful completion. |
| Reset calibration | Available in advanced IMU settings; require confirmation where appropriate and show successful reset state. |

The user must always understand whether calibration is running, completed, failed, cancelled, or needs attention.

## 10. Laser Pointer UI

Laser Pointer has a quick start/stop control on the main screen. When active, its control shows an unmistakable active state and provides an obvious stop/exit action. The pointer-movement interaction must be direct and low-friction; exact input mapping is **TBD**.

Use a persistent compact active indicator while the laser is enabled. Exiting the feature stops pointer display and restores the prior control context. The UI should support rapid use during a lecture without forcing navigation through a separate settings flow.

## 11. Presentation Controls UI

Presentation Controls are directly accessible from the main screen. Use large, clearly separated buttons with recognizable icons and short labels when icon meaning could be unclear.

| Control | UI requirement |
|---|---|
| Previous slide | Large previous control with clear directional icon/label. |
| Next slide | Large next control with clear directional icon/label. |
| Start presentation | Clearly named start control. |
| End presentation | Clearly named end control; confirmation may be used where appropriate. |
| Black/blank screen | Toggle with clear active state. |
| Volume up/down | Compact but easy-to-hit controls. |
| Mute | Toggle with distinct muted/unmuted states. |
| Play/Pause | Toggle with distinct playing/paused states where state is available. |

Use tooltips, short help, or onboarding hints where helpful, especially on PC or for less universal icons. Prevent accidental destructive actions where appropriate, particularly ending a presentation, without adding repeated friction to common navigation. Do not hide common presentation actions behind multiple navigation levels.

## 12. File Sharing UI

File sharing must support both directions with clear in-progress state. There must be **no file-sharing history screen**, historical transfer list, or transfer-history page.

### Phone → PC

- Entry from Share File on the main screen.
- File selection interface.
- Destination information identifying the selected room/PC and, when relevant, configured PC destination.
- Confirmation before starting where appropriate.
- Active transfer view with file name, progress percentage or equivalent, transfer state, cancel action, and concise error area.
- Completion state and failure state with clear next action.

### PC → Phone

- PC-side file selection entry.
- Clear destination information identifying the connected phone/session as appropriate.
- Active transfer view with file name, progress percentage or equivalent, transfer state, cancel action, completion, and failure feedback.
- Phone receives clear incoming-transfer state/prompt where required.

After completion, show a transient completion notice rather than preserving a history list. On failure, state what happened and offer retry, choose another file/destination, or dismiss as applicable.

## 13. Screen Hosting UI

Screen Hosting is full real-time mobile screen mirroring. The teacher starts it directly from the mobile main screen.

### Mobile requirements

- **Start Screen Hosting** action on main screen.
- Clear pre-start state indicating the selected/connected room.
- Obvious active-hosting indicator once mirroring starts.
- Persistent, easy-to-reach **Stop Screen Hosting** action.
- Connection/recovery status when stream quality or reachability changes.
- Quality and FPS settings in advanced Screen Hosting settings.
- Orientation-aware UI that remains usable in portrait and landscape.
- The teacher can continue operating the phone while hosting is active.

### PC requirements

- A Screen Hosting display window/panel shows the mirrored mobile screen.
- Fullscreen action is available.
- Current hosting/connection state is visible.
- Stop action is available from appropriate PC controls.
- Rotation is reflected clearly without confusing stale layout.

Exact handling of permission prompts and detailed streaming capability checks is **TBD**. On failure, show a clear failure state with retry/stop guidance.

## 14. Custom Commands UI

Custom Commands have a main-screen entry. It opens a concise set of command cards/buttons, such as Open Calculator, Open Browser, and Open Teaching Folder.

Each card/button shows display name, recognizable optional icon, enabled/disabled state, and optional confirmation requirement. Tapping an enabled command produces immediate pending feedback followed by success or failure feedback. Disabled commands are visibly unavailable and explain why when selected, if appropriate.

Command management—creating, editing, enabling/disabling, choosing approved local actions, and confirmation configuration—belongs in advanced settings. Do not expose arbitrary shell commands to the teacher as a normal UI feature.

## 15. Three-Dot Advanced Menu

The three-dot (⋮) menu is a grouped configuration entry point, not a long unstructured list. Use clear section grouping for:

- Mouse Settings
- IMU
- Presentation
- Screen Hosting
- Systems
- File Sharing
- Custom Commands
- Security
- General/App Settings

Each group should use a concise title, recognizable icon, and supporting description only when helpful. Frequently used teaching actions remain outside this menu.

## 16. Systems / Room Management UI

### Mobile setup and room flow

Provide screens or dialogs for Welcome, room registration, QR scanning, system selection, and OTP authentication.

| Stage | UI requirements |
|---|---|
| Welcome | Briefly state that the phone controls an Air Mouse classroom PC over Wi-Fi; guide teacher to register or select a room. |
| Room registration | Explain that the QR registers a room; provide QR scan entry and readable manual fallback only if defined later (TBD). |
| QR scanning | Clear camera/scan area, cancel action, and scan-success feedback. |
| System selection | Show saved room number, reachability, and connection state. Reachable systems should be easy to identify by room number. |
| OTP | Show selected room number, temporary OTP input, verifying state, expiry/error state, and cancel/back action. |

Do not expose internal addresses or technical identifiers as primary UI content. Room number is the teacher-facing identity.

## 17. PC First-Time Setup UI

PC onboarding must be simple enough for a teacher or classroom administrator without technical knowledge.

1. **Welcome screen:** Air Mouse name and a short purpose statement.
2. **Room number input:** clear label, validation/error state, and continue action.
3. **Generate QR:** action to generate the room-registration QR.
4. **Display QR:** prominent QR with instruction to scan it in the mobile app; clarify it is for registration.
5. **Waiting for mobile registration:** calm waiting state with room number visible.
6. **Setup completion:** confirmation that the PC is ready and can run from the system tray.

Detailed room-validation rules are **TBD**.

## 18. PC System-Tray UI

The PC receiver primarily operates in the background/system tray. The main teaching workflow must not require keeping a large PC window open.

The tray panel contains:

- Air Mouse branding/name.
- Room number.
- System status.
- Connected device state.
- Mouse status.
- Presentation status.
- Screen Hosting status.
- File Transfer status.
- Disconnect action.
- Settings / three-dot menu.

The panel should be compact, scannable, and use the same terminology and status meanings as the mobile app. Expanded detail should appear only when needed; it must not resemble a technical diagnostics console.

## 19. PC OTP UI

When authentication is required, show a compact OTP panel or overlay containing:

- Room number.
- Visually prominent temporary OTP.
- Expiration information.
- Waiting-for-mobile/verification state.

The OTP must be easy to read from a distance and easy to communicate/enter. On expiry, clearly replace or refresh the displayed code and state what the teacher should do next.

## 20. PC Teaching-Time Notifications

The PC remains unobtrusive during teaching. Use compact notifications for:

- Air Mouse connected.
- File received.
- Screen Hosting started.
- Disconnected.
- Transfer completed.
- Custom command execution result.

Notifications must not cover the presentation unnecessarily. They should be brief, dismiss automatically where appropriate, include an icon and readable text, and allow non-critical events to remain non-blocking.

## 21. PC Advanced Menu

PC advanced settings are under a three-dot menu, grouped as:

- Room
- Devices
- Control
- Screen Hosting
- File Sharing
- Custom Commands
- Security
- General

Maintain mobile terminology wherever possible. For example, both platforms use **Screen Hosting**, **Custom Commands**, and the same room-number naming. Exact PC-only settings are **TBD**, but they must not undermine the shared terminology or teacher-facing simplicity.

## 22. Responsive Layout

The mobile interface must accommodate different phone screen sizes and orientations. The PC interface must accommodate different desktop resolutions and tray-panel constraints. Do not depend on one fixed screen size.

- Use flexible spacing and responsive cards.
- Keep essential actions visible or readily reachable at compact sizes.
- Scale controls and typography without clipping or crowding.
- Use adaptive layout changes for orientation and available width.
- Keep touch targets at an accessible minimum size; exact dimensions are **TBD**.
- Ensure dialogs, OTP panels, file transfer panels, and hosting controls remain usable on constrained sizes.

## 23. Accessibility

- Provide adequate contrast for text and interactive controls in both themes.
- Use large touch targets and sufficient spacing.
- Make active/inactive, enabled/disabled, connected/disconnected, and success/error states clear.
- Do not rely on color alone; combine semantic color with label, icon, or shape.
- Pair icons with labels when their meaning is ambiguous.
- Use readable typography and avoid overly small secondary text.
- Write clear, human-readable error messages.
- Keep navigation predictable, with consistent close/back/cancel behavior.
- Avoid rapid, decorative, or excessive animation.

## 24. Interaction Feedback

| Event | Required feedback |
|---|---|
| Button press | Immediate pressed/selected visual state; subtle animation where appropriate. |
| Toggle | Clear on/off state and label/icon change. |
| Connection/disconnection | Status indicator update plus concise notification when meaningful. |
| Calibration | Preparing, countdown, sampling, completed/failed/cancelled state. |
| File transfer | Progress, active state, completion, cancellation, and error feedback. |
| Screen Hosting | Starting, active, reconnecting/failure, and stopped feedback. |
| Custom command | Pending, executed, rejected, disabled, or failed feedback. |
| Errors | Concise error message plus available recovery action. |

Use haptic feedback where available and appropriate on mobile, especially for successful calibration or meaningful control confirmation. Haptics are optional and configurable. Do not over-animate the app.

## 25. Error States

Every error state must explain what happened, whether the user must act, and what action is available.

| Error | Required UI content |
|---|---|
| No PC discovered | Explain no Air Mouse PC was found on current Wi-Fi; provide Retry and return/select-room action. |
| Room unavailable | Explain selected room is unavailable; provide Retry and system-selection action. |
| Wrong OTP | Explain code was not accepted; keep/reopen input and provide retry. |
| Expired OTP | Explain code expired; request the current PC code and provide retry. |
| Connection lost | Explain control is paused/lost; show reconnecting state or Retry/Disconnect actions. |
| Reconnection | Show ongoing reconnection without implying controls are usable until connected. |
| Sensor unavailable | Explain Gyro Mouse is unavailable; offer Trackpad or return action. |
| Calibration failure | Explain failure cause when available; offer Retry and Cancel. |
| File transfer failure | Identify failed transfer and reason where available; offer Retry, choose destination/file, or dismiss. |
| Screen Hosting failure | Explain hosting did not start/continue; offer Retry or Stop/return. |
| Custom command failure | Identify command and say it could not execute; offer dismiss/retry only where appropriate. |
| Unauthorized request | Explain action is not allowed; do not expose security internals. |

## 26. UI State Model

Use common states on both platforms, with shared labels and icon concepts.

| State | Mobile representation | PC representation |
|---|---|---|
| Disconnected | Neutral/error-aware connection area, Select/Retry action | Tray status shows waiting/disconnected |
| Discovering | Progress/scan state with reachable-room list updating | Receiver remains available/waiting |
| Awaiting Authentication | Selected room and OTP-entry UI | Prominent OTP and waiting-for-mobile state |
| Authenticating | Non-blocking verification progress; controls unavailable | Verification/waiting state |
| Connected | Room number, connected label, controls enabled | Tray panel shows connected device and active statuses |
| Reconnecting | Persistent reconnecting label; controls safely unavailable as needed | Tray panel shows reconnecting/unavailable state |
| Feature Active | Active badge/card/toggle and stop/pause action | Corresponding tray status or hosting panel status |
| Feature Error | Clear inline/overlay error and recovery action | Concise notification/panel error and recovery action |

State changes should be smooth and clear, but never hide critical changes behind animation alone.

## 27. Consistency Rules

Mobile and PC must share:

- The same terminology.
- The same feature names.
- The same icon concepts.
- The same status meanings.
- The same light/dark theme philosophy.
- The same glassmorphism language.
- Similar interaction patterns for status, confirmation, cancellation, and errors.

For example, if mobile uses **Screen Hosting**, PC uses **Screen Hosting**, not a different term such as casting, projection, or mirroring. If one platform shows a Warning state, the other uses the same semantic meaning and recognizable indicator.

## 28. UX Principles for Teaching

Classroom use favors action over configuration. The design must prioritize quick access, minimal distraction, large controls, visible state, few navigation steps, reliable feedback, and easy recovery from connection failures.

A teacher should not repeatedly open settings during a lecture. The app should make common actions usable with brief glances, avoid technical jargon, and preserve access to stop/disconnect actions whenever a control or stream is active.

## 29. Screen Inventory

### Mobile screens

| Screen | Preferred form |
|---|---|
| Welcome/setup | Full screen or onboarding flow |
| Room registration | Full screen, panel, or dialog |
| QR scanning | Full screen scanner/overlay |
| System selection | Full screen or bottom panel |
| OTP authentication | Dialog, panel, or full screen as layout requires |
| Main screen | Primary full screen |
| Gyro Mouse | Main-screen mode, dedicated view, or overlay |
| Trackpad | Dedicated full screen/view |
| Calibration | Focused dialog, overlay, or dedicated view |
| Laser Pointer | Main-screen mode, dedicated view, or overlay |
| Presentation Controls | Main-screen section/panel; must remain direct-access |
| File Sharing | File-selection flow and active transfer panel/dialog |
| Screen Hosting | Main-screen action plus active-hosting panel/state |
| Custom Commands | Panel or dedicated concise view |
| Advanced Settings | Grouped full screen or panel |
| Security | Advanced-settings sub-screen |
| General Settings | Advanced-settings sub-screen |

### PC screens/panels

| Screen/panel | Preferred form |
|---|---|
| First-time setup | Setup window/onboarding flow |
| Room configuration | Setup or settings panel |
| QR display | Setup view/dialog |
| System-tray panel | Tray view/popover |
| OTP panel | Compact dialog, panel, or tray view |
| Connected-device status | Tray-panel section or compact status view |
| Screen Hosting window | Dedicated display window/panel with fullscreen option |
| File transfer panel | Tray detail, compact window, or dialog |
| Custom Commands management | Advanced settings panel/window |
| Advanced Settings | Grouped settings panel/window |

Exact platform presentation forms are flexible; the behavior and visibility requirements above are mandatory.

## 30. Final UX Acceptance Criteria

The UI/UX is functionally complete when:

- [ ] A new teacher can understand the main screen without documentation.
- [ ] The connected room is immediately visible.
- [ ] Frequently used teaching functions are accessible from the main screen.
- [ ] Presentation Controls are not hidden in advanced settings.
- [ ] Calibration is directly accessible.
- [ ] Advanced configuration is grouped under the three-dot menu.
- [ ] Gyro Mouse and Trackpad are clearly distinguishable.
- [ ] Screen Hosting can be started and stopped easily.
- [ ] File transfers show clear progress plus completion or error feedback.
- [ ] No file-sharing history exists.
- [ ] PC software can remain unobtrusive in the system tray.
- [ ] Light and dark themes remain readable.
- [ ] Mobile and PC use a consistent glassmorphism design language.
- [ ] Error states clearly explain what happened and what the user can do next.

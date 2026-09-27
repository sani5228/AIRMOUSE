# Air Mouse for College Teaching — Presentation and Media Control Requirements

## 1. Purpose

Air Mouse lets a teacher control presentations and basic media playback on a classroom PC from a mobile phone over Wi-Fi:

**Mobile App → Wi-Fi → PC Receiver → OS/Application**

The mobile app sends semantic presentation commands. The PC receiver translates those commands into suitable local OS/application behavior. The phone does not need to know which presentation software is used.

This document defines presentation/media behavior, mobile UI, PC-side execution responsibility, feedback, error handling, session requirements, and classroom usability. It does not define implementation code, operating-system APIs, communication protocols, or application-specific integrations.

## 2. Required Presentation Controls

The following controls must be directly available from the mobile main screen and must not be hidden in advanced settings:

- Previous Slide
- Next Slide
- Start Presentation
- End Presentation
- Black/Blank Screen
- Volume Up
- Volume Down
- Mute
- Play/Pause

## 3. Control Categories

| Category | Controls |
|---|---|
| Slide Navigation | Previous, Next |
| Presentation State | Start Presentation, End Presentation, Black/Blank Screen |
| Media/System Controls | Volume Up, Volume Down, Mute, Play/Pause |

Use these categories consistently in documentation and UI.

## 4. Main-Screen UX

Presentation controls are frequent teaching actions. The mobile main screen uses large, easy-to-identify touch targets, clear icons, sufficient spacing, and concise labels where icon meaning could be unclear.

The layout minimizes accidental presses, shows immediate pressed/active feedback, requires minimal navigation, and remains usable while the teacher stands away from the PC. Exact pixel dimensions are not prescribed. Presentation Controls must remain directly reachable even while Screen Hosting or other supported features are active.

## 5. Semantic Commands

The mobile sends semantic actions, not arbitrary keyboard or shell commands. Conceptual command names include:

| Semantic command | Meaning |
|---|---|
| `NEXT_SLIDE` | Advance presentation by one slide/step. |
| `PREVIOUS_SLIDE` | Move presentation back by one slide/step. |
| `START_PRESENTATION` | Start or activate presentation mode. |
| `END_PRESENTATION` | End or exit presentation mode. |
| `BLANK_SCREEN` | Toggle or activate blank-screen behavior, subject to PC capability. |
| `VOLUME_UP` | Raise classroom PC/system audio level. |
| `VOLUME_DOWN` | Lower classroom PC/system audio level. |
| `MUTE` | Toggle or set mute behavior, subject to final capability. |
| `PLAY_PAUSE` | Toggle media playback state where supported. |

These names are conceptual. Actual message format is defined separately in `API_PROTOCOL.md`.

## 6. PC Command Execution

The PC receiver accepts an authorized semantic command and translates it into the appropriate local action. The architecture separates mobile command meaning from PC-specific execution.

For example, `NEXT_SLIDE` means “advance the presentation by one slide” regardless of the presentation application in use. The PC determines whether and how that action can be performed. Exact OS/application execution mechanism is **TBD**.

## 7. Presentation Application Independence

The mobile app must not be tightly coupled to one presentation application. The PC provides the presentation-control abstraction and may map semantic commands to a supported active presentation/media application.

Do not assume a particular presentation application. Application support, detection, and mapping are PC-side responsibilities and remain **TBD** unless configured later.

## 8. Start Presentation

When the teacher selects Start Presentation:

1. Mobile sends `START_PRESENTATION` through the authorized session.
2. PC attempts to start or activate appropriate presentation state.
3. Mobile provides lightweight sent/acknowledged feedback where applicable.
4. If PC reports/knows failure, mobile shows concise failure feedback.

If no suitable presentation application is available or active, the PC reports inability to execute rather than silently claiming success. Exact execution behavior is **TBD**.

## 9. End Presentation

When the teacher selects End Presentation, mobile sends `END_PRESENTATION`. The PC attempts to terminate or exit the active presentation mode according to supported PC behavior.

Use clear feedback when the action succeeds or fails where confirmation is available. If no presentation is active, show a concise non-misleading result such as no active presentation or command unavailable; exact wording is **TBD**. End Presentation may require accidental-action protection where appropriate, provided it does not impose repeated friction during teaching.

## 10. Slide Navigation

`NEXT_SLIDE` moves the presentation forward by one slide/step; `PREVIOUS_SLIDE` moves it backward by one slide/step.

Requirements:

- Low perceived latency.
- One action per deliberate tap.
- Avoid duplicate execution from a single tap.
- Continue working throughout an authenticated session.
- Do not queue/replay actions after disconnection.

## 11. Black/Blank Screen

Black/Blank Screen temporarily hides presentation content. The control provides activation and, where supported, deactivation/toggle behavior. It has a visible active state only when the PC can provide reliable state information; otherwise mobile must not claim to know the actual PC state.

When no presentation is active, PC handles the request according to supported capability and reports failure/unavailability where applicable. Exact toggle behavior and rendering/execution mechanism are **TBD**.

## 12. Volume Controls

Volume Up, Volume Down, and Mute operate on classroom PC/system audio. The mobile app must not assume a specific PC audio device.

| Control | Required behavior |
|---|---|
| Volume Up | One tap requests one increment/action; repeated taps request repeated changes. Repeat/hold behavior is **TBD**. |
| Volume Down | One tap requests one decrement/action; repeated taps request repeated changes. Repeat/hold behavior is **TBD**. |
| Mute | Requests mute toggle or defined mute state; visual state appears only if PC supplies reliable state. |

If the OS does not expose the requested operation, PC reports failure/unsupported state without crashing or leaving mobile in a misleading state.

## 13. Play/Pause

Play/Pause is a semantic media control. Where supported, it may control embedded presentation media, supported media playback, or other currently active media playback. Application mapping is handled entirely on the PC side.

Mobile sends `PLAY_PAUSE` and shows lightweight interaction feedback. Do not claim playing/paused state unless it is provided by the PC.

## 14. Button Behavior

A normal tap produces one command. Avoid accidental double execution, long delays, unnecessary confirmation dialogs for routine actions, and UI blocking after a command.

For volume or other controls where repeated activation is useful, long-press/repeat behavior is **TBD**. Buttons should show an immediate pressed state and return to usable state quickly. Confirmation should be reserved for actions where accidental activation has meaningful classroom disruption, if justified.

## 15. Feedback

Mobile feedback is lightweight and should not interrupt teaching.

| Event | Feedback |
|---|---|
| Command sent | Immediate pressed/brief sending state where useful. |
| Command acknowledged | Subtle success/state update where PC acknowledgement exists. |
| Command failed | Concise actionable error. |
| Connection lost | Persistent disconnected/reconnecting state. |
| PC unavailable | Clear unavailable status and retry/select-room path. |

Do not show a confirmation popup after every normal Next/Previous action.

## 16. Connection Requirements

Presentation controls require an authenticated connection to the selected classroom PC. Before authentication, commands cannot execute. After authentication, commands are permitted under session authorization.

If connection is lost, commands stop being sent and must not be queued indefinitely. Stale commands must not execute unexpectedly after reconnection. Session rules are defined in `PAIRING.md`.

## 17. Command Reliability

Discrete actions—including Next, Previous, Start, End, and Mute—must minimize accidental duplicate execution. Each conceptual command has command ID/type, session association, timestamp where required, and result/error state. The exact protocol is defined in `API_PROTOCOL.md`.

Reliability behavior must make a deliberate single tap produce at most the intended single local action under ordinary conditions. The final request/acknowledgement and duplicate-handling mechanism is **TBD**.

## 18. Reconnection Behavior

If connection drops during teaching, mobile shows disconnected/reconnecting status and stops sending presentation commands. Commands created while disconnected must not automatically execute later unless explicitly designed and documented; default requirement is no stale-command replay.

After recovery, clearly indicate when the connection is ready again. Reconnection and re-authentication behavior follows `PAIRING.md`.

## 19. PC Application Focus

The PC receiver may need to determine which application receives a presentation/media command. This is a PC-side responsibility. The mobile app does not directly manipulate PC application windows.

Exact focus/application-selection behavior is **TBD**. The PC must fail clearly rather than target an unintended application when it cannot safely determine a supported target.

## 20. Multiple Presentation Applications

Where multiple applications are installed, PC receiver must have a defined command-routing strategy. Possible approaches include active presentation application, user-configured application, supported-application detection, or explicit PC-side configuration.

Final strategy is **TBD**. The mobile UI continues to expose the same semantic controls regardless of the selected strategy.

## 21. Presentation State

Conceptual state may include presentation inactive, presentation active, blank screen active, media playing, media paused, muted, connected, and disconnected.

Distinguish state known by mobile from state actually known by PC. Mobile may show requested/pending state after a tap, but must not claim the true PC/application state unless the PC supplies that information.

## 22. Mobile UI State

| State | Mobile behavior |
|---|---|
| Disconnected | Controls disabled or clearly unavailable; room/connection status visible. |
| Connected | Controls available. |
| Sending | Optional brief lightweight sending feedback. |
| Failed | Concise error, with retry/reconnection guidance where useful. |
| Active | Mute or blank-screen active state shown only where reliably known. |

## 23. PC-Side UI

Presentation control does not require the PC receiver window to remain open. The receiver primarily operates in background/system tray.

PC-side UI may show presentation status, connected device, last relevant command/result, and errors. During teaching it must remain unobtrusive and avoid covering presentation content.

## 24. Notifications

Use compact PC/mobile notifications for important events, including presentation control connected, presentation control disconnected, presentation command failed, and blank screen activated where relevant. Screen Hosting interaction may produce related status where applicable.

Do not generate unnecessary notifications for every successful Next or Previous action.

## 25. Error Handling

| Condition | Required behavior |
|---|---|
| No presentation application active | Detect where possible; report command cannot currently execute. |
| Presentation application unavailable | Report unsupported/unavailable state; do not claim success. |
| Command unsupported | Report that requested semantic action is unsupported in current PC/application context. |
| PC unavailable | Show PC unavailable and stop command sending. |
| Authentication expired | Disable control; require valid session recovery. |
| Network failure | Show disconnected/reconnecting state; do not queue stale actions indefinitely. |
| Permission failure | Report PC could not perform action; keep UI usable. |
| PC execution failure | Return concise failure; do not crash. |
| Unknown command | PC rejects it safely. |
| Invalid session | PC rejects it and mobile returns to appropriate authentication/connection state. |

All errors must be detected where possible, communicated appropriately, avoid misleading UI state, and not crash either app.

## 26. Security

Presentation commands are privileged remote-control actions.

- Only authenticated sessions may issue commands.
- PC validates session authorization.
- Unknown/unauthorized commands are rejected.
- Mobile cannot send arbitrary OS commands.
- Presentation control must not become a remote shell mechanism.

Security/session details are defined by `PAIRING.md` and future protocol documentation, not here.

## 27. Classroom Usability

Design for real teaching: controls are usable without close inspection of the phone, have clear icons and spacing, avoid complex menus, offer immediate feedback, and remain reachable while supported features such as Screen Hosting are active.

Teachers should be able to navigate slides naturally while moving around the classroom. Do not require repeated opening of settings or a large PC window.

## 28. Future Extensibility

The semantic command model must accommodate additional commands later without redesigning the system. Potential future/TBD commands include First Slide, Last Slide, Start/Stop Media, Fullscreen, and Escape Presentation Mode.

These are not mandatory current features.

## 29. Testing Requirements

| Area | Tests |
|---|---|
| Slide control | Next, Previous, rapid repeated taps, deliberate single-tap behavior. |
| Presentation state | Start, End, blank screen, return from blank state where supported. |
| Audio | Volume Up, Volume Down, Mute. |
| Media | Play, Pause. |
| Connection | Disconnected state, reconnection, PC unavailable, authentication failure. |
| Reliability | Duplicate taps, delayed network, lost requests where applicable, stale-command prevention. |

Testing must verify user-facing feedback and that mobile never incorrectly implies confirmed PC state.

## 30. Acceptance Criteria

- [ ] Presentation controls are directly accessible from the mobile main screen.
- [ ] Next Slide works.
- [ ] Previous Slide works.
- [ ] Start Presentation works where supported.
- [ ] End Presentation works where supported.
- [ ] Black/Blank Screen works according to defined PC capability.
- [ ] Volume Up works.
- [ ] Volume Down works.
- [ ] Mute works.
- [ ] Play/Pause works.
- [ ] Commands require an authenticated session.
- [ ] Commands are semantic rather than arbitrary OS/shell commands.
- [ ] PC performs platform/application-specific execution.
- [ ] Duplicate command execution is minimized.
- [ ] Stale commands are not executed after reconnection.
- [ ] Connection failures are clearly communicated.
- [ ] PC operation remains unobtrusive during teaching.
- [ ] The design can be extended with additional presentation commands later.

This document is limited to presentation and media control. Pairing, mouse/IMU processing, screen hosting, and file-sharing requirements are defined separately.

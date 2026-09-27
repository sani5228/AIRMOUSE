# Product Requirements Document (PRD)

# Air Mouse for College Teaching

**Document Type:** Product Requirements Document  
**Product:** Air Mouse for College Teaching  
**Document Status:** Product Definition / Planning  
**Version:** 1.0

---

## 1. Product Overview

Air Mouse for College Teaching is a classroom teaching-control system that allows a teacher to control a classroom PC from a mobile phone over Wi-Fi.

The system consists of two software components:

```text
┌─────────────────────────────┐
│         Mobile App         │
│                             │
│  Teacher's Primary Control  │
│          Device             │
└──────────────┬──────────────┘
               │
               │ Direct Wi-Fi
               │ Communication
               ▼
┌─────────────────────────────┐
│     PC / System Receiver    │
│                             │
│  Classroom PC-side Control  │
└─────────────────────────────┘
```

There is no ESP32, Arduino, Bluetooth controller, or other intermediary hardware.

The mobile application provides the teacher-facing controls. The PC-side receiver receives authorized requests and performs the required local operating-system actions.

The product is designed primarily for college classrooms, lectures, presentations, projectors, and large displays.

---

# 2. Problem Statement

Teachers frequently need to remain near the classroom PC in order to:

- Move the mouse.
- Change presentation slides.
- Start or stop presentations.
- Control media playback.
- Adjust volume.
- Point at content on screen.
- Transfer teaching material.
- Display content from a mobile device.

Remaining beside the PC can interrupt movement around the classroom and reduce the flexibility of teaching.

Air Mouse addresses this problem by turning the teacher's mobile phone into a wireless classroom-control device.

The product must provide the most frequently required classroom actions from a simple mobile interface while keeping advanced configuration available without cluttering the teaching workflow.

---

# 3. Target Users

## 3.1 Primary Users

- College teachers
- Lecturers
- Faculty members conducting classroom presentations

## 3.2 Primary Environment

- College classrooms
- Classroom PCs
- Projectors
- Large displays
- Local classroom Wi-Fi networks

## 3.3 User Characteristics

The product should assume that the teacher:

- May not have technical knowledge.
- Needs quick access to common controls.
- May operate the phone while standing or moving around the classroom.
- May use multiple classroom PCs.
- May need to transfer files immediately before or during teaching.
- May switch between portrait and landscape phone orientation.
- Should not need to understand the underlying networking architecture.

---

# 4. Product Goals

| ID | Goal |
|---|---|
| GOAL-01 | Replace the need for the teacher to remain beside the classroom PC during common teaching operations. |
| GOAL-02 | Provide wireless mouse control from the mobile phone. |
| GOAL-03 | Provide frequently used presentation controls directly from the mobile main screen. |
| GOAL-04 | Make switching between multiple classroom PCs simple through room numbers. |
| GOAL-05 | Provide secure authorization through a temporary OTP. |
| GOAL-06 | Provide quick two-way teaching-file transfer. |
| GOAL-07 | Provide full real-time mobile screen mirroring. |
| GOAL-08 | Provide a convenient laser-pointer mode. |
| GOAL-09 | Provide predefined and allowlisted PC commands. |
| GOAL-10 | Operate without additional hardware. |
| GOAL-11 | Keep the product simple enough for teachers without technical knowledge. |

---

# 5. Product Principles

The product must follow these principles:

1. **Mobile-first classroom control**
2. **Direct mobile-to-PC Wi-Fi communication**
3. **Minimal teaching interface**
4. **Advanced settings separated from frequent actions**
5. **Room-number-based classroom identification**
6. **Explicit OTP-based session authorization**
7. **Allowlisted PC commands**
8. **No unnecessary hardware**
9. **Simple user-facing errors**
10. **Consistent mobile and PC visual design**

---

# 6. System Architecture

## 6.1 Logical Architecture

```text
Teacher
   │
   ▼
Mobile App
   │
   │ Wi-Fi
   │
   ▼
PC / System Receiver
   │
   ├── OS Mouse/Input Control
   ├── Presentation Control
   ├── Media/Volume Control
   ├── Laser Pointer Display
   ├── File Transfer
   ├── Screen Hosting Receiver
   └── Allowlisted Custom Commands
```

## 6.2 Architecture Requirements

| ID | Requirement |
|---|---|
| ARCH-01 | The mobile application shall communicate directly with the PC receiver over Wi-Fi. |
| ARCH-02 | No ESP32, Arduino, Bluetooth controller, or other intermediary hardware shall be required. |
| ARCH-03 | The PC receiver shall perform the required OS-level actions locally. |
| ARCH-04 | Control features shall require an authorized mobile session. |
| ARCH-05 | The system shall not assume that every device on the local Wi-Fi network is trusted. |
| ARCH-06 | The architecture shall support multiple classroom PCs identified by room number. |
| ARCH-07 | The architecture shall support interruption and reconnection handling. |

---

# 7. Core Product Modules

The product consists of the following functional areas:

1. Mobile application
2. PC receiver
3. Room/system management
4. Session authorization
5. Gyro Mouse
6. Touchpad Mouse
7. Presentation Controls
8. Laser Pointer
9. File Sharing
10. Screen Hosting
11. Custom Commands
12. Settings and configuration
13. Connection and error handling

---

# 8. Mobile Application Requirements

## 8.1 Main Screen

The mobile main screen is the primary teaching interface.

It shall contain:

- Connected room/system
- Gyro Mouse
- Trackpad
- Laser Pointer
- Calibrate
- Presentation controls
- Share File
- Screen Hosting
- Custom Commands

### Requirements

| ID | Requirement |
|---|---|
| MOB-001 | The main screen shall prioritize frequently used teaching actions. |
| MOB-002 | The main screen shall show the currently connected room/system. |
| MOB-003 | The main screen shall provide direct access to Gyro Mouse. |
| MOB-004 | The main screen shall provide direct access to Trackpad. |
| MOB-005 | The main screen shall provide direct access to Laser Pointer. |
| MOB-006 | The main screen shall provide direct access to calibration. |
| MOB-007 | Presentation controls shall remain directly accessible on the main screen. |
| MOB-008 | The main screen shall provide direct access to file sharing. |
| MOB-009 | The main screen shall provide direct access to Screen Hosting. |
| MOB-010 | The main screen shall provide direct access to Custom Commands. |
| MOB-011 | Technical configuration controls shall not overcrowd the main screen. |
| MOB-012 | Connection state shall be understandable without requiring the user to inspect technical information. |

---

# 9. Advanced Settings

Advanced configuration shall be grouped under a three-dot (`⋮`) menu.

## 9.1 Required Settings Groups

| Section | Expected Contents |
|---|---|
| Mouse Settings | Touchpad and mouse behavior |
| IMU | Axis mapping, sensitivity, filtering, drift, calibration |
| Presentation | Presentation behavior/preferences |
| Screen Hosting | Quality, FPS, display-related options |
| Systems | Saved rooms and classroom-system management |
| File Sharing | Download location and transfer preferences |
| Custom Commands | Command definitions and management |
| Security | Session/security-related settings |
| General | General application preferences |

### Requirements

| ID | Requirement |
|---|---|
| SET-001 | Advanced settings shall be accessible from a three-dot (`⋮`) menu. |
| SET-002 | Advanced settings shall be grouped by function. |
| SET-003 | Technical settings shall not be required for normal teaching use. |
| SET-004 | Changing an advanced setting shall not require re-pairing unless explicitly necessary for the setting being changed. |
| SET-005 | Settings shall use understandable labels rather than implementation-specific terminology where possible. |

---

# 10. Gyro Mouse

The Gyro Mouse allows the phone's IMU/gyroscope to control the PC cursor.

## 10.1 Axis Modes

The system shall support:

- X axis
- Y axis
- Z axis
- XY mode
- XZ mode
- YZ mode

The selected mode determines which phone motion axes contribute to cursor movement.

## 10.2 Axis Inversion

The user shall be able to independently invert:

- X
- Y
- Z

## 10.3 Sensitivity

The system shall support:

- Overall sensitivity
- Per-axis sensitivity

Overall sensitivity shall provide a general adjustment, while per-axis sensitivity shall allow independent adjustment of the selected axes.

## 10.4 Cursor Behavior

The system shall provide:

- Cursor acceleration
- Dead zone
- Noise filtering
- Drift compensation

## 10.5 Calibration

The mobile application shall provide an IMU calibration workflow.

Calibration shall:

1. Clearly instruct the user what to do.
2. Capture the required stationary reference information.
3. Indicate calibration progress.
4. Report successful completion.
5. Report failure when calibration cannot be completed reliably.
6. Allow the user to retry calibration.

## 10.6 Mouse Lock/Pause

The user shall be able to temporarily stop cursor movement without disconnecting the session.

Mouse lock/pause shall:

- Stop Gyro Mouse movement.
- Preserve the connection.
- Allow the user to resume movement.
- Not terminate the authorized session.

## 10.7 Gyro Mouse Requirements

| ID | Requirement |
|---|---|
| GYR-001 | Phone IMU/gyroscope movement shall control the PC cursor. |
| GYR-002 | X-axis control shall be supported. |
| GYR-003 | Y-axis control shall be supported. |
| GYR-004 | Z-axis control shall be supported. |
| GYR-005 | XY axis mode shall be supported. |
| GYR-006 | XZ axis mode shall be supported. |
| GYR-007 | YZ axis mode shall be supported. |
| GYR-008 | X inversion shall be configurable. |
| GYR-009 | Y inversion shall be configurable. |
| GYR-010 | Z inversion shall be configurable. |
| GYR-011 | Overall sensitivity shall be configurable. |
| GYR-012 | Per-axis sensitivity shall be configurable. |
| GYR-013 | Cursor acceleration shall be configurable. |
| GYR-014 | Dead zone shall be configurable. |
| GYR-015 | IMU noise filtering shall be supported. |
| GYR-016 | Drift compensation shall be supported. |
| GYR-017 | IMU calibration shall be available from the mobile application. |
| GYR-018 | Gyro Mouse movement shall be pausable/locked. |
| GYR-019 | Calibration shall not require the user to interact with the PC. |

---

# 11. Touchpad Mouse

The mobile touchscreen shall provide a conventional laptop-style touchpad.

## 11.1 Required Gestures

| Gesture | Action |
|---|---|
| Touch and move | Cursor movement |
| Tap | Left click |
| Two-finger tap | Right click |
| Double tap | Double click |
| Two-finger vertical swipe | Vertical scroll |
| Two-finger horizontal swipe | Horizontal scroll |
| Tap/hold and move | Drag |

## 11.2 Requirements

| ID | Requirement |
|---|---|
| TRK-001 | The mobile screen shall support touchpad cursor movement. |
| TRK-002 | A single tap shall perform a left click. |
| TRK-003 | A two-finger tap shall perform a right click. |
| TRK-004 | A double tap shall perform a double click. |
| TRK-005 | Two-finger vertical movement shall provide vertical scrolling. |
| TRK-006 | Two-finger horizontal movement shall provide horizontal scrolling. |
| TRK-007 | Tap/hold and move shall support drag operations. |
| TRK-008 | Touchpad sensitivity shall be configurable through advanced settings. |
| TRK-009 | Touchpad controls shall remain usable without requiring technical configuration. |

---

# 12. Presentation Controls

Presentation controls are high-frequency teaching actions and must be directly accessible from the main mobile screen.

## 12.1 Required Controls

- Next slide
- Previous slide
- Start presentation
- End presentation
- Black/blank screen
- Volume up
- Volume down
- Mute
- Play/pause

## 12.2 Requirements

| ID | Requirement |
|---|---|
| PRE-001 | Next slide shall be available directly on the mobile main screen. |
| PRE-002 | Previous slide shall be available directly on the mobile main screen. |
| PRE-003 | Start presentation shall be available directly on the mobile main screen. |
| PRE-004 | End presentation shall be available directly on the mobile main screen. |
| PRE-005 | Black/blank screen shall be available directly on the mobile main screen. |
| PRE-006 | Volume up shall be available directly on the mobile main screen. |
| PRE-007 | Volume down shall be available directly on the mobile main screen. |
| PRE-008 | Mute shall be available directly on the mobile main screen. |
| PRE-009 | Play/pause shall be available directly on the mobile main screen. |
| PRE-010 | These frequently used controls shall not be hidden inside advanced settings. |
| PRE-011 | Presentation commands shall provide clear feedback when the mobile session is disconnected. |

---

# 13. Laser Pointer

The product shall provide a laser-pointer mode controlled from the mobile phone.

## 13.1 User Flow

```text
Main Screen
    │
    ▼
Laser Pointer
    │
    ▼
Start Pointer Mode
    │
    ▼
Move Phone / Pointer Input
    │
    ▼
PC Renders Pointer
    │
    ▼
Stop Pointer Mode
```

## 13.2 Pointer Rendering

The PC shall render a visible pointer indicator on the classroom display.

The pointer position shall be controlled by mobile input.

The product must define the pointer as a software-rendered classroom presentation aid.

The pointer shall:

- Be clearly visible against typical presentation backgrounds.
- Follow mobile pointer input with low perceptible delay.
- Remain constrained to the display area.
- Stop updating when pointer mode is stopped.
- Stop receiving movement commands when the session becomes unauthorized or disconnected.

## 13.3 Requirements

| ID | Requirement |
|---|---|
| LAS-001 | The mobile app shall provide a Start Laser Pointer action. |
| LAS-002 | The mobile app shall provide a Stop Laser Pointer action. |
| LAS-003 | Mobile pointer input shall control the pointer position on the PC. |
| LAS-004 | The PC shall render the pointer on the appropriate presentation display. |
| LAS-005 | Pointer movement shall remain within the active display bounds. |
| LAS-006 | Pointer control shall require an authorized session. |
| LAS-007 | Pointer activity shall stop when the authorized session is lost. |

---

# 14. File Sharing

Air Mouse shall support two-way file transfer.

## 14.1 Phone to PC

The teacher shall be able to:

1. Open Share File.
2. Select a file from the mobile device.
3. Start the transfer.
4. View transfer progress.
5. Cancel the transfer if required.
6. Receive a completion or failure notification.

The file shall be saved to the configured PC download location.

## 14.2 PC to Phone

The PC shall be able to initiate a file transfer to the authorized mobile device.

The mobile application shall:

1. Receive the transfer request.
2. Clearly identify that a file is being transferred.
3. Allow the transfer to proceed.
4. Show transfer progress.
5. Report completion or failure.

## 14.3 Requirements

| ID | Requirement |
|---|---|
| FILE-001 | Phone-to-PC transfer shall be supported. |
| FILE-002 | PC-to-phone transfer shall be supported. |
| FILE-003 | The mobile app shall provide file selection for phone-to-PC transfer. |
| FILE-004 | Transfer progress shall be displayed. |
| FILE-005 | Transfer completion shall be reported. |
| FILE-006 | Transfer failure shall be reported clearly. |
| FILE-007 | The user shall be able to cancel an active transfer. |
| FILE-008 | The PC download location shall be configurable. |
| FILE-009 | File transfers shall require an authorized session. |
| FILE-010 | The receiver shall validate the configured destination path before writing a file. |
| FILE-011 | File-sharing history shall not be required in the current version. |

---

# 15. Screen Hosting

## 15.1 Definition

Screen Hosting means **full real-time mirroring of the mobile device screen to the connected PC**.

It is not limited to:

- Individual files
- Images
- PDFs
- Selected applications

The intended behavior is full mobile-screen mirroring.

## 15.2 Screen Hosting Flow

```text
Mobile App
    │
    ▼
Screen Hosting
    │
    ▼
Start Hosting
    │
    ▼
Capture Complete Mobile Screen
    │
    ▼
Stream Over Wi-Fi
    │
    ▼
PC Receives Stream
    │
    ▼
PC Displays Mobile Screen
```

## 15.3 Required Features

- Full mobile screen capture
- Real-time streaming
- Low latency
- Portrait support
- Landscape support
- Start/stop
- PC fullscreen display
- Display selection where supported
- Quality configuration
- FPS configuration
- Network interruption handling

## 15.4 Network Interruption

If the network temporarily interrupts:

1. Screen streaming shall stop or enter a recoverable state.
2. The user shall receive a clear status indication.
3. The PC shall not display stale content as if it were still live.
4. The system should attempt reconnection where appropriate.
5. The user shall be able to restart Screen Hosting without restarting the entire application.

## 15.5 Requirements

| ID | Requirement |
|---|---|
| SCR-001 | Screen Hosting shall mirror the complete mobile screen. |
| SCR-002 | Screen Hosting shall operate in real time. |
| SCR-003 | Screen Hosting shall use the direct Wi-Fi connection. |
| SCR-004 | Portrait orientation shall be supported. |
| SCR-005 | Landscape orientation shall be supported. |
| SCR-006 | The user shall be able to start Screen Hosting. |
| SCR-007 | The user shall be able to stop Screen Hosting. |
| SCR-008 | The PC shall support fullscreen display of the mirrored screen. |
| SCR-009 | Display selection shall be supported where the PC environment supports multiple displays. |
| SCR-010 | Quality configuration shall be provided. |
| SCR-011 | FPS configuration shall be provided. |
| SCR-012 | Screen Hosting shall require an authorized session. |
| SCR-013 | Network interruption shall be handled gracefully. |
| SCR-014 | Stale screen content shall not be presented as a live stream after the connection is lost. |

---

# 16. Custom Commands

Custom Commands provide controlled execution of predefined local PC actions.

## 16.1 Examples

- Open Calculator
- Open Browser
- Open Teaching Folder

## 16.2 Command Model

The mobile application shall send a command identifier/request.

The PC receiver shall:

1. Receive the request.
2. Validate the command identifier.
3. Check the identifier against an allowlist.
4. Resolve the identifier to a predefined local action.
5. Execute the approved action.
6. Return a success or failure status where appropriate.

## 16.3 Security Boundary

Custom Commands shall **not** provide unrestricted remote shell, terminal, or arbitrary command execution.

The phone shall not be able to submit arbitrary OS commands for execution.

## 16.4 Requirements

| ID | Requirement |
|---|---|
| CMD-001 | The mobile app shall provide a Custom Commands section. |
| CMD-002 | Teachers shall be able to use predefined commands. |
| CMD-003 | The product shall support management of approved custom commands. |
| CMD-004 | Each command shall correspond to an approved local PC action. |
| CMD-005 | The mobile app shall send a command identifier/request rather than unrestricted shell text. |
| CMD-006 | The PC receiver shall validate every command against an allowlist. |
| CMD-007 | Unrecognized command identifiers shall be rejected. |
| CMD-008 | Unauthorized command requests shall be rejected. |
| CMD-009 | The current version shall not provide unrestricted remote shell or terminal access. |

---

# 17. Multi-PC and Room Management

The product must support teachers who use multiple classroom PCs.

The user-facing identity of each classroom system is its **room number**.

---

# 18. First-Time Classroom PC Setup

## 18.1 Flow

```text
PC Receiver Starts
       │
       ▼
First-Time Setup
       │
       ▼
Teacher/Administrator Enters Room Number
       │
       ▼
PC Generates QR Code
       │
       ▼
Teacher Scans QR Code
       │
       ▼
Mobile App Stores Room Number
```

## 18.2 QR Requirements

The QR code shall contain **only the room number**.

The QR code shall not be treated as:

- A password
- An authentication token
- An authorization credential
- A permanent session credential

## 18.3 Requirements

| ID | Requirement |
|---|---|
| ROOM-001 | The PC shall request a room number during first-time setup. |
| ROOM-002 | The PC shall generate a QR code containing only the room number. |
| ROOM-003 | The mobile app shall scan the QR code. |
| ROOM-004 | The mobile app shall store the scanned room number. |
| ROOM-005 | QR scanning shall not itself authorize control of the PC. |
| ROOM-006 | The room number shall be the primary user-facing identity of the classroom system. |

---

# 19. Normal Connection Workflow

## 19.1 Flow

```text
Open Mobile App
      │
      ▼
Discover Available Air Mouse PCs
      │
      ▼
Match With Stored Room Information
      │
      ▼
Display Available Systems By Room Number
      │
      ▼
Teacher Selects Room
      │
      ▼
Selected PC Displays Temporary OTP
      │
      ▼
Teacher Enters OTP
      │
      ▼
PC Verifies OTP
      │
      ▼
Session Authorized
      │
      ▼
Teaching Controls Enabled
```

## 19.2 Requirements

| ID | Requirement |
|---|---|
| CON-001 | The mobile app shall discover available Air Mouse systems on the current Wi-Fi. |
| CON-002 | The mobile app shall compare discovered systems with stored room information where applicable. |
| CON-003 | Available systems shall be presented using room numbers as the primary user-facing identity. |
| CON-004 | The teacher shall be able to select a room. |
| CON-005 | The selected PC shall generate/display a temporary OTP. |
| CON-006 | The teacher shall enter the OTP into the mobile app. |
| CON-007 | The PC shall verify the OTP. |
| CON-008 | The mobile session shall become authorized only after successful OTP verification. |
| CON-009 | Selecting a room shall not itself grant control authorization. |
| CON-010 | The app shall clearly show whether the session is connected and authorized. |

---

# 20. Session Authorization and OTP

## 20.1 OTP Requirements

| ID | Requirement |
|---|---|
| SEC-001 | An OTP shall be required for session authorization. |
| SEC-002 | OTPs shall be temporary. |
| SEC-003 | Expired OTPs shall be rejected. |
| SEC-004 | Incorrect OTPs shall be rejected. |
| SEC-005 | The system shall provide understandable feedback after an invalid OTP. |
| SEC-006 | Successful OTP verification shall authorize the selected mobile session. |
| SEC-007 | The QR code shall not replace OTP authorization. |
| SEC-008 | Session authorization shall have a controlled lifetime. |
| SEC-009 | The system shall not treat the local Wi-Fi network as automatically trusted. |

Exact OTP length, expiration duration, retry limits, and session lifetime are product parameters that require security review and validation before implementation and are therefore **TBD**.

---

# 21. Connection States

The mobile application shall distinguish important connection states.

Recommended user-facing states:

- Not connected
- Discovering
- Room selected
- Waiting for OTP
- Authorizing
- Connected
- Authorized
- Disconnected
- Reconnecting
- Authorization expired
- Connection failed

The PC receiver should similarly expose appropriate state information through its UI.

The UI should use simple language and avoid exposing unnecessary technical networking information.

---

# 22. PC Receiver UI

The PC software shall primarily operate as a background/system-tray application.

## 22.1 Required UI States

### Initial Setup

Allows the user to:

- Enter room number
- Confirm room number
- Generate QR code

### OTP Display

Displays the temporary OTP when a mobile device attempts to authorize.

### Connected State

Shows that an authorized mobile device is controlling the system.

### Disconnected State

Shows that the mobile session is no longer active.

### Configuration

Provides relevant PC-side configuration.

### Screen Mirroring

Provides the display surface for Screen Hosting.

## 22.2 Requirements

| ID | Requirement |
|---|---|
| PC-001 | The PC application shall normally run in the system tray/background. |
| PC-002 | The PC UI shall remain unobtrusive during normal teaching. |
| PC-003 | First-time setup shall be available. |
| PC-004 | Room number configuration shall be available. |
| PC-005 | QR generation shall be available. |
| PC-006 | Temporary OTP display shall be available. |
| PC-007 | Connection status shall be visible when needed. |
| PC-008 | Configuration/settings shall be available. |
| PC-009 | Screen mirroring shall provide an appropriate display surface. |

---

# 23. Mobile UI / UX Requirements

## 23.1 Main Screen Layout

The main screen should prioritize the following hierarchy:

1. Connection/room status
2. Mouse controls
3. Laser Pointer
4. Calibration
5. Presentation controls
6. File Sharing
7. Screen Hosting
8. Custom Commands

The exact visual arrangement may be refined during UI design, but the listed actions must remain quickly accessible.

## 23.2 Main Screen Restrictions

The main screen should not be overloaded with:

- IMU tuning controls
- Network details
- Security configuration
- File-transfer technical settings
- Screen-stream technical parameters
- Command management details
- Other advanced configuration

Those controls belong under the three-dot menu.

---

# 24. Visual Design Requirements

Both mobile and PC applications shall use a shared glassmorphism design language.

## 24.1 Required Characteristics

- Translucent glass surfaces
- Background blur
- Rounded corners
- Subtle borders
- Soft shadows
- Clear typography
- Consistent iconography
- Smooth animations
- Light theme
- Dark theme
- Good readability
- Good contrast

## 24.2 Cross-Platform Consistency

The mobile and PC interfaces should feel like parts of one product.

They should share:

- Visual hierarchy
- Typography principles
- Icon style
- Glass panel treatment
- Border treatment
- Corner-radius language
- Animation behavior
- Light/dark theme concepts

---

# 25. Accessibility and Usability

The product shall prioritize usability for teachers in real classroom environments.

## Requirements

| ID | Requirement |
|---|---|
| UX-001 | Primary controls shall be easy to identify. |
| UX-002 | Important controls shall have readable labels or recognizable icons. |
| UX-003 | Text and controls shall maintain adequate contrast. |
| UX-004 | Critical states shall not depend on color alone. |
| UX-005 | Touch targets shall be suitable for use while standing or moving. |
| UX-006 | Error messages shall explain what the user should do next where possible. |
| UX-007 | Technical implementation details shall be hidden from normal teaching workflows. |
| UX-008 | The product shall support both light and dark themes. |

---

# 26. Security Requirements

## 26.1 Authorization

- OTP is required for session authorization.
- OTP must expire.
- Invalid OTPs must be rejected.
- Session lifetime must be controlled.
- Unauthorized devices must not receive control privileges.

## 26.2 Command Security

- Custom commands must use an allowlist.
- Arbitrary shell execution is not permitted by the product requirements.
- Unknown command identifiers must be rejected.
- The receiver must map approved identifiers to predefined local actions.

## 26.3 File Security

- File transfers require an authorized session.
- Destination paths must be validated.
- The system must not allow a transfer request to bypass the configured destination restrictions.

## 26.4 Session Security

- Mouse control requires an authorized session.
- Presentation commands require an authorized session.
- Laser Pointer requires an authorized session.
- File transfer requires an authorized session.
- Screen Hosting requires an authorized session.
- Custom Commands require an authorized session.

## 26.5 Stale Connection Protection

When a connection becomes invalid or is lost:

- New control commands must not continue to execute.
- Stale mouse input must not continue indefinitely.
- Active pointer movement must stop.
- Screen Hosting must stop or clearly enter a disconnected state.
- Active file operations must fail or enter an explicitly defined recoverable state.

---

# 27. Reliability Requirements

The system shall gracefully handle:

- Wi-Fi unavailable
- PC unavailable
- Mobile disconnected
- PC disconnected
- OTP expiration
- Wrong OTP
- Network changes
- Temporary network interruption
- File-transfer failure
- Screen-mirroring interruption
- Sensor/calibration problems

## Reliability Behavior

### Wi-Fi Unavailable

The app shall clearly indicate that no connection can currently be established.

### PC Unavailable

The unavailable system shall not be presented as successfully connected.

### Mobile Disconnect

The PC shall stop accepting stale control input from the disconnected mobile session.

### PC Disconnect

The mobile application shall show that the classroom system is disconnected.

### OTP Expiration

The user shall be told that the OTP expired and shall be able to initiate another authorization attempt.

### Wrong OTP

The user shall receive a clear invalid-OTP message and remain unauthorized.

### Network Change

The application should detect that the previous connection is no longer valid and transition to an appropriate discovery/reconnection state.

### File Transfer Failure

The user shall receive a clear failure status rather than a false completion message.

### Screen Hosting Interruption

The PC shall not present stale content as live.

### Sensor/Calibration Problem

The mobile app shall indicate that calibration or sensor data is unavailable or unreliable and provide an appropriate recovery action.

---

# 28. Performance and Non-Functional Requirements

The product must provide a responsive classroom experience.

## 28.1 Mouse Latency

Mouse movement should feel immediate and responsive during normal Wi-Fi conditions.

No fixed numerical latency target is defined at this stage.

**Target:** TBD after prototype benchmarking.

## 28.2 Presentation Command Latency

Presentation actions should respond quickly enough for normal teaching use.

**Target:** TBD after prototype benchmarking.

## 28.3 Screen Hosting

Screen Hosting should provide:

- Low perceptible latency
- Stable playback
- Appropriate quality for classroom display
- Configurable FPS
- Configurable quality

Exact latency, FPS, bitrate, resolution, and resource targets are **TBD** after technical feasibility testing.

## 28.4 Resource Usage

The system should use reasonable CPU, memory, battery, and network resources for the intended classroom environment.

Exact limits are **TBD** after benchmarking.

## 28.5 Reliability

The system should recover gracefully from normal temporary network interruptions without requiring unnecessary application restarts.

## 28.6 Compatibility

The product should support a practical range of mobile devices and classroom PCs where the required operating-system capabilities are available.

Exact supported operating-system versions and device requirements are **TBD** during technical validation.

---

# 29. Error Handling Requirements

Errors should be understandable to non-technical users.

## Preferred Error Structure

An error should communicate:

1. What happened.
2. Whether the user needs to do something.
3. What action can be taken next.

Example:

> **Connection lost**  
> The classroom PC is no longer reachable. Check the Wi-Fi connection and try again.

Avoid exposing unnecessary low-level details such as internal networking errors unless shown in an appropriate diagnostic area.

---

# 30. Key User Flows

# 30.1 First-Time Classroom Registration

```text
PC
 │
 ├─ Start receiver
 │
 ├─ Request room number
 │
 ├─ Store room number
 │
 └─ Generate QR
        │
        ▼
Teacher
 │
 ├─ Open mobile app
 │
 ├─ Scan QR
 │
 └─ Room number stored
```

**Important:** Scanning the QR does not authorize the mobile device.

---

# 30.2 Normal Classroom Connection

```text
Teacher
 │
 ├─ Open app
 │
 ├─ Discover systems
 │
 ├─ Select room
 │
 ▼
PC
 │
 └─ Display temporary OTP
      │
      ▼
Teacher
 │
 ├─ Enter OTP
 │
 ▼
PC
 │
 ├─ Verify OTP
 │
 └─ Authorize session
      │
      ▼
Teaching Controls Available
```

---

# 30.3 Gyro Mouse

```text
Teacher
 │
 ├─ Select Gyro Mouse
 │
 ├─ Calibrate if required
 │
 ├─ Select axis mode
 │
 └─ Move phone
       │
       ▼
    Wi-Fi
       │
       ▼
PC Receiver
 │
 └─ Move OS cursor
```

---

# 30.4 Touchpad

```text
Teacher
 │
 ├─ Select Trackpad
 │
 └─ Touch screen
       │
       ▼
    Wi-Fi
       │
       ▼
PC Receiver
 │
 └─ Perform mouse action
```

---

# 30.5 Presentation Control

```text
Teacher
 │
 └─ Tap presentation action
       │
       ▼
    Wi-Fi
       │
       ▼
PC Receiver
 │
 └─ Perform corresponding presentation/media action
```

---

# 30.6 Laser Pointer

```text
Teacher
 │
 ├─ Start Laser Pointer
 │
 ├─ Move phone
 │
 ▼
PC
 │
 └─ Render software pointer
 │
 ▼
Teacher
 │
 └─ Stop Laser Pointer
```

---

# 30.7 Phone-to-PC File Transfer

```text
Teacher
 │
 ├─ Share File
 ├─ Select file
 └─ Start transfer
       │
       ▼
    Wi-Fi
       │
       ▼
PC
 │
 ├─ Validate destination
 ├─ Receive file
 └─ Report completion
```

---

# 30.8 PC-to-Phone File Transfer

```text
PC
 │
 └─ Initiate file transfer
       │
       ▼
Mobile
 │
 ├─ Receive request
 ├─ Transfer file
 └─ Report completion
```

---

# 30.9 Screen Hosting

```text
Teacher
 │
 ├─ Screen Hosting
 ├─ Configure if required
 └─ Start
       │
       ▼
Mobile Screen Capture
       │
       ▼
Wi-Fi Stream
       │
       ▼
PC Receiver
       │
       ▼
Fullscreen / Selected Display
```

---

# 30.10 Custom Command

```text
Teacher
 │
 └─ Select predefined command
       │
       ▼
Mobile sends command identifier
       │
       ▼
PC validates allowlist
       │
       ├── Approved ──► Execute predefined action
       │
       └── Rejected ──► Do not execute
```

---

# 31. Acceptance Criteria

The following criteria define minimum expected product behavior.

## 31.1 Gyro Mouse Acceptance Criteria

### AC-GYR-01
**Given** the mobile device is authorized and calibrated,  
**when** the teacher moves the phone,  
**then** the PC cursor moves according to the configured axis mapping and sensitivity.

### AC-GYR-02
**Given** XY mode is selected,  
**when** the phone moves on the configured X/Y axes,  
**then** cursor movement uses those axes and does not require Z-axis input.

### AC-GYR-03
**Given** an axis is inverted,  
**when** the phone moves along that axis,  
**then** cursor movement occurs in the opposite configured direction.

### AC-GYR-04
**Given** the teacher changes sensitivity,  
**when** the same physical movement is performed,  
**then** cursor movement reflects the updated sensitivity.

### AC-GYR-05
**Given** Mouse Lock/Pause is enabled,  
**when** the teacher moves the phone,  
**then** Gyro Mouse cursor movement does not occur.

### AC-GYR-06
**Given** the user starts calibration,  
**when** the calibration procedure completes successfully,  
**then** the app reports successful calibration and uses the resulting calibration state.

---

# 31.2 Touchpad Acceptance Criteria

### AC-TRK-01
**Given** the touchpad is active,  
**when** the teacher touches and moves a finger,  
**then** the PC cursor moves.

### AC-TRK-02
**Given** the touchpad is active,  
**when** the teacher taps once,  
**then** the PC performs a left click.

### AC-TRK-03
**Given** the touchpad is active,  
**when** the teacher performs a two-finger tap,  
**then** the PC performs a right click.

### AC-TRK-04
**Given** the touchpad is active,  
**when** the teacher double taps,  
**then** the PC performs a double click.

### AC-TRK-05
**Given** the touchpad is active,  
**when** the teacher performs a two-finger vertical swipe,  
**then** the PC performs vertical scrolling.

### AC-TRK-06
**Given** the touchpad is active,  
**when** the teacher performs a two-finger horizontal swipe,  
**then** the PC performs horizontal scrolling.

### AC-TRK-07
**Given** the touchpad is active,  
**when** the teacher taps/holds and moves,  
**then** the system supports drag behavior.

---

# 31.3 Presentation Acceptance Criteria

### AC-PRE-01
The Next Slide control is available directly on the main screen and sends the corresponding action to the authorized PC.

### AC-PRE-02
The Previous Slide control is available directly on the main screen and sends the corresponding action to the authorized PC.

### AC-PRE-03
Start Presentation is available directly on the main screen.

### AC-PRE-04
End Presentation is available directly on the main screen.

### AC-PRE-05
Black/Blank Screen is available directly on the main screen.

### AC-PRE-06
Volume Up, Volume Down, and Mute are available directly on the main screen.

### AC-PRE-07
Play/Pause is available directly on the main screen.

---

# 31.4 Laser Pointer Acceptance Criteria

### AC-LAS-01
**Given** an authorized session,  
**when** the teacher starts Laser Pointer mode,  
**then** the PC displays the software-rendered pointer.

### AC-LAS-02
**Given** Laser Pointer mode is active,  
**when** the teacher moves the pointer input,  
**then** the PC pointer follows the movement.

### AC-LAS-03
**Given** Laser Pointer mode is active,  
**when** the teacher stops pointer mode,  
**then** the pointer stops receiving movement input and the mode ends.

### AC-LAS-04
**Given** the session becomes unauthorized or disconnected,  
**then** pointer control stops.

---

# 31.5 File Sharing Acceptance Criteria

### AC-FILE-01
**Given** an authorized session,  
**when** the teacher selects a phone file and starts transfer,  
**then** the file is transferred to the configured PC location.

### AC-FILE-02
The mobile app displays transfer progress during an active transfer.

### AC-FILE-03
The mobile app reports successful completion after the PC confirms the transfer.

### AC-FILE-04
The teacher can cancel an active transfer.

### AC-FILE-05
A failed transfer produces a failure status rather than a false completion status.

### AC-FILE-06
PC-to-phone transfer is supported for an authorized session.

### AC-FILE-07
The system does not require file-transfer history.

---

# 31.6 Screen Hosting Acceptance Criteria

### AC-SCR-01
**Given** an authorized session,  
**when** the teacher starts Screen Hosting,  
**then** the complete mobile screen is streamed to the PC.

### AC-SCR-02
Screen Hosting includes the full mobile display rather than only selected files or applications.

### AC-SCR-03
Portrait orientation is reflected appropriately on the PC.

### AC-SCR-04
Landscape orientation is reflected appropriately on the PC.

### AC-SCR-05
The teacher can stop Screen Hosting from the mobile application.

### AC-SCR-06
The PC can display the mirrored screen fullscreen.

### AC-SCR-07
Where multiple displays are supported by the PC environment, the user can select the intended display where supported.

### AC-SCR-08
The user can configure available quality and FPS settings.

### AC-SCR-09
After network interruption, the system does not continue presenting stale content as live.

---

# 31.7 Custom Commands Acceptance Criteria

### AC-CMD-01
**Given** an approved command,  
**when** the teacher selects it,  
**then** the mobile app sends its command identifier/request.

### AC-CMD-02
**Given** a recognized and allowlisted command identifier,  
**when** the PC receives it from an authorized session,  
**then** the corresponding predefined local action is executed.

### AC-CMD-03
**Given** an unknown command identifier,  
**when** the PC receives it,  
**then** the receiver rejects it and performs no arbitrary action.

### AC-CMD-04
The product does not provide unrestricted shell or terminal execution through Custom Commands.

---

# 31.8 Pairing Acceptance Criteria

### AC-ROOM-01
**Given** a PC has not been configured,  
**when** first-time setup starts,  
**then** the PC requests a room number.

### AC-ROOM-02
**Given** a configured room number,  
**when** QR generation is requested,  
**then** the generated QR contains only the room number.

### AC-ROOM-03
**Given** the teacher scans the QR,  
**then** the mobile application stores the room number.

### AC-ROOM-04
Scanning the QR does not authorize the mobile device to control the PC.

### AC-ROOM-05
**Given** the teacher selects a discovered room,  
**then** the selected PC displays a temporary OTP.

### AC-ROOM-06
**Given** the correct unexpired OTP is entered,  
**then** the PC authorizes the mobile session.

### AC-ROOM-07
**Given** an incorrect or expired OTP,  
**then** the session remains unauthorized.

---

# 31.9 Security Acceptance Criteria

### AC-SEC-01
No control feature can be used through an unauthorized session.

### AC-SEC-02
An expired OTP cannot authorize a session.

### AC-SEC-03
An invalid OTP cannot authorize a session.

### AC-SEC-04
An unknown custom command cannot cause arbitrary PC execution.

### AC-SEC-05
File-transfer requests are rejected when the session is not authorized.

### AC-SEC-06
Screen Hosting requests are rejected when the session is not authorized.

### AC-SEC-07
After connection loss, stale control input is not executed indefinitely.

---

# 31.10 Reliability Acceptance Criteria

### AC-REL-01
When Wi-Fi is unavailable, the app clearly indicates that connection cannot be established.

### AC-REL-02
When the selected PC becomes unavailable, the app changes to a disconnected/error state.

### AC-REL-03
When the mobile disconnects, the PC stops accepting stale control input from that session.

### AC-REL-04
When an OTP expires, the user can start another authorization attempt.

### AC-REL-05
When a file transfer fails, the app reports failure.

### AC-REL-06
When Screen Hosting is interrupted, stale content is not presented as live.

### AC-REL-07
Sensor/calibration problems produce understandable user-facing feedback.

---

# 32. Product Constraints

The following are mandatory product constraints.

| ID | Constraint |
|---|---|
| CONSTRAINT-01 | No ESP32. |
| CONSTRAINT-02 | No Arduino. |
| CONSTRAINT-03 | No Bluetooth controller or intermediary hardware. |
| CONSTRAINT-04 | Mobile-to-PC communication is direct over Wi-Fi. |
| CONSTRAINT-05 | The mobile phone is the primary teacher controller. |
| CONSTRAINT-06 | The PC software performs the required OS-level actions. |
| CONSTRAINT-07 | The QR code contains only the room number. |
| CONSTRAINT-08 | The QR code is not an authentication credential. |
| CONSTRAINT-09 | OTP is required for session authorization. |
| CONSTRAINT-10 | The mobile home screen must remain minimal. |
| CONSTRAINT-11 | Presentation controls must remain directly accessible on the main screen. |
| CONSTRAINT-12 | File-sharing history is not required. |
| CONSTRAINT-13 | Screen Hosting means full real-time mobile screen mirroring. |
| CONSTRAINT-14 | Custom Commands use an allowlist. |
| CONSTRAINT-15 | Unrestricted remote shell/terminal access is not a default requirement. |
| CONSTRAINT-16 | Mobile and PC interfaces use glassmorphism. |
| CONSTRAINT-17 | The product should not depend on unnecessary cloud infrastructure. |
| CONSTRAINT-18 | The product should not require additional classroom hardware. |

---

# 33. Out of Scope

The following are outside the current product scope.

## 33.1 Unrestricted Remote Shell

The mobile application shall not provide unrestricted terminal or shell access to the classroom PC.

## 33.2 Unnecessary Hardware

The system shall not require:

- ESP32
- Arduino
- Bluetooth controller
- Dedicated physical remote
- Other intermediary control hardware

## 33.3 Unnecessary Cloud Infrastructure

The core classroom-control workflow does not require cloud infrastructure.

The primary communication path is direct mobile-to-PC Wi-Fi.

## 33.4 File-Sharing History

Persistent file-transfer history is not part of the current version.

## 33.5 Partial Screen Sharing as the Definition of Screen Hosting

Screen Hosting is not limited to:

- Individual files
- Images
- PDFs
- Selected applications

The required behavior is full real-time mobile-screen mirroring.

## 33.6 Unrelated Classroom Features

Features unrelated to the defined teaching-control workflow should not be added to the current product scope.

---

# 34. Data and State Requirements

The product needs to maintain only the information required for its defined workflows.

## Mobile App State

Potential required state includes:

- Stored room numbers
- Current selected room
- Current connection state
- Current authorization state
- Mouse configuration
- IMU configuration/calibration state
- Screen Hosting preferences
- File-sharing preferences
- Custom command definitions
- General application preferences

## PC State

Potential required state includes:

- Configured room number
- Receiver configuration
- Authorized session state
- Active OTP state
- Approved custom-command definitions
- File download location
- Relevant PC display configuration

The exact storage mechanism is intentionally not specified in this PRD.

---

# 35. State Transition Requirements

## Session State

```text
Not Connected
      │
      ▼
Discovering
      │
      ▼
Room Selected
      │
      ▼
Waiting for OTP
      │
      ▼
Authorizing
      │
      ├── Failure ──► Unauthorized
      │
      ▼
Authorized
      │
      ├── Network Loss ──► Reconnecting
      │
      ├── OTP/Session Expiry ──► Unauthorized
      │
      └── User Disconnect ──► Not Connected
```

The exact reconnection behavior may be refined during technical design, but no transition may accidentally preserve unauthorized control access.

---

# 36. Feature Priority

## Priority 1 — Core Classroom Control

- Room management
- OTP authorization
- Gyro Mouse
- Touchpad
- Presentation controls
- Connection status

## Priority 2 — Teaching Utilities

- Laser Pointer
- File Sharing
- Screen Hosting

## Priority 3 — Controlled Extensibility

- Custom Commands
- Advanced configuration

The priority describes product sequencing and does not remove any required feature from the product definition.

---

# 37. Requirements Traceability

| Product Goal | Related Requirements |
|---|---|
| Replace staying beside PC | GYR, TRK, PRE, LAS |
| Wireless phone control | ARCH-01, ARCH-02 |
| Multiple classroom PCs | ROOM, CON |
| Secure authorization | SEC |
| File transfer | FILE |
| Full mobile mirroring | SCR |
| Predefined PC commands | CMD |
| Simple teacher UI | MOB, SET, UX |
| No additional hardware | CONSTRAINT |
| Consistent product design | Visual Design |

---

# 38. Definition of Done

A feature shall not be considered complete merely because the underlying action works.

A feature is considered product-complete when:

1. The required user flow exists.
2. The feature is accessible from the correct UI location.
3. Authorization rules are enforced.
4. Required error states are handled.
5. Connection interruption behavior is handled.
6. The feature follows the product's visual design.
7. The feature does not introduce prohibited functionality.
8. The relevant acceptance criteria pass.

---

# 39. Release-Level Acceptance

The product's initial release is acceptable when all of the following are true:

- A classroom PC can be configured with a room number.
- A QR code containing only the room number can be generated.
- A teacher can scan and save the room number.
- The app can discover reachable classroom systems.
- The teacher can select a room.
- The selected PC can display a temporary OTP.
- The teacher can enter the OTP.
- The PC can authorize the mobile session.
- Gyro Mouse works according to configured axes and settings.
- Touchpad gestures perform the specified mouse actions.
- Presentation controls are directly accessible from the main screen.
- Laser Pointer can be started, controlled, and stopped.
- Phone-to-PC file sharing works with progress and completion reporting.
- PC-to-phone file sharing works.
- File transfers require authorization.
- Screen Hosting mirrors the complete mobile screen in real time.
- Screen Hosting supports portrait and landscape behavior.
- Screen Hosting can be started and stopped.
- Screen Hosting handles network interruption without presenting stale content as live.
- Custom Commands validate against an allowlist.
- Arbitrary shell/terminal execution is not exposed.
- Unauthorized sessions cannot control the PC.
- Connection loss prevents stale control input.
- Mobile and PC interfaces follow the glassmorphism design language.
- The mobile main screen remains focused on frequently used teaching functions.
- Advanced settings are grouped under the three-dot menu.
- User-facing errors are understandable to non-technical teachers.

---

# 40. Open / TBD Product Decisions

The following values intentionally remain undefined until technical prototyping, benchmarking, or security review.

| Area | TBD |
|---|---|
| Minimum supported mobile OS version | TBD |
| Supported PC operating systems | TBD |
| Exact OTP length | TBD |
| OTP expiration duration | TBD |
| OTP retry policy | TBD |
| Session lifetime | TBD |
| Reconnection strategy details | TBD |
| Target mouse latency | TBD |
| Target presentation-command latency | TBD |
| Screen Hosting target latency | TBD |
| Screen Hosting default FPS | TBD |
| Screen Hosting quality levels | TBD |
| Screen Hosting bandwidth target | TBD |
| Maximum file size | TBD |
| Maximum concurrent file transfers | TBD |
| Maximum concurrent mobile sessions | TBD |
| Exact sensor requirements | TBD |
| Exact multi-display behavior | TBD |
| Exact supported display-selection capabilities | TBD |
| Exact custom-command management workflow | TBD |
| Exact storage mechanism | TBD |

These values should be decided only after feasibility testing and should not be invented as implementation assumptions.

---

# 41. Future Expansion Guidelines

Future features may be considered if they support the core teaching-control purpose.

Potential areas include:

- Additional classroom controls
- Additional presentation actions
- Improved input customization
- Improved screen-hosting configuration
- Additional predefined classroom commands
- Improved device/session management
- Accessibility improvements
- Additional classroom display workflows

Future expansion must not weaken the core principles of:

- Simplicity
- Direct Wi-Fi communication
- Explicit authorization
- Controlled PC actions
- Minimal teaching UI
- No unnecessary hardware

---

# 42. Final Product Definition

Air Mouse for College Teaching is a **direct mobile-to-PC classroom control system**.

The mobile phone is the teacher's controller.

The PC is the system-side receiver.

The teacher identifies classroom systems using room numbers, authorizes a selected PC using a temporary OTP, and then uses the mobile application to control classroom functions.

The product's core capabilities are:

- Gyro Mouse
- Touchpad Mouse
- Presentation Controls
- Laser Pointer
- Two-way File Sharing
- Full real-time Screen Hosting
- Allowlisted Custom Commands
- Multi-PC room management
- Secure session authorization

The product must remain simple for classroom use while providing advanced configuration through a separate settings area.

Its visual identity is shared across mobile and PC and is based on glassmorphism.

The product does not require ESP32, Arduino, Bluetooth controllers, or other intermediary hardware, and it does not provide unrestricted remote shell access.

---

# 43. Product Success Criteria

The product succeeds when a teacher can walk into a classroom, select the correct room, authorize the classroom PC, and perform normal teaching operations from the phone without needing to remain beside the PC or understand the underlying technical system.

The defining experience is:

```text
Select Room
    ↓
Enter OTP
    ↓
Connected
    ↓
Control PC
    ↓
Teach Freely
```

**Product principle:**

> **One phone. One classroom system. Simple, secure control.**

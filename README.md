# Air Mouse for College Teaching

**Air Mouse for College Teaching** is a classroom control system that allows a teacher to use an Android/mobile phone as a wireless controller for a classroom PC.

The system provides mouse control, presentation controls, laser-pointer functionality, file sharing, real-time screen hosting, and predefined custom commands through a direct Wi-Fi connection.

There is **no ESP32, Arduino, Bluetooth controller, or other intermediary hardware**.

---

## Problem Being Solved

During classroom teaching, instructors often need to move between the PC, presentation controls, physical mouse, and other classroom resources.

This can interrupt teaching flow, especially when the teacher is away from the classroom PC.

Air Mouse provides a single mobile interface that allows the teacher to control the classroom PC wirelessly while moving freely around the classroom.

---

## Project Goal

The goal is to create a simple, reliable, and highly user-friendly classroom control system where:

- A mobile phone acts as the teacher's controller.
- A classroom PC acts as the system-side receiver.
- Communication happens directly over Wi-Fi.
- The teacher can control common classroom actions from the mobile device.
- Multiple classroom PCs can be identified using room numbers.
- Security is based on explicit authorization through a temporary OTP.
- Advanced functionality remains available without cluttering the main teaching interface.

---

# Architecture Overview

```text
┌─────────────────────────┐
│      Mobile App         │
│                         │
│ • Air Mouse             │
│ • Trackpad              │
│ • Presentation          │
│ • Laser Pointer         │
│ • File Sharing          │
│ • Screen Hosting        │
│ • Custom Commands       │
└────────────┬────────────┘
             │
             │ Wi-Fi
             │
┌────────────▼────────────┐
│   PC / System Receiver  │
│                         │
│ • Input Receiver        │
│ • Presentation Control  │
│ • File Transfer         │
│ • Screen Hosting        │
│ • Command Validation    │
│ • System Tray Service   │
└─────────────────────────┘
```

The mobile application and PC receiver communicate **directly over Wi-Fi**.

No external controller or intermediary hardware is required.

---

# Main Features

## Mouse Control

The mobile application provides two primary methods of controlling the PC mouse.

### Gyroscope / IMU Air Mouse

The phone's motion sensors can be used to control the PC cursor.

Supported controls include:

- Cursor movement
- Left click
- Right click
- Double click
- Scroll
- Drag and drop
- Sensitivity control
- Cursor acceleration
- Dead zone
- X/Y/Z axis inversion
- XY, XZ, or YZ axis selection
- IMU noise filtering
- Drift compensation
- IMU calibration

### Touchpad / Trackpad

The phone can also operate as a conventional wireless trackpad.

The trackpad supports common mouse operations such as:

- Cursor movement
- Left click
- Right click
- Double click
- Scroll
- Drag and drop

The teacher can switch between the air-mouse and trackpad modes according to the teaching situation.

---

# Teaching / Presentation Controls

Frequently used presentation controls are available directly from the mobile application's main screen.

Available controls include:

- Next slide
- Previous slide
- Start presentation
- End presentation
- Black / blank screen
- Volume up
- Volume down
- Mute
- Play / pause

These controls are intended to be immediately accessible without navigating through advanced settings.

---

# Laser Pointer

The mobile application provides a dedicated laser-pointer mode.

Features include:

- Start pointer mode
- Stop pointer mode
- Pointer movement

The laser-pointer functionality is intended to provide a convenient presentation aid while the teacher is away from the PC.

---

# File Sharing

Air Mouse supports direct file transfer between the mobile device and the classroom PC.

### Phone → PC

The teacher can select a file on the mobile device and transfer it to the connected PC.

### PC → Phone

The PC can also send files to the connected mobile device.

### Transfer Features

- Transfer progress
- Transfer completion notification
- Configurable PC download location

File-sharing history is **not a required feature**.

---

# Screen Hosting

**Screen Hosting** means full real-time mobile screen mirroring.

The complete mobile screen is streamed to the connected classroom PC over Wi-Fi.

```text
Mobile Screen
      │
      │ Real-time Wi-Fi stream
      ▼
Classroom PC
      │
      ▼
Fullscreen / Selected Display
```

## Requirements

Screen Hosting should support:

- Real-time screen mirroring
- Low-latency transmission
- Portrait orientation
- Landscape orientation
- Start / stop controls
- Fullscreen display on the PC
- Display selection where supported
- Quality configuration
- FPS configuration

The feature is intended for situations where the teacher wants to display content directly from the mobile device on the classroom display.

---

# Custom Commands

The teacher can create and use predefined commands from the mobile application.

Examples include:

- Open Calculator
- Open Browser
- Open Teaching Folder

Commands are defined as approved actions rather than arbitrary instructions.

The PC receiver validates every requested command against an **allowlist** before executing it.

### Security Boundary

Custom Commands must **not** provide unrestricted remote shell, terminal, or arbitrary command execution.

The receiver should only execute locally approved actions associated with configured commands.

---

# Mobile App Overview

The mobile application is designed around the needs of a teacher during an active classroom session.

The main screen should contain only frequently used teaching functions.

## Main Screen

The main screen should provide:

- Connected room/system
- Gyro Mouse
- Trackpad
- Laser Pointer
- Calibrate
- Presentation controls
- Share File
- Screen Hosting
- Custom Commands

The objective is to make common actions available with minimal navigation.

---

# Advanced Settings

Advanced configuration should be grouped under a three-dot (`⋮`) menu.

Possible settings categories include:

- Mouse settings
- IMU settings
- Presentation settings
- Screen-hosting settings
- System / room management
- File-sharing settings
- Custom-command management
- Security
- General app settings

This keeps the main teaching interface clean while still providing detailed configuration when required.

---

# PC Receiver Overview

The PC-side software acts primarily as a background receiver.

During normal teaching, it should run unobtrusively in the **system tray**.

Its responsibilities include:

- Receiving mobile control input
- Processing mouse commands
- Handling presentation controls
- Handling laser-pointer functionality
- Managing file transfers
- Receiving screen-hosting streams
- Validating custom commands
- Managing room identification
- Generating and displaying OTPs
- Managing authorized mobile connections

## Important PC UI Screens

The PC software should provide minimal interfaces for:

- First-time setup
- Room number configuration
- QR generation
- OTP display
- Connection status
- Configuration / settings
- Screen-mirroring display

The PC interface should not interfere with normal classroom teaching.

---

# Multi-PC / Classroom System Support

Each classroom PC is identified to the teacher using a **room number**.

The room number acts as the human-readable identity of the classroom system.

---

## First-Time Setup

The first-time setup follows this process:

```text
PC starts first-time setup
        │
        ▼
Enter Room Number
        │
        ▼
PC generates QR Code
        │
        ▼
Teacher scans QR Code
        │
        ▼
Mobile App stores Room Number
```

The QR code contains **only the room number**.

The QR code is used for room identification and registration.

It is **not an authentication mechanism**.

---

# Later Connection Workflow

When the teacher wants to connect to a classroom system:

```text
Open Mobile App
      │
      ▼
Discover Air Mouse systems over Wi-Fi
      │
      ▼
Show reachable/saved systems by Room Number
      │
      ▼
Teacher selects a Room
      │
      ▼
PC displays temporary OTP
      │
      ▼
Teacher enters OTP in Mobile App
      │
      ▼
PC verifies OTP
      │
      ▼
Mobile App becomes authorized
```

The mobile application should identify systems using **room numbers only** in the user-facing classroom-selection interface.

---

# Pairing and OTP Workflow

The QR code and OTP have different purposes.

| Mechanism | Purpose |
|---|---|
| Room Number | Identifies the classroom system |
| QR Code | Transfers the room number to the mobile app |
| Wi-Fi Discovery | Finds reachable Air Mouse systems |
| Temporary OTP | Authorizes the mobile device |
| Authorization | Allows the selected mobile app to control the PC |

The QR code must not be treated as an authentication credential.

The OTP provides the explicit authorization step before control is granted.

---

# Security Principles

Security should be designed around controlled classroom access rather than unrestricted remote access.

## Core Principles

- Room numbers are used for identification.
- QR codes contain only room numbers.
- QR codes are not authentication credentials.
- Control access requires temporary OTP verification.
- The PC must verify the OTP before authorizing the mobile device.
- Custom commands must be restricted to an allowlist.
- Arbitrary remote shell or terminal execution must not be provided.
- Unauthorized devices should not receive control privileges.
- Sensitive configuration should remain inside the appropriate settings interface.
- Authorization should be associated with the intended classroom system.

The system should follow the principle of **least privilege**: a connected mobile device should only receive the capabilities necessary for the enabled Air Mouse features.

---

# UI / UX Design Philosophy

Air Mouse should prioritize simplicity during active teaching.

The teacher should not need to navigate through complicated menus to perform common classroom actions.

## Mobile UI

The mobile application should use:

- Clear primary actions
- Large, easy-to-use controls
- Minimal navigation
- Consistent icons
- Immediate connection status
- Accessible presentation controls
- Advanced settings hidden under the `⋮` menu

The main interface should remain focused on teaching.

## PC UI

The PC software should remain mostly invisible during normal operation.

The receiver should normally operate from the system tray and only display UI when setup, authorization, configuration, or screen hosting requires it.

---

# Visual Design

Both the mobile application and PC software should follow the same **glassmorphism design language**.

## Design Characteristics

- Translucent glass panels
- Background blur
- Subtle borders
- Rounded corners
- Soft shadows
- Clear typography
- Consistent icons
- Smooth transitions
- Light theme
- Dark theme
- Good readability
- Strong contrast between controls and backgrounds

The mobile and PC interfaces should feel like two parts of the same product rather than separate applications.

---

# High-Level Project Structure

The project structure should remain technology-agnostic until implementation decisions are finalized.

```text
Air-Mouse-for-College-Teaching/
│
├── README.md
│
├── mobile/
│   └── <mobile application>
│
├── pc/
│   └── <PC receiver>
│
├── docs/
│   ├── architecture.md
│   ├── features.md
│   ├── communication.md
│   ├── pairing-and-security.md
│   ├── screen-hosting.md
│   ├── file-sharing.md
│   ├── custom-commands.md
│   └── ui-ux.md
│
└── ...
```

The exact technologies, frameworks, libraries, protocols, and implementation details are intentionally left open.

---

# Development Status

> **Status: Project Definition / Planning**

The functional requirements, system architecture, user workflows, and UI/UX principles are being defined.

Implementation technologies and detailed technical architecture will be selected during the development phase.

---

# Documentation

Detailed project documentation should be maintained as separate Markdown files inside the `docs/` directory.

Suggested documentation includes:

- [`architecture.md`](docs/architecture.md) — Overall system architecture
- [`features.md`](docs/features.md) — Detailed feature requirements
- [`communication.md`](docs/communication.md) — Mobile-to-PC communication design
- [`pairing-and-security.md`](docs/pairing-and-security.md) — Room identification, OTP, and authorization
- [`screen-hosting.md`](docs/screen-hosting.md) — Screen Hosting requirements and behavior
- [`file-sharing.md`](docs/file-sharing.md) — File-transfer requirements
- [`custom-commands.md`](docs/custom-commands.md) — Allowlisted custom-command system
- [`ui-ux.md`](docs/ui-ux.md) — Mobile and PC interface guidelines

These documents can be expanded as the project moves from requirements to implementation.

---

# Future Expansion

Potential future development areas include:

- Additional classroom-control functions
- Improved input customization
- Additional presentation controls
- More advanced screen-hosting configuration
- Additional predefined classroom commands
- Improved device/session management
- Accessibility improvements
- More customization for classroom environments
- Additional display and presentation workflows

Future features should preserve the project's core principles:

**simple for the teacher, direct over Wi-Fi, secure by authorization, and unobtrusive during teaching.**

---

## Project Principle

> **One phone. One classroom system. Simple control.**

# Air Mouse for College Teaching — Platform Integration

## 1. Purpose

Platform integration translates platform-independent semantic requests into approved PC operating-system/application behavior:

```text
Mobile Command → Semantic Command → PC Command Handler
→ Platform Abstraction → OS/Application Implementation
```

Mobile never needs to know local OS APIs, key sequences, mouse APIs, rendering APIs, or application-specific details. PC receiver validates session/request before invoking platform behavior.

## 2. Supported Platform Strategy

| Platform | Support | Notes |
|---|---|---|
| Platform A | TBD | Target desktop OS/environment not finalized. |
| Platform B | TBD | Target desktop OS/environment not finalized. |
| Platform C | TBD | Target desktop OS/environment not finalized. |
| Unsupported platforms | TBD | Must report unsupported capability rather than approximate unsafely. |

Target systems, minimum versions, and platform-specific support are **TBD** in `TECH_STACK.md`. Do not implement platform API behavior until target is selected.

## 3. Platform Abstraction

Core PC application communicates through conceptual adapters for:

- Mouse and keyboard input.
- Presentation/media/volume behavior.
- Window/application interaction.
- Laser rendering.
- System tray and notifications.
- Filesystem access.
- Screen hosting display/fullscreen.

```text
Validated Core Feature Logic → Platform Abstraction → OS Implementation
```

Core/session/network logic must not call OS APIs directly. Platform adapters must not contain session/network business logic.

## 4. Mouse Integration

| Semantic Action | Platform Action | Implementation Status |
|---|---|---|
| `MOUSE_CURSOR_DELTA` | Relative OS cursor movement | TBD |
| `MOUSE_LEFT_CLICK` | Primary button click | TBD |
| `MOUSE_RIGHT_CLICK` | Secondary button click | TBD |
| `MOUSE_DOUBLE_CLICK` | Double primary click | TBD |
| `MOUSE_BUTTON_DOWN` | Press approved mouse button | TBD |
| `MOUSE_BUTTON_UP` | Release approved mouse button | TBD |
| `MOUSE_DRAG_START/MOVE/END` | Controlled press/move/release lifecycle | TBD |
| `MOUSE_SCROLL_VERTICAL` | Vertical scrolling | TBD |
| `MOUSE_SCROLL_HORIZONTAL` | Horizontal scrolling | TBD |
| `MOUSE_PAUSE` | Stop receiving/applying movement | Core/session state; OS action may be none |

Input engine validates authorized session, command ordering, drag state, and parameters before adapter action. API choice is **TBD**.

## 5. Keyboard Integration

Keyboard behavior may support approved presentation/application actions. It originates only from approved semantic PC commands and validated PC mappings.

Do not expose arbitrary key sequences from mobile by default. Validate command/session/capability before any keyboard adapter call. Exact keyboard integration is **TBD**.

## 6. Presentation Integration

| Semantic command | PC responsibility | Status |
|---|---|---|
| `NEXT_SLIDE` | Advance supported active presentation one slide/step | TBD |
| `PREVIOUS_SLIDE` | Go back supported presentation one slide/step | TBD |
| `START_PRESENTATION` | Start/activate supported presentation state | TBD |
| `END_PRESENTATION` | Exit supported presentation state | TBD |
| `BLANK_SCREEN` | Activate/toggle supported blank presentation behavior | TBD |

PC maps semantic intent to local application/OS action. Presentation application detection, focus routing, supported-app list, and mapping strategy are **TBD**. Mobile remains application-independent.

## 7. Media and Volume Integration

| Command | Required behavior | Failure/unsupported behavior |
|---|---|---|
| `VOLUME_UP` | Request supported PC/system audio increase | Return unsupported/execution failure safely. |
| `VOLUME_DOWN` | Request supported PC/system audio decrease | Return unsupported/execution failure safely. |
| `MUTE` | Toggle/set mute where capability exists | Do not claim state unless known. |
| `PLAY_PAUSE` | Control supported current media context | Report unavailable if no supported target. |

Capability reporting informs mobile when implemented. OS/media API choice is **TBD**.

## 8. Laser Pointer Integration

PC Laser Pointer Renderer owns pointer creation, state, position updates, visibility, coordinate mapping, presentation compatibility, and cleanup. It starts only after validated `LASER_START`, updates using current authorized position, and hides on `LASER_STOP`, disconnect, session end, renderer failure, or receiver shutdown.

Coordinate mapping, fullscreen interaction, multi-display handling, and rendering technology are **TBD**. Stale updates must not re-show pointer after stop/session end.

## 9. Screen Hosting Display Integration

PC receiver screen-hosting integration owns authorized stream receiver handoff, decoder integration, display surface, windowed/fullscreen behavior, aspect ratio, portrait/landscape rotation, scaling, display selection where supported, and cleanup after stop/failure/session end.

Codec/decoder/display technology is **TBD**. Preserve aspect ratio; letterbox/pillarbox rather than distort. Unsupported display configurations return capability/error state. See `SCREEN_MIRRORING.md`.

## 10. File System Integration

PC filesystem adapter handles configured download location, safe file creation/naming, existing-file conflicts, temporary/incomplete files, cleanup, permission/storage failure, and PC→phone explicit source selection.

Security rules:

- Never trust remote absolute path.
- Validate filename.
- Prevent path traversal/path escape.
- Restrict writes to configured approved locations.
- Handle existing-file conflict under defined policy, **TBD**.
- Clean incomplete/cancelled transfer state safely.
- Never expose unrestricted remote filesystem access.

See `FILE_SHARING.md` and `SECURITY.md`.

## 11. System Tray Integration

Tray integration provides lightweight receiver access:

- Tray icon/status.
- Room number.
- Connected mobile/session status where appropriate.
- Open compact UI.
- Disconnect.
- Settings.
- Exit.

It remains unobtrusive during teaching. Exact tray technology is **TBD**.

## 12. Desktop Notifications

Show compact notifications for Air Mouse connected, authentication completed, file received, Screen Hosting started/stopped, disconnection, and important failure. Do not notify continuously for cursor/pointer updates. Native notification implementation is **TBD**.

## 13. Window/Application Integration

PC abstraction may interact with active supported presentation/media context, presentation window/display, and fullscreen state only for approved feature actions. It must not become arbitrary remote window management or arbitrary application execution. Focus/application strategy is **TBD**.

## 14. Permissions

| Capability | Permission Required | User Action | Failure Behavior |
|---|---|---|---|
| Mouse control | TBD by target platform | Grant required local permission if needed | Report unavailable; reject action safely. |
| Keyboard control | TBD by target platform | Grant required permission if needed | Report unsupported/denied. |
| Screen display/hosting | TBD by target platform | Grant access if required | Stream/display cannot start; clear UI state. |
| Filesystem access | TBD by target platform | Choose/configure permitted destination | Reject unsafe/unavailable transfer. |
| Notifications | TBD by target platform | Enable if optional/required | Continue feature; reduce notification capability. |
| Tray/background | TBD by target platform | Allow receiver background behavior where needed | Report receiver limitation. |

Do not invent permission names. Handle denial/revocation using `ERROR_HANDLING.md`.

## 15. Capability Detection

PC determines available support for mouse control, presentation/media/volume, Screen Hosting, file transfer, Laser Pointer, tray/notifications, and Custom Command approved actions. If unavailable, do not falsely report support; return supported/unsupported/error status and keep unrelated features functioning where possible.

Capability detection/negotiation mechanism is **TBD**.

## 16. Error Handling

Platform errors map to common error model:

| Platform condition | Error category |
|---|---|
| Permission denied/revoked | Permission error |
| Unsupported operation | Unsupported feature |
| Application unavailable | Presentation/media/command target unavailable |
| Display unavailable | Screen display failure |
| Filesystem failure | File destination/permission/storage error |
| OS adapter failure | Execution/platform error |
| Receiver lacks authorization | Unauthorized/invalid session |

Use safe user messages; do not show raw OS/API exceptions. See `ERROR_HANDLING.md`.

## 17. Security Boundary

```text
Authenticated Session → Command Validation → Authorization
→ Platform Adapter → OS
```

Platform layer is high-trust execution boundary. It must never become unrestricted remote executor. Prohibit arbitrary shell commands, arbitrary executable paths, arbitrary keyboard injection, and arbitrary filesystem writes unless a future specification explicitly changes the security model.

## 18. Responsiveness

Platform actions must not unnecessarily block network handling, command processing, PC UI, Screen Hosting, or file transfer. Mouse/pointer operations remain responsive. Exact concurrency mechanisms are implementation-dependent and follow `DESIGN.md`.

## 19. Platform Test Strategy

For each selected platform test:

| Area | Tests |
|---|---|
| Mouse | Movement, clicks, drag, vertical/horizontal scroll, pause, disconnect cleanup. |
| Presentation | Next/previous, start/end, blank screen, unavailable application. |
| Media | Volume, mute, play/pause, unsupported capability. |
| Filesystem | Safe save, existing files, invalid paths, permissions, storage, cleanup. |
| Screen Hosting | Display, fullscreen, rotation, scaling, stop/recovery. |
| Permissions | Missing, granted, revoked permission. |
| Tray/notifications | Status visibility, compact notifications, background behavior. |

See `TESTING.md`.

## 20. Platform Compatibility Matrix

| Feature | Platform A | Platform B | Platform C | Notes |
|---|---|---|---|---|
| Mouse/Trackpad OS control | TBD | TBD | TBD | Target platforms not finalized. |
| Presentation/media/volume | TBD | TBD | TBD | Capability varies by OS/application. |
| Laser rendering | TBD | TBD | TBD | Renderer approach TBD. |
| File transfer/filesystem | TBD | TBD | TBD | Safe destination behavior required. |
| Screen display/fullscreen | TBD | TBD | TBD | Decoder/display support TBD. |
| Tray/notifications | TBD | TBD | TBD | Native integration TBD. |

## 21. Implementation Decision Log

| Area | Decision | Alternatives | Status | Reason |
|---|---|---|---|---|
| Integration boundary | Platform adapters behind validated core | Direct OS calls from features | Confirmed | Preserves portability/security/testability. |
| Input execution | Semantic validated requests only | Raw remote key/shell input | Confirmed | Security requirement. |
| Supported PC platforms | TBD | TBD | TBD | Must be selected in `TECH_STACK.md`. |
| OS APIs/libraries | TBD | TBD | TBD | Select only after target platform choice. |
| Presentation mapping | TBD | TBD | TBD | Depends on supported application strategy. |
| Laser rendering | TBD | TBD | TBD | Depends on display/platform capabilities. |

## 22. Cross-Document Boundaries

- `COMMAND_CATALOG.md` defines semantic commands.
- `API_PROTOCOL.md` defines communication format.
- `SECURITY.md` defines security requirements.
- `PLATFORM_INTEGRATION.md` defines OS/application execution boundary.
- `MOUSE.md` defines mouse behavior.
- `PRESENTATION.md` defines presentation behavior.
- `FILE_SHARING.md` defines file transfer behavior.
- `SCREEN_MIRRORING.md` defines Screen Hosting behavior.
- `ERROR_HANDLING.md` defines common errors.

## 23. AI Coding Agent Rules

The AI must use these abstractions, inspect target OS before platform work, isolate platform-specific code, never silently add unsupported OS behavior, never bypass permissions, never implement arbitrary remote execution, test actual OS behavior, preserve other-platform functionality, and update this document when major platform decision changes.

## Platform Integration Summary

Air Mouse keeps mobile and core logic platform-independent by sending semantic teacher intent to a validated PC receiver. Only after session authentication, command validation, authorization, and capability checks does the PC platform adapter perform approved OS/application actions. This preserves safety, testability, portability, and classroom reliability while leaving OS-specific technology decisions explicitly **TBD**.

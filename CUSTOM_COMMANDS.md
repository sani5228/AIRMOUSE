# Air Mouse for College Teaching — Custom Commands Requirements

## 1. Purpose

Custom Commands let a teacher create and use additional predefined classroom actions from the mobile app while keeping execution on the classroom PC controlled and secure.

The mobile main screen contains **Custom Commands**. Teachers can use useful approved actions such as Open Calculator, Open Browser, Open Teaching Folder, opening a specific approved application, or another predefined local PC action. Exact available action types remain configurable and are **TBD**.

Custom Commands follow this model:

```text
Mobile app → semantic custom-command request → authenticated PC session → PC allowlist validation → approved local action → result/status
```

The mobile app must not send arbitrary shell commands, terminal commands, scripts, or unrestricted executable paths to the PC. The PC decides whether a command is allowed.

## 2. Scope and Principles

Custom Commands are semantic requests for PC-approved actions, not general remote administration. The feature must be practical for a college-project prototype and must not become unrestricted remote code execution.

Core principles:

- Main screen provides quick access to Custom Commands.
- Advanced settings/three-dot area provides management.
- Mobile requests command identity and permitted configured input, not arbitrary execution text.
- PC validates every request independently of mobile behavior.
- Only PC-side allowlisted commands can execute.
- Teachers receive simple success/failure feedback without technical stack traces.
- Platform-specific execution mapping remains PC-side and **TBD**.

## 3. Command Definition

A custom command conceptually contains the following fields. This is not a database schema; detailed model/persistence belongs in `DATA_MODEL.md`.

| Field | Purpose |
|---|---|
| Command ID | Stable unique semantic identifier used for mobile request and PC allowlist lookup. |
| Display name | Teacher-visible command name. |
| Description | Optional plain-language explanation of what the command does. |
| Enabled/disabled state | Determines whether command is available for execution. |
| Approved local action | PC-configured action that is allowed to run for this command. |
| Optional parameters | Restricted, validated inputs only where an approved command requires them. |
| Optional confirmation requirement | Indicates whether teacher must confirm before execution. |
| Execution result/status | Current request outcome: pending, succeeded, failed, rejected, timed out, or cancelled where applicable. |

Do not store arbitrary executable text, unrestricted shell strings, or unrestricted script content as normal command definition fields.

## 4. Command Management

Advanced command management is accessed through Custom Commands settings in the appropriate three-dot/settings area. The mobile main screen offers quick entry to execute available commands, not a full configuration dashboard.

Teachers or authorized PC-side users can:

| Action | Requirement |
|---|---|
| Add command | Create a named command mapped to an approved local action. Allowed creation workflow is **TBD**. |
| Edit command | Change display name, description, enabled state, approved configured action, permitted parameters, or confirmation requirement subject to PC validation. |
| Enable/disable | Make command available/unavailable without deleting its definition. |
| Delete | Remove a command through an explicit management action; confirmation is appropriate where accidental deletion matters. |
| Reorder | Reorder visible commands where useful for classroom workflow; exact behavior is **TBD**. |
| Execute | Select an enabled command from mobile quick-access view. |
| View result | Receive concise success, failure, rejection, or timeout feedback. |

The PC remains the authoritative owner of the execution allowlist. If mobile-side management exists, PC validates and applies only safe/allowed changes according to final design, **TBD**.

## 5. Main-Screen Experience

The mobile main screen contains **Custom Commands** as a direct teaching feature. Opening it presents enabled, teacher-visible command cards/buttons with recognizable names and optional icons.

Each visible command should show:

- Display name.
- Optional concise description where name alone is ambiguous.
- Enabled/disabled state.
- Optional confirmation cue.
- Pending/result feedback after selection.

Disabled commands are visibly unavailable and must not execute. Command management is not mixed into routine execution view unless a simple shortcut to settings is appropriate.

## 6. Command Execution Lifecycle

1. Teacher opens Custom Commands from the main screen.
2. Teacher selects an enabled command.
3. If configured, mobile asks for confirmation.
4. Mobile creates a semantic request containing command ID and only permitted validated parameters.
5. Mobile sends request through authenticated Air Mouse session.
6. PC validates session, request identity, command existence, enabled state, allowlist membership, and parameters.
7. PC executes the approved local action if validation succeeds.
8. PC returns success/failure/rejection status where available.
9. Mobile presents concise result feedback.

Normal classroom commands should not require a confirmation popup every time unless that command is explicitly configured to require confirmation.

## 7. Allowlist Validation

The PC-side allowlist is the mandatory security boundary between a mobile semantic request and local execution.

Before execution, PC validates:

- Active authorized session.
- Recognized command ID.
- Command exists in PC configuration.
- Command is enabled.
- Command is in allowlist.
- Requested parameters are permitted and valid.
- Requested action remains available under relevant PC conditions.

The mobile client is not trusted to decide whether an action is safe. Unknown IDs, disabled commands, unauthorized requests, invalid parameters, and unsupported actions are rejected.

## 8. Security Requirements

- Custom commands require an authorized Air Mouse session.
- PC validates every command request.
- Only PC-side allowlisted commands may execute.
- Mobile input must not become arbitrary shell or terminal input.
- Arbitrary executable paths are not allowed by default.
- Unrestricted scripts are not allowed by default.
- Parameters must be validated against each command’s permitted input rules.
- Path traversal must be prevented for path-related actions.
- Command injection must be prevented by semantic allowlisted action mapping and parameter validation.
- Unknown command IDs are rejected.
- Disabled commands are rejected.
- Session expiration/termination invalidates command authorization appropriately.

If advanced scripting is considered later, it is a **Future/TBD** capability requiring a separate security design, explicit threat assessment, restricted policy, and dedicated acceptance criteria. It is not part of this project’s default Custom Commands feature.

## 9. Approved Action Mapping

PC-side execution maps a semantic command ID to an approved local action. The mapping may be platform-specific because desktop environments/platforms can differ. Do not assume a specific operating system, shell, application, or launcher mechanism.

The final action-mapping mechanism is **TBD**. It must preserve these requirements:

- Mobile sends semantic command identity, not raw execution instructions.
- PC determines actual local behavior.
- Only configured/approved actions are executable.
- PC fails safely if action cannot be performed.
- Platform differences do not change mobile command meaning.

## 10. Conceptual Examples

These are examples only, not mandatory implementation choices or application integrations.

| Teacher-visible command | Example mobile request | Example approved PC action |
|---|---|---|
| Open Calculator | `OPEN_CALCULATOR` | Launch approved calculator action. |
| Open Browser | `OPEN_BROWSER` | Launch approved browser action. |
| Open Teaching Folder | `OPEN_TEACHING_FOLDER` | Open configured teaching folder. |
| Open approved application | Configured semantic ID | Launch configured approved application action. |

The PC must not treat these examples as permission to launch arbitrary supplied paths or execute arbitrary user-provided commands.

## 11. Parameters and Path Safety

Some approved commands may require optional parameters. Parameters are permitted only when explicitly defined for the command and must be validated by PC.

For actions involving folders/files:

- Do not accept arbitrary paths by default.
- Restrict path-related behavior to configured allowed locations where practical.
- Prevent traversal/path escape.
- Do not expose arbitrary filesystem access through mobile requests.
- Prefer PC-configured identifiers or fixed approved targets over mobile-provided paths.

Parameter formats, allowed value sets, validation rules, and configurable target policy are **TBD** and belong in future data/protocol/security design.

## 12. Feedback and Status

Use lightweight, teacher-readable feedback.

| Event | Example UX intent |
|---|---|
| Request sent | Brief pending/working state if noticeable. |
| Success | “Command executed: Open Calculator.” |
| Failure | “Could not open Calculator.” |
| Rejected | “Command is not allowed.” |
| Disabled | “Command is disabled.” |
| Unavailable | “Command target is unavailable.” |
| Connection loss | “Connection lost. Command was not completed.” |
| Timeout | “Command did not complete. Try again.” |

Do not show raw error codes, shell output, stack traces, filesystem internals, or security-sensitive details to the teacher.

## 13. Failure Handling

| Condition | Required behavior |
|---|---|
| Unknown command | PC rejects request; mobile shows command unavailable/not allowed. |
| Disabled command | PC rejects request; mobile shows disabled state. |
| Unauthorized session | PC rejects request; mobile requires valid connection/authentication. |
| Invalid parameters | PC rejects request; mobile shows invalid command input without technical details. |
| Application not installed | PC reports target unavailable; mobile shows concise failure. |
| Application failed to launch | PC reports execution failure; mobile shows retry/dismiss as appropriate. |
| Target folder unavailable | PC reports configured target unavailable; mobile shows concise failure. |
| Permission denied | PC reports action could not be performed; mobile does not expose system internals. |
| PC unavailable | Mobile stops/rejects request and shows PC unavailable. |
| Network disconnection | Do not queue/replay stale action by default; show connection lost/reconnecting. |
| Command timeout | Mark outcome as failed/unknown according to final policy; do not claim success. |
| Duplicate/stale command | PC avoids repeated unintended execution and reports/handles safely. |

No failure condition may crash either application or leave mobile falsely claiming execution succeeded.

## 14. Duplicate and Stale Requests

The system must minimize accidental repeated execution caused by UI double taps, network retries, delayed requests, reconnection, or duplicated delivery.

Conceptual safeguards may include request IDs, command/session association, timestamps where appropriate, and idempotency/deduplication rules. Exact implementation details are **TBD** and belong in `API_PROTOCOL.md`.

Requirements:

- A deliberate single command selection should result in at most its intended execution under ordinary conditions.
- Stale requests must not execute after session termination or reconnection without valid current authorization.
- Retries must not silently cause duplicate non-idempotent local actions.
- Mobile UI should disable/guard repeated taps briefly while a command is pending where appropriate, without making teaching controls feel slow.

## 15. Connection and Session Behavior

Custom Commands are available only during an authorized session. Before authentication, they are disabled or unavailable. On connection loss, mobile shows disconnected/reconnecting state and does not send commands as if connected.

After reconnection, commands become usable only when valid session policy confirms authorization. Session/reconnection rules are defined in `PAIRING.md`. Commands created while disconnected must not automatically execute later unless an explicit future policy defines safe behavior.

## 16. PC-Side UI and Notifications

PC receiver runs mainly in the system tray/background. It may show command execution status, recent relevant result, failure notification, and settings entry without requiring a large window during teaching.

Use compact notifications for significant execution result or failure. Do not require a visible PC window for normal use. Detailed management UX follows `UI_UX.md`.

## 17. Platform Considerations

PC may run on different desktop environments/platforms. Approved action mapping can be platform-specific, but the mobile semantic command remains consistent. Unsupported actions are rejected or marked unavailable rather than approximated through unrestricted shell execution.

Platform support matrix, local launcher integration, application detection, and configuration mechanics are **TBD**.

## 18. Testing Requirements

| Area | Required tests |
|---|---|
| Validity | Valid command executes approved action successfully. |
| Allowlist | Unknown command and disabled command are rejected. |
| Authorization | Unauthorized request is rejected. |
| Parameters | Invalid parameter is rejected safely. |
| Availability | Application unavailable, target folder unavailable, permission failure. |
| Reliability | Command timeout, duplicate request, delayed/stale request. |
| Network | Network interruption and reconnection during request. |
| Management | Command deletion, command enable/disable, editing where implemented. |
| Outcome | Successful command execution and correct mobile/PC feedback. |

Testing must confirm that requests cannot become shell/terminal/script injection or arbitrary executable-path access.

## 19. Relationship to Other Documents

- `ARCHITECTURE.md` defines mobile/PC responsibilities, authorization boundaries, and custom-command component placement.
- `PAIRING.md` defines session authentication and lifecycle.
- `SECURITY.md`, if present, defines broader security policy.
- `API_PROTOCOL.md`, if present, defines command request/result format and duplicate handling.
- `DATA_MODEL.md`, if present, defines persistent command data model.
- `UI_UX.md` defines detailed management and execution UI.
- `PRD.md` contains broader product requirements if present.

## 20. Acceptance Criteria

- [ ] The document defines Custom Commands as predefined semantic PC actions, not arbitrary remote execution.
- [ ] Teachers can add, edit, enable/disable, delete, and where useful reorder commands through advanced management.
- [ ] Mobile main screen provides quick access to Custom Commands.
- [ ] Mobile sends semantic command requests through authorized session.
- [ ] PC validates session, command ID, enabled state, allowlist membership, and parameters before execution.
- [ ] Only approved PC-side actions can execute.
- [ ] Arbitrary shell/terminal commands, unrestricted scripts, and arbitrary executable paths are prohibited by default.
- [ ] Unknown, disabled, unauthorized, malformed, and invalid-parameter requests are rejected safely.
- [ ] Path traversal and command injection risks are addressed through allowlisting and validation.
- [ ] PC returns success/failure/rejection status and mobile displays teacher-readable feedback.
- [ ] Duplicate/stale requests are prevented or safely handled without unintended repeated execution.
- [ ] Platform-specific execution mapping remains PC-side and is marked **TBD** where undecided.
- [ ] Testing covers valid, invalid, unauthorized, unavailable, duplicate, interrupted, management, and successful execution scenarios.

---
title: Mobile Coding Agent — Product Requirements v2
version: 2.0
status: draft-for-client-clarification
created: 2026-06-21
updated: 2026-06-21
supersedes: requirements-analysis.md and docs/PRD.md for product direction
---

# Mobile Coding Agent — Requirements v2

## 1. Purpose

This document redefines the product from a general-purpose, multi-model AI chat app into a mobile application with a Claude-like conversational interface that is used like Claude Code.

The product is a mobile control surface for an agentic coding runtime. A developer can connect a repository, describe engineering work in natural language, observe the agent reading and changing code, approve sensitive actions, review diffs and test results, and continue a coding session away from the desktop.

This is not a chatbot that only explains code. The defining requirement is that the agent can take verifiable action against a real development environment.

## 2. Product Vision

Enable developers to safely delegate, monitor, review, and steer software-development tasks from iOS and Android with the simplicity of a modern AI chat application.

The app should support workflows such as:

- “Investigate why checkout is failing.”
- “Implement issue #142 and run the relevant tests.”
- “Explain this repository before changing anything.”
- “Review the current diff and fix the failing tests.”
- “Commit the approved changes and open a pull request.”
- “Notify me when the task finishes or needs approval.”

## 3. Core Product Model

### 3.1 Two-part system

The product consists of:

1. **Mobile client** — conversation, session control, approvals, code/diff review, notifications, and account/device management.
2. **Agent runtime** — runs in an environment that has repository and tool access, executes the coding agent, streams events, and enforces permissions.

### 3.2 Assumed execution model for this draft

For v1, the agent runtime runs on a user-controlled macOS, Linux, or Windows/WSL computer through a lightweight companion service. The mobile app does not clone repositories or execute arbitrary shell commands on the phone.

The companion makes outbound encrypted connections only; users are not required to expose an inbound port. A cloud-hosted isolated workspace is a future option, not an MVP dependency.

### 3.3 Primary user

The primary user is an individual software developer who:

- owns or has authorized access to one or more Git repositories;
- already uses Git, a terminal, and an IDE;
- wants to start or continue coding-agent work away from the desk;
- needs visibility and control before consequential actions occur.

Team administration and enterprise governance are secondary, post-MVP concerns.

## 4. Goals and Success Measures

### 4.1 Product goals

- A developer can pair a trusted runtime and start the first coding session within 10 minutes.
- A developer can understand the current task state from a phone without reading raw terminal output.
- Every file change, command, and external side effect is visible and attributable to a session.
- Sensitive operations never proceed without the configured authorization.
- A session can survive temporary mobile disconnection without losing its transcript or task state.

### 4.2 Initial success measures

| ID | Measure | Draft target |
|---|---|---|
| SM-1 | Pairing completion rate | ≥ 80% of users who begin pairing |
| SM-2 | Time from app open to first submitted coding task | ≤ 10 minutes for a new user; ≤ 30 seconds for a returning user |
| SM-3 | Weekly users completing at least one agent task | ≥ 35% of weekly active users |
| SM-4 | Approval notification delivery | ≥ 99% within 10 seconds under normal network conditions |
| SM-5 | Session event delivery after runtime receipt | p95 ≤ 1 second |
| SM-6 | Crash-free mobile sessions | ≥ 99.5% |
| SM-C1 | Unintended or wrongly approved destructive actions | 0 known incidents |
| SM-C2 | Tasks marked successful while required tests failed | < 1% |

Targets are assumptions until the client confirms launch scale and commercial objectives.

## 5. Key User Journeys

### UJ-1 — Pair a development machine

Rafi installs the companion on his laptop, signs in, and runs a pairing command. The companion displays a short-lived QR code. Rafi scans it in the mobile app, reviews the machine identity and requested capabilities, and confirms trust. The app then lists repositories explicitly shared by that runtime.

### UJ-2 — Start a coding task from mobile

Rafi selects a repository and branch, chooses Plan mode, and asks the agent to fix an issue. The agent inspects the repository and returns a concise plan with affected areas and validation steps. Rafi approves execution. The agent edits files and runs tests while the app streams progress as structured activity, not an unreadable terminal dump.

### UJ-3 — Approve a sensitive action

The agent needs to install a dependency and later push a branch. Each request shows the exact command or action, why it is required, its risk level, working directory, and scope of any persistent permission. Rafi approves the dependency command once but denies direct push, asking the agent to prepare the branch only.

### UJ-4 — Review and finish work

The agent reports completion with a changed-file summary, diff, test results, unresolved risks, and token/cost usage. Rafi reviews the diff by file, requests one correction, then authorizes creation of a commit and pull request. The final session links to the commit and PR.

### UJ-5 — Continue an existing session away from the desk

Rafi begins a task in the desktop terminal, enables remote access, and later opens the same synchronized session on his phone. He can read the full transcript, answer questions, approve actions, and receive a completion notification. The actual tools continue running on the paired machine.

## 6. Functional Requirements

### 6.1 Accounts, authentication, and trusted devices

#### FR-1: User authentication

The user shall sign in with email/password and at least one supported social identity provider. Sessions shall use short-lived access credentials and revocable refresh credentials stored in platform-secure storage.

#### FR-2: Runtime pairing

The user shall pair a runtime by scanning a short-lived QR code or entering a one-time code. Pairing shall require confirmation on both the runtime and mobile device.

#### FR-3: Trusted-device management

The user shall see all paired runtimes and mobile devices, including name, OS, last-seen time, online status, and granted capabilities. The user shall be able to rename or revoke any device.

#### FR-4: Runtime health

The app shall show whether a runtime is online, busy, sleeping, incompatible, or disconnected and shall provide actionable recovery guidance.

### 6.2 Repositories and workspaces

#### FR-5: Repository discovery

The runtime shall expose only repositories or directories the user explicitly authorizes. The mobile app shall list repository name, path alias, current branch, dirty/clean state, and last activity without revealing unauthorized filesystem paths.

#### FR-6: Workspace selection

Before starting a session, the user shall select a runtime, repository, and working branch or isolated worktree.

#### FR-7: Workspace isolation

The user shall be warned when multiple active sessions share a working directory. Where supported, the user shall be able to create an isolated Git worktree for a new task.

#### FR-8: Repository context

The agent shall be able to read authorized project files and project instructions, inspect Git status/history, and search the codebase within the selected workspace.

### 6.3 Coding-agent sessions

#### FR-9: Create a session

The user shall create a session with a natural-language task, optional issue/PR URL, optional image or document, selected model, and permission mode.

#### FR-10: Session list

The app shall list active and historical sessions grouped by runtime and repository. Each item shall show title, branch, status, last activity, pending approval count, and completion outcome.

#### FR-11: Resume and steer

The user shall resume a session, send follow-up instructions, answer agent questions, interrupt the current turn, or cancel the task without losing completed work.

#### FR-12: Structured activity stream

The session shall present agent messages and structured tool events, including file reads, searches, edits, shell commands, tests, Git actions, external tool calls, and errors. Events shall be collapsible and display timestamps and status.

#### FR-13: Background execution

After the mobile app is backgrounded or disconnected, an authorized task may continue on the runtime. The mobile app shall recover missed events on reconnection.

#### FR-14: Session states

The system shall distinguish at least: queued, planning, running, waiting for user, waiting for approval, completed, failed, cancelled, runtime offline, and reconnecting.

#### FR-15: Session naming and search

The system shall auto-name sessions from the task. Users shall rename, archive, delete, filter, and search session transcripts.

### 6.4 Agent actions and tool use

#### FR-16: File operations

Within the authorized workspace, the agent shall read, create, edit, move, and delete files subject to the active permission policy.

#### FR-17: Shell execution

The agent shall execute non-interactive shell commands on the runtime, stream output, report exit status and duration, and support cancellation.

#### FR-18: Test and quality checks

The agent shall run repository-defined tests, linters, type checks, formatting, and build commands. Results shall be summarized with failures linked to the relevant output.

#### FR-19: Git operations

The agent shall inspect status and history, create or select branches/worktrees, stage changes, create commits, and—with explicit permission—push branches.

#### FR-20: Pull-request workflow

When provider integration is configured, the agent shall inspect issues and pull requests and prepare or create a pull request. The app shall display the resulting external link and any failed external operation.

#### FR-21: External tools

The runtime may expose configured MCP or equivalent tools. The app shall identify the tool and proposed action before approval and shall never silently broaden tool access.

### 6.5 Permission and safety controls

#### FR-22: Permission modes

Each session shall support at least:

- **Plan** — read-only investigation and plan creation;
- **Ask before changes** — explicit approval for edits and commands;
- **Accept edits** — workspace edits allowed, sensitive commands still require approval;
- **Custom** — rules determine allow, ask, or deny behavior.

A bypass-all mode, if ever supported, shall not be an MVP feature and shall require an isolated disposable environment.

#### FR-23: Approval request

An approval request shall show the proposed action, exact command or file scope where applicable, purpose, working directory, risk explanation, and choices to deny, approve once, or approve a narrowly scoped rule.

#### FR-24: Rule management

The user shall view and revoke session-level and repository-level allow/deny rules. Deny rules shall take precedence over allow rules.

#### FR-25: Dangerous-action safeguards

The runtime shall block known destructive actions outside the authorized workspace, credential extraction, unauthorized secret access, and privilege escalation unless a separately designed enterprise policy explicitly permits them.

#### FR-26: Checkpoints and rollback

The runtime shall create a checkpoint before agent file modifications. The user shall view checkpoints and restore file changes made after a selected checkpoint. The UI shall state that remote side effects, database mutations, deployments, and already-pushed Git operations cannot be undone by a file checkpoint.

#### FR-27: Audit trail

The system shall record session prompts, approvals/denials, permission changes, tool actions, file-change metadata, commands, outcomes, and actor/device identity in a tamper-evident audit log.

### 6.6 Code and diff review

#### FR-28: Changed-file summary

The app shall show added, modified, renamed, and deleted files with aggregate lines added/removed.

#### FR-29: Mobile diff viewer

The user shall review unified and side-by-side diffs with syntax highlighting, line wrapping, file navigation, and added/removed-line indicators optimized for a small screen.

#### FR-30: Review feedback

The user shall select a file or line range and ask the agent a question or request a correction using that selection as context.

#### FR-31: Completion report

Before a task is marked complete, the agent shall provide: work summary, files changed, tests/checks run and outcomes, commands with external side effects, unresolved risks, and recommended next action.

### 6.7 Notifications

#### FR-32: Actionable notifications

The app shall send push notifications when a session needs a decision, requests permission, completes, fails, or loses its runtime. Approval notifications shall open the exact request but shall require in-app authentication before a consequential action is approved.

#### FR-33: Notification preferences

Users shall control notification categories and quiet hours. Approval requests and security alerts cannot be silently converted into approvals.

### 6.8 Models and usage

#### FR-34: Model configuration

The product shall support at least one coding-capable model. If multiple models are offered, the user shall see model availability, capability, relative cost, and the model active for each session.

#### FR-35: Usage visibility

The session shall display token or credit consumption and any enforced limit. The user shall receive a clear warning before a task stops because of quota or billing state.

### 6.9 Settings and supportability

#### FR-36: Appearance and accessibility settings

The app shall support system, light, and dark themes, dynamic text sizing, screen readers, and reduced motion.

#### FR-37: Diagnostics

Users shall view app/runtime versions, connection diagnostics, and sanitized logs and shall export a support bundle that excludes source code, prompts, secrets, and command output by default.

#### FR-38: Data controls

Users shall export or delete stored account and session data and configure transcript retention, subject to explicit organizational retention policy where applicable.

## 7. Mobile Information Architecture

1. **Home** — active sessions, recent sessions, pending approvals, runtime status.
2. **Repositories** — paired runtimes, authorized repositories, branch/worktree selection.
3. **New Task** — repository, task prompt, context attachments, mode, model, start action.
4. **Session** — chat transcript, structured activity, composer, interrupt/cancel controls.
5. **Changes** — changed-file tree, diff viewer, checkpoints, review comments.
6. **Approvals** — current request, risk details, allow/deny options, rule scope.
7. **Result** — summary, validation results, usage, commit/PR actions and links.
8. **Settings** — account, trusted devices, permission rules, notifications, data, diagnostics.

The default session view should feel like Claude chat, but code actions, pending approvals, task state, and changed files must remain visible without forcing users to parse the conversation.

## 8. Non-Functional Requirements

### 8.1 Security

- **NFR-1:** All traffic shall use TLS 1.3 or the strongest platform-supported equivalent.
- **NFR-2:** Pairing and session credentials shall be short-lived, scoped, rotatable, and revocable.
- **NFR-3:** The runtime shall not open a public inbound port for standard operation.
- **NFR-4:** Source code and secrets shall not be persisted by the relay service unless explicitly required and disclosed.
- **NFR-5:** Secrets in command output and files shall be redacted from mobile events where reliably identifiable.
- **NFR-6:** Approval decisions shall be protected against replay and bound to the exact action presented.
- **NFR-7:** Consequential approvals shall require an unlocked authenticated app; optional biometric re-authentication shall be supported.
- **NFR-8:** The runtime shall enforce workspace boundaries independently of the mobile UI.

### 8.2 Reliability and recovery

- **NFR-9:** Session transcripts and structured events shall be resumable after a temporary network interruption.
- **NFR-10:** Commands shall have deterministic states so reconnecting cannot accidentally execute a command twice.
- **NFR-11:** A runtime disconnect shall never be reported as task completion.
- **NFR-12:** The mobile client shall cache enough metadata to display recent session status offline, clearly marked stale.

### 8.3 Performance

- **NFR-13:** Home shall display cached content in under 2 seconds on a representative mid-range device.
- **NFR-14:** New agent text and activity events shall render within 250 ms of receipt.
- **NFR-15:** Diffs up to 5,000 changed lines shall remain scrollable without visible interaction jank; larger diffs may be paginated.

### 8.4 Accessibility

- **NFR-16:** Interactive controls shall meet WCAG 2.2 AA contrast and labeling expectations.
- **NFR-17:** Minimum touch target shall be 44×44 points.
- **NFR-18:** Diff meaning shall not depend on red/green color alone.

### 8.5 Compatibility

- **NFR-19:** Mobile targets are iOS and Android; exact minimum OS versions remain an open decision.
- **NFR-20:** Runtime targets are macOS, Linux, and Windows through WSL for MVP, subject to technical validation.

## 9. MVP Scope

### 9.1 In scope

- iOS and Android mobile applications.
- One account with multiple trusted mobile devices and runtimes.
- Companion runtime on a user-controlled computer.
- Explicit repository authorization and one active workspace per session.
- New, resumable, and cancellable agent sessions.
- Plan, ask-before-changes, and accept-edits permission modes.
- File operations, shell commands, tests, and local Git operations.
- Structured activity stream, permission approvals, changed-file list, mobile diff review, and completion report.
- Push notifications for decisions, completion, failure, and disconnect.
- At least one coding-capable AI model.
- Session history, search, diagnostics, and basic data deletion.

### 9.2 Out of scope for MVP

- General consumer multi-model chat and entertainment personas.
- Running arbitrary development toolchains directly on the phone.
- Fully managed cloud development environments.
- Multiple cooperating agents or agent teams.
- Autonomous production deployments.
- Enterprise SSO, centralized policy administration, and compliance exports.
- Built-in code editor intended to replace a desktop IDE.
- Voice input/output, image generation, and social conversation sharing.
- Unlimited bypass-permission mode on a user’s primary computer.

## 10. Risks and Mitigations

| Risk | Impact | Required mitigation |
|---|---|---|
| Mobile approval of a harmful command | Data loss or compromise | Exact action binding, risk display, deny-first rules, re-authentication, audit log |
| Runtime has broad local access | Unauthorized source/secret exposure | Explicit workspace allowlist and runtime-enforced sandbox boundary |
| Phone or account compromise | Remote control of developer machine | Device revocation, short-lived credentials, biometric approval, anomaly alerts |
| Network interruption during command | Unknown or duplicated action | Command IDs, idempotent event protocol, authoritative runtime state |
| Concurrent sessions edit same files | Conflicts and corrupted work | Worktree isolation and prominent shared-workspace warning |
| Large logs/diffs overwhelm mobile UX | User cannot review safely | Structured summaries, progressive disclosure, pagination, raw output on demand |
| Model claims success without evidence | Low trust and broken code | Required validation results and explicit “not run” state in completion report |
| App-store positioning resembles Claude | Trademark/review risk | Independent brand, original visual identity, no implied Anthropic affiliation |

## 11. Exactly 10 Client Clarification Questions

These questions are phase-blocking for the final PRD. Until answered, the assumptions in this draft remain provisional.

1. Should v1 control an agent running on the user’s own computer, run agents in cloud-hosted workspaces, or support both from launch?
2. Is this product specifically a mobile client for Claude Code, or an independent Claude-Code-like product that may use Anthropic, OpenAI, or other models behind its own agent runtime?
3. Which launch platforms are required: iOS, Android, or both, and what minimum OS versions must be supported?
4. How should users connect code: local folders through the companion, GitHub/GitLab repositories, or both?
5. Which actions must the MVP perform: inspect/explain only, edit files, run commands/tests, create commits, push branches, open pull requests, and/or deploy?
6. What default permission policy does the client want: approve every edit/command, auto-approve file edits but ask for commands, or configurable repository-level rules?
7. Is the first release for individual developers only, or must it include teams, organization admins, shared sessions, SSO, and audit retention?
8. Who pays for model usage: the product’s subscription, the customer’s own API/provider account, or a hybrid model?
9. Must sessions keep running when the user’s computer sleeps or is offline? If yes, is a cloud runtime acceptable for those sessions?
10. What is the required MVP launch date, budget/team size, and commercial model (free, subscription, usage-based, or enterprise license)?

## 12. Research Basis

This draft uses current Claude Code concepts as category references, not as a commitment to clone Anthropic’s implementation:

- Claude Code runs agentic work against a development environment, including file edits and command execution: <https://code.claude.com/docs/en/how-claude-code-works>
- Remote Control uses mobile/web as a window into a session that continues to run on the user’s machine: <https://code.claude.com/docs/en/remote-control>
- Claude Code uses tiered permission rules and permission modes for tool use: <https://code.claude.com/docs/en/permissions>
- Sessions can be named, resumed, branched, and tied to a project directory: <https://code.claude.com/docs/en/sessions>

## 13. Version 2 Decision Record

| Date | Decision | Status | Source |
|---|---|---|---|
| 2026-06-21 | Replace general-purpose AI chat positioning with mobile agentic coding positioning | Confirmed | Client clarification |
| 2026-06-21 | Preserve a Claude-like conversational mobile experience | Confirmed | Client clarification |
| 2026-06-21 | Treat real code/tool actions as the core value, not multi-model chat | Confirmed | “used like Claude Code” |
| 2026-06-21 | Assume local companion runtime for MVP pending client answer | Provisional | Product/technical inference |
| 2026-06-21 | Preserve the previous requirement documents instead of overwriting them | Confirmed | Request to create requirement_v2 |

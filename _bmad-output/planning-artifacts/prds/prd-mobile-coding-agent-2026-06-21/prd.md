---
title: Poco Editor Mobile Coding Companion PRD
status: draft
created: 2026-06-21
updated: 2026-06-21
source: requirement_v2.md
---

# PRD: Poco Editor Mobile Coding Companion

## 1. Document Purpose

This PRD defines the mobile companion for Poco Editor. It combines a Claude-like conversational experience with the coding-agent capabilities already exposed by Poco Editor through client-provided APIs. It is the product contract for mobile UX, API integration, mobile engineering, QA, and delivery planning.

The app is not a general AI chatbot, a replacement editor, or a new coding-agent runtime. It lets a developer direct, monitor, review, and authorize work performed by Poco Editor. Poco owns the editor, execution environment, repository access, agent orchestration, model integration, and associated APIs. Our delivery responsibility is the iOS/Android application and its integration with those APIs. `[ASSUMPTION: Poco also owns production API hosting, availability, and server-side security controls.]`

This draft is based on `requirement_v2.md`. Unconfirmed client decisions are marked `[ASSUMPTION]` and consolidated in §13.

## 2. Product Vision

Poco Editor users should be able to move a software task forward safely when they are away from their workstation. From a phone, they can connect to their Poco account, open a Poco workspace or session, describe work, follow what the agent is doing, respond to questions, approve sensitive actions, inspect code changes and validation results, and finish with a reviewable branch or pull request.

The experience should feel as direct as a modern AI conversation while retaining the evidence, permissions, and control required when an AI can modify real code and invoke real tools.

## 3. Problem and Opportunity

### 3.1 Problem

Poco Editor's coding workflows are currently tied to the editor experience. Long-running work can pause when the developer leaves the computer or when the agent needs a decision. Generic mobile chat apps can discuss code but do not expose Poco workspaces, agent sessions, structured tool activity, permissions, diffs, or validation evidence.

Raw remote-terminal applications are also poorly suited to this job: terminal output is difficult to review on a small screen, approvals lack context, and changes must be reconstructed manually from logs and Git commands.

### 3.2 Opportunity

A purpose-built mobile client on top of Poco APIs can extend the existing product into a safe, reviewable mobile workflow:

- conversation for intent and steering;
- structured status instead of raw terminal noise;
- explicit authorization for consequential actions;
- mobile-first diff and test review;
- reliable notifications when human input is needed;
- session continuity across desktop and mobile.

## 4. Target User and Jobs to Be Done

### 4.1 Primary user

The MVP serves an existing or newly registered Poco Editor user who has access to one or more Poco workspaces and wants to code or continue coding-agent work from a phone. The user understands basic development concepts such as files, branches, diffs, and tests. `[ASSUMPTION: Poco's existing account, workspace, entitlement, and organization rules apply unchanged on mobile.]`

### 4.2 Jobs to be done

- When I am away from Poco Editor, let me start or continue a coding task through the same workspaces, sessions, and tools exposed by Poco APIs.
- When an agent acts on my code, show me what it is doing and why so I can intervene confidently.
- When an operation carries risk, let me approve or deny the exact action without granting broader access accidentally.
- When work is ready, let me review changes and validation evidence from my phone.
- When a task needs attention, notify me promptly and take me to the exact decision.
- When something goes wrong, let me stop the agent and restore reversible file changes.

### 4.3 Non-users for MVP

- Non-technical consumers seeking general AI chat.
- Teams requiring SSO, centralized policy management, or compliance reporting.
- Developers expecting a full mobile IDE or arbitrary toolchains running on-device.
- Operators seeking unattended production deployment automation.

## 5. Product Principles

1. **Poco is the system of record.** The mobile app reflects Poco API state and never invents execution outcomes locally.
2. **Control before autonomy.** The user must understand and govern consequential actions.
3. **Evidence before completion.** Success requires observable changes and validation outcomes returned by Poco APIs, not an unverified client assumption.
4. **Progressive disclosure.** Show concise task state first and detailed commands, logs, and diffs on demand.
5. **Least privilege.** Workspaces, tools, credentials, and permissions remain scoped by Poco's access model.
6. **API-contract discipline.** Every mobile capability must map to a documented, versioned Poco API or be identified as an API gap.
7. **Mobile-native review.** Critical decisions must remain understandable and operable on a small screen.

## 6. User Journeys

### UJ-1 — Rafi signs in and opens a Poco workspace

Rafi installs the Poco mobile app and signs in with his Poco account. The app retrieves his authorized organizations, workspaces, projects, and active sessions through Poco APIs. He opens the same project he uses in Poco Editor and sees its branch, current state, and recent agent work.

**Success outcome:** a user reaches an authorized Poco workspace without reconfiguring repository access in the mobile app.

### UJ-2 — Rafi starts a safe coding task

Rafi selects a Poco workspace, branch, and Plan mode. He asks the agent to fix an issue. Poco creates or resumes the agent session and streams a plan with expected files and validation. Rafi approves execution and changes to Accept Edits mode. The app displays concise live status with expandable file, search, command, and test events returned by Poco APIs.

**Success outcome:** Rafi can understand current progress and intervene without parsing a terminal transcript.

### UJ-3 — Rafi handles a sensitive action

The agent asks to install a dependency. The request shows the exact command, working directory, purpose, risk, and scope. Rafi approves it once. Later, the agent asks to push a branch; Rafi denies that action and instructs it to prepare a local commit instead.

**Success outcome:** only the exact authorized action runs, and both decisions appear in the audit trail.

### UJ-4 — Rafi reviews and completes the task

The agent reports changed files, test outcomes, unresolved risks, and usage. Rafi opens a mobile diff, comments on a line, and asks the agent to correct it. After the correction and passing checks, he approves commit and pull-request creation. The session ends with links to the resulting artifacts.

**Success outcome:** completion is supported by reviewable changes and validation evidence.

### UJ-5 — Rafi continues Poco Editor work from his phone

Rafi begins a session in Poco Editor and leaves while tests run. A push notification reports that the tests failed and the agent needs guidance. He opens the synchronized session in the mobile app, answers the question, and receives a second notification when Poco reports task completion.

**Success outcome:** temporary mobile disconnection or app backgrounding does not lose task state or events.

## 7. Functional Requirements

### 7.1 Poco identity, access, and workspace availability

#### FR-1 — Poco account authentication

The user can authenticate using the identity and session mechanism supplied by Poco. `[ASSUMPTION: Poco provides OAuth/OIDC or an equivalent mobile-safe authentication flow; raw long-lived API keys are not embedded in the app.]`

**Acceptance outcomes:**

- Authentication state survives normal app restarts and respects server-side revocation.
- The user can sign out from the mobile app and follow Poco's account-recovery flow.
- Sensitive credentials are stored using platform-protected storage.

#### FR-2 — Retrieve Poco account context

After authentication, the app retrieves the user's Poco profile, organizations or teams, entitlements, feature flags, and authorized workspaces through Poco APIs.

**Acceptance outcomes:**

- Unauthorized organizations and workspaces are never displayed.
- Account or entitlement changes are reflected after refresh or a server event.
- API authorization failures result in re-authentication or an access-specific message, not a generic app failure.

#### FR-3 — Register the mobile device

The app registers its installation and push token with Poco's API and supplies platform, app version, locale, and notification capability without collecting unrelated device data.

**Acceptance outcomes:**

- Sign-out unregisters or invalidates the device session and push token.
- Token rotation and notification-permission changes are synchronized with Poco.

#### FR-4 — Poco service and workspace status

The app displays workspace and session availability states supplied by Poco, including ready, busy, sleeping, unavailable, incompatible, disconnected, or reconnecting, and provides an actionable explanation when work cannot proceed.

### 7.2 Repositories and workspaces

#### FR-5 — List authorized Poco projects and workspaces

The app lists only projects, repositories, and workspaces returned as authorized by Poco APIs. Repository authorization and filesystem access remain owned by Poco Editor and are not configured independently by the mobile app.

#### FR-6 — Select task context

Before starting a task, the user selects an authorized Poco project or workspace and, where the API permits, a branch or isolated worktree.

**Acceptance outcomes:**

- The app displays branch and clean/dirty state before start.
- A dirty workspace requires acknowledgment when Poco reports that state.
- A workspace already used by another active session produces a conflict warning when supplied by the API.

#### FR-7 — Create isolated work

Where supported, the user can start a session in an isolated Git worktree. The resulting worktree is identifiable from the session and can be cleaned up after completion.

#### FR-8 — Supply repository context

The mobile app can request or display authorized project context, files, project instructions, Git status, and history only through Poco APIs. The agent's repository access remains a Poco Editor responsibility.

### 7.3 Sessions and conversation

#### FR-9 — Start a coding session

The user can ask Poco to create a coding session with a natural-language task, optional issue or pull-request reference, optional supporting attachment, selected workspace, model option, and permission mode supported by the API.

#### FR-10 — View and find sessions

The app lists active and historical Poco sessions by project or workspace with title, branch, current state, last activity, pending approvals, and completion outcome. Users can rename, archive, delete, filter, and search sessions where supported by Poco APIs.

#### FR-11 — Synchronize conversation

The conversation, questions, answers, and agent status remain synchronized between Poco Editor and mobile using Poco's session and event APIs.

#### FR-12 — Steer or stop work

The user can send follow-up instructions, answer agent questions, interrupt the current turn, or cancel the task. Cancellation does not erase completed changes or the audit record.

#### FR-13 — Present structured activity

The session distinguishes agent messages from structured file, search, edit, command, test, Git, external-tool, permission, and error events. Events are timestamped, stateful, and expandable.

#### FR-14 — Continue in background

Poco-hosted or Poco-managed work can continue while the app is backgrounded or temporarily disconnected. On reconnection, the client receives missed events and reconciles against Poco's authoritative session state.

#### FR-15 — Represent session state

The system distinguishes queued, planning, running, waiting for user, waiting for approval, completed, failed, cancelled, Poco service unavailable, and reconnecting states. A service disconnect cannot be represented as completion.

### 7.4 Agent actions

#### FR-16 — Modify workspace files

Through Poco APIs, the mobile user can instruct the Poco agent to create, read, edit, move, and delete files inside the selected workspace, subject to Poco authorization and permission rules.

#### FR-17 — Execute shell commands

Through Poco APIs, the mobile user can instruct the Poco agent to execute supported commands. The app displays the exact command, working directory, streamed or summarized output, duration, exit state, and cancellation state returned by Poco.

#### FR-18 — Run validation

The agent can run repository-defined tests, linters, type checks, formatters, and builds. The app summarizes pass, fail, skipped, cancelled, and not-run states and provides access to relevant output.

#### FR-19 — Perform Git actions

The agent can inspect Git state and history, create or select branches and worktrees, stage changes, and prepare commits. Commit and push behavior follows the active permission policy.

#### FR-20 — Work with issues and pull requests

When an external source-control provider is connected, the agent can inspect referenced issues and pull requests and prepare or create a pull request with explicit authorization for external writes.

#### FR-21 — Use configured external tools

The agent can invoke explicitly configured external tools. The app identifies the tool, action, target, and permission requirement and does not silently broaden access.

### 7.5 Permissions, safety, and recovery

#### FR-22 — Choose a permission mode

Each session supports:

- **Plan:** read-only investigation and plan creation;
- **Ask Before Changes:** edits and commands require explicit approval;
- **Accept Edits:** workspace edits are allowed while sensitive commands still require approval;
- **Custom:** allow, ask, and deny rules control scoped actions.

Unrestricted bypass mode is excluded from MVP.

#### FR-23 — Review an approval request

An approval request shows the exact proposed action, target or file scope, command and working directory where applicable, purpose, risk explanation, and whether it has external side effects.

The user can deny, approve once, or create a narrowly scoped rule. The approved action must match the presented action exactly.

#### FR-24 — Manage permission rules

The user can view and revoke session- and repository-scoped rules. Deny rules take precedence. The UI makes persistent scope visually distinct from one-time approval.

#### FR-25 — Enforce safety boundaries

Poco APIs and the Poco execution environment block access outside authorized workspaces, unauthorized secret retrieval, privilege escalation, and destructive behavior outside granted policy. The mobile app must not offer controls that bypass those server-side protections.

#### FR-26 — Restore file checkpoints

When Poco exposes checkpoints, the app displays them and lets the user request restoration through the API. The app warns that external side effects, database mutations, deployments, and pushed Git operations may not be reversed. `[API DEPENDENCY: checkpoint listing and restore endpoints.]`

#### FR-27 — Preserve an audit trail

The app displays the ordered audit data supplied by Poco for prompts, permission changes, approvals and denials, actor/device identity, tool actions, file-change metadata, commands, results, and outcomes. Poco owns authoritative audit persistence.

### 7.6 Review and completion

#### FR-28 — Summarize changed files

The app groups added, modified, renamed, and deleted files and reports aggregate additions and removals.

#### FR-29 — Review code diffs

The user can review mobile-optimized unified and side-by-side diffs with syntax highlighting, file navigation, line wrapping, and non-color-only change indicators.

#### FR-30 — Give contextual review feedback

The user can select a file or line range and ask a question or request a correction with that selection attached as context.

#### FR-31 — Produce a completion report

Before presenting a successful outcome, the session reports:

- work performed;
- files changed;
- validation commands and outcomes;
- tests or checks not run;
- commands with external side effects;
- unresolved risks or assumptions;
- model usage or credits consumed;
- recommended next action.

#### FR-32 — Create final development artifacts

With the required authorization, the user can ask the agent to create a commit, push a branch, or create a pull request. The result includes identifiers and links or a clear failure state.

### 7.7 Notifications and usage

#### FR-33 — Receive actionable notifications

The app sends push notifications when a Poco session needs input or permission, completes, fails, or becomes unavailable. Opening a notification navigates to the relevant session event.

Consequential approval requires an authenticated app session and cannot occur directly from an unauthenticated notification action.

#### FR-34 — Configure notifications

The user can control notification categories and quiet hours. Security and revocation alerts remain available independently of task-completion preferences.

#### FR-35 — Select and identify models

The app presents coding-capable models and capabilities returned by Poco's configuration API. Model providers, routing, entitlement, and billing are owned by Poco; the mobile app must not hardcode model availability.

#### FR-36 — Show consumption and limits

The app displays usage, quota, and billing state supplied by Poco and warns before work is expected to stop for lack of entitlement or credits.

### 7.8 Settings, accessibility, and data control

#### FR-37 — Configure appearance and accessibility

The user can select system, light, or dark appearance. The app supports dynamic text, screen readers, reduced motion, and minimum 44×44-point touch targets.

#### FR-38 — Access diagnostics

The user can view mobile version, Poco API environment/status, compatibility, and sanitized diagnostics and can export a support bundle that excludes source code, secrets, prompts, and command output by default.

#### FR-39 — Control product data

The user can export and delete account and session data and configure transcript retention, subject to disclosed organizational policy.

## 8. Mobile Experience Requirements

### 8.1 Primary navigation

1. **Home:** active and recent Poco sessions, pending approvals, service status.
2. **Projects:** authorized Poco projects, workspaces, repositories, branches, and worktrees.
3. **New Task:** task, context, workspace, mode, model, and start action.
4. **Session:** conversation, current state, activity stream, steering controls.
5. **Changes:** file tree, diffs, checkpoints, contextual feedback.
6. **Approvals:** exact action, risk, scope, deny/approve choices.
7. **Result:** completion evidence and commit/PR actions.
8. **Settings:** account, devices, rules, notifications, data, diagnostics.

### 8.2 Interaction requirements

- The default session view emphasizes conversation but keeps task state, approvals, and changed-file count persistently discoverable.
- Raw command output is secondary to structured summaries but remains available.
- A destructive or externally consequential action cannot use a generic unlabeled confirmation.
- Approval scope must be readable without horizontal scrolling.
- Large diffs use file-level navigation and progressive loading.
- Offline or stale information is visually distinct from live runtime state.

### 8.3 Poco API integration requirements

- Every mobile feature shall map to a documented Poco endpoint, event, or SDK capability before implementation begins.
- Poco shall provide API base URLs for each environment, authentication documentation, schemas, error codes, pagination rules, rate limits, versioning policy, and sample payloads.
- Long-running sessions require a documented real-time mechanism such as WebSocket, SSE, or resumable event polling.
- Events require stable identifiers, ordering or cursors, timestamps, session linkage, and terminal states.
- Mutation endpoints require idempotency support where duplicate mobile requests could cause repeated actions.
- Approval endpoints must bind a decision to the exact pending Poco action and reject stale approvals.
- The app shall handle documented API errors with specific recovery actions and preserve unknown error details in sanitized diagnostics.
- Unsupported or unavailable Poco capabilities shall be feature-gated rather than simulated in the client.

## 9. Cross-Cutting Non-Functional Requirements

### 9.1 Security and privacy

- **NFR-1:** All network traffic uses TLS 1.3 or the strongest supported platform equivalent.
- **NFR-2:** Poco authentication and session credentials are short-lived, scoped, revocable, and independently rotatable.
- **NFR-3:** The mobile app communicates only with documented Poco endpoints and approved platform services.
- **NFR-4:** Approval decisions are replay-resistant and cryptographically bound to the presented action.
- **NFR-5:** Poco enforces authorization and execution boundaries independently of the mobile client; the client also prevents unauthorized navigation and cached-data exposure.
- **NFR-6:** Sensitive approvals require an unlocked, authenticated app; biometric re-authentication is available.
- **NFR-7:** The mobile app does not persist source files, command output, or secrets beyond the minimum encrypted cache needed for the approved offline experience.
- **NFR-8:** Support and telemetry data exclude source code, prompts, secrets, and command output by default.

### 9.2 Reliability

- **NFR-9:** Poco session events can be recovered after temporary disconnection without duplicate mobile mutations.
- **NFR-10:** Every command and approval has a durable identifier and deterministic terminal state.
- **NFR-11:** Poco APIs are authoritative for execution state; client state is reconciled after reconnect.
- **NFR-12:** Recent session metadata remains readable offline and is marked with its last refresh time.

### 9.3 Performance

- **NFR-13:** Home displays cached content within 2 seconds on a representative mid-range device.
- **NFR-14:** New text and activity events render within 250 ms after client receipt.
- **NFR-15:** Poco-event receipt to mobile rendering is p95 ≤ 1 second, excluding latency inside Poco before the event reaches the mobile connection.
- **NFR-16:** Diffs up to 5,000 changed lines remain responsive; larger diffs may paginate.
- **NFR-17:** Approval push notifications arrive within 10 seconds for at least 99% of accepted push-service deliveries.

### 9.4 Accessibility and compatibility

- **NFR-18:** User-facing mobile surfaces meet WCAG 2.2 AA contrast and labeling expectations.
- **NFR-19:** Diff meaning does not depend on color alone.
- **NFR-20:** Crash-free mobile sessions are ≥ 99.5% during the launch measurement window.
- **NFR-21:** MVP targets iOS and Android. Exact minimum OS versions are unresolved.
- **NFR-22:** The app supports the workspace and execution environments exposed by Poco APIs without assuming a local operating system.

## 10. MVP Scope

### 10.1 Included

- iOS and Android apps.
- Individual accounts and trusted-device management.
- Poco account authentication and entitlement integration.
- Poco API integration for projects, workspaces, sessions, activity, permissions, diffs, and results.
- Branch and optional worktree selection when supported by Poco.
- New, synchronized, resumable, interruptible, and cancellable sessions.
- Plan, Ask Before Changes, Accept Edits, and scoped Custom permissions.
- Mobile control of file operations, commands, validation, and Git actions exposed by Poco APIs.
- Structured activity, approvals, audit trail, changed-file summary, diff review, checkpoints, and completion reports.
- Push notifications for input, permission, completion, failure, and Poco-session unavailability.
- At least one coding-capable model and usage visibility.
- Session history, diagnostics, export, and deletion.

### 10.2 Excluded

- General-purpose consumer AI chat.
- On-device development toolchains or a full mobile IDE.
- Building or replacing Poco Editor, its agent runtime, repository engine, model gateway, or backend APIs.
- Direct mobile access to developer machines or repositories outside Poco APIs.
- Agent teams and multi-agent orchestration.
- Autonomous production deployment.
- Enterprise SSO, admin policy console, or compliance export.
- Voice interaction, image generation, or social conversation sharing.
- Unrestricted permission bypass on a primary development machine.

## 11. Success Metrics

| ID | Metric | Draft target |
|---|---|---|
| SM-1 | Authenticated users successfully loading a Poco workspace | ≥ 95% |
| SM-2 | Median new-user time to first submitted coding task | ≤ 10 minutes |
| SM-3 | Returning-user time from app open to submitted task | ≤ 30 seconds |
| SM-4 | Weekly active users completing at least one agent task | ≥ 35% |
| SM-5 | Poco-event receipt to mobile rendering | p95 ≤ 1 second |
| SM-6 | Crash-free mobile sessions | ≥ 99.5% |
| SM-7 | Approval notifications delivered within 10 seconds | ≥ 99% of accepted deliveries |

**Counter-metrics**

- **SM-C1:** Known unintended or incorrectly scoped destructive actions: 0.
- **SM-C2:** Tasks represented as successful when required checks failed: < 1%.
- **SM-C3:** Median approvals per completed task. Excessive approvals indicate unusable permission design, while near-zero approvals may indicate unsafe defaults.
- **SM-C4:** Percentage of users granting broad persistent rules within their first week. Growth requires security review rather than optimization.

All targets remain `[ASSUMPTION]` until launch scale, audience, and commercial strategy are confirmed.

## 12. Risks and Product Guardrails

| Risk | Product guardrail |
|---|---|
| Harmful command approved on a small screen | Exact-action display, risk context, narrow scope, authenticated approval, audit record |
| Mobile displays unauthorized Poco data | Server authorization, cache isolation, access tests, and logout data purge |
| Compromised phone controls a developer machine | Short-lived credentials, device revocation, biometric approval, security alerts |
| Network interruption produces ambiguous execution | Poco action IDs, authoritative API state, idempotent mutations, ordered recovery |
| Concurrent agents overwrite work | Worktree option and shared-workspace warning |
| Agent claims completion without proof | Required validation evidence and explicit not-run/failed states |
| Large diff or log hides important information | Structured summaries, progressive disclosure, pagination, raw evidence on demand |
| Product implies affiliation with Anthropic | Independent name and visual identity; no Claude trademark in market-facing brand |

## 13. Open Questions and Assumptions

The client has confirmed that Poco Editor owns the editor and will provide APIs; our team owns the mobile application. The PRD remains draft until the following integration and delivery questions are resolved:

1. Which API specifications, SDKs, sandbox credentials, and Postman/OpenAPI collections will Poco provide, and when?
2. What authentication flow should mobile use, including SSO, MFA, token refresh, logout, and account recovery?
3. Which launch platforms and minimum iOS/Android versions are required?
4. Which Poco entities are exposed: organizations, projects, workspaces, repositories, branches, worktrees, and sessions?
5. Which actions are available in the MVP APIs: edit, terminal commands, tests, commits, pushes, pull requests, deployment, checkpoints, and rollback?
6. What real-time protocol and event replay contract will Poco provide for streaming agent activity?
7. How are permissions and approvals represented, expired, denied, and audited by Poco?
8. Which existing Poco branding, design system, analytics, subscription, and entitlement rules must mobile reuse?
9. What API environments, rate limits, uptime targets, support contacts, and change/versioning policy will Poco guarantee?
10. What launch date, delivery team/budget, app-store ownership, and acceptance process constrain the project?

### Assumptions currently applied

- Existing Poco users are the MVP audience.
- Both iOS and Android are launch targets.
- Poco provides and operates all backend, editor, workspace, agent, model, and repository APIs.
- Our team builds the mobile clients and API integration, not the execution runtime.
- At least session chat, structured progress, approvals, diff review, notifications, and result review are expected in scope.
- Model availability, usage, entitlements, and external-action policy come from Poco APIs.

## 14. Release Readiness Gates

The MVP is ready for external launch only when:

- all §13 phase-blocking decisions have owners and recorded outcomes;
- authentication, logout/data purge, Poco authorization boundaries, and approval binding pass security review;
- interruption/reconnect tests demonstrate no duplicate actions;
- destructive-action and compromised-device threat scenarios are tested;
- diff review and permission flows pass mobile usability testing;
- accessibility validation covers screen readers, dynamic text, contrast, and non-color diff semantics;
- privacy terms clearly describe mobile cache, Poco API, transcript, telemetry, and model-provider data flows;
- each in-scope feature has a confirmed Poco API contract and test environment;
- completion reports distinguish passed, failed, skipped, cancelled, and not-run validation.

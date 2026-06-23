# Product Requirements Document
## Puku Mobile App

**Version:** 2.0
**Date:** June 2026
**Status:** Draft
**Source:** Figma — https://scoop-white-12890271.figma.site/

---

## Table of Contents

1. [Product Overview](#1-product-overview)
2. [Product Vision](#2-product-vision)
3. [Target Users](#3-target-users)
4. [User Journeys](#4-user-journeys)
5. [Navigation Architecture](#5-navigation-architecture)
6. [Feature Specifications](#6-feature-specifications)
   - 6.1 Authentication
   - 6.2 Chat
   - 6.3 Incognito Chat
   - 6.4 Navigation Drawer
   - 6.5 Chat History (Chats)
   - 6.6 Projects
   - 6.7 Project Details
   - 6.8 Project Knowledge
   - 6.9 Add Content
   - 6.10 Custom Instructions
   - 6.11 Code Sessions
   - 6.12 New Session
   - 6.13 Create Project
   - 6.14 Settings
7. [Backend API Contracts](#7-backend-api-contracts)
8. [Non-Functional Requirements](#8-non-functional-requirements)
9. [MVP Scope](#9-mvp-scope)
10. [Open Questions](#10-open-questions)

---

## 1. Product Overview

### 1.1 What We Are Building

A native iOS and Android mobile companion for **Puku Editor** — the AI code editor that understands your entire codebase. The app lets developers interact with Puku's AI from their phone: asking questions, managing projects with knowledge context, and running live code sessions directly from a mobile device.

The client — Puku — owns and operates the editor, the AI model (`puku-ai`), all backend APIs, agent execution, and workspace infrastructure. Our team owns the mobile apps and their integration with Puku's APIs.

### 1.2 Problem Statement

Puku Editor lives at the workstation. Developers lose context and momentum when they step away: long-running agent tasks pause, code questions go unasked, and project knowledge stays locked to the desktop.

### 1.3 Solution

A mobile app that extends Puku to the phone with three complementary experiences:

| Experience | What it does |
|---|---|
| **Chat** | Conversational AI assistance, powered by `puku-ai`, with project context and memory |
| **Projects** | Organize work with knowledge bases and custom instructions that shape every chat |
| **Code** | Spin up live coding sessions with real environments, GitHub integration, and agent execution |

### 1.4 Platforms

| Platform | Target |
|---|---|
| iOS | TBD (see §10) |
| Android | TBD (see §10) |
| Web | Out of scope for v1 |

---

## 2. Product Vision

A developer with Puku Editor on their workstation should be able to continue thinking, building, and steering their AI agent from their phone — and move a coding task forward safely even when away from a desk. From a phone they can open a chat, load project context, ask coding questions, start a code session, and review results — all through the same AI model that powers Puku Editor.

---

## 3. Target Users

### Primary — The Active Puku Developer

An existing or newly registered Puku Editor user who wants to continue development work from their phone. They understand basic development concepts (files, branches, environments, tests) and expect their mobile experience to feel like a natural extension of Puku Editor.

### Secondary — The Exploring New User

A developer who first encounters Puku through the mobile app. They sign in, start a conversation, and discover the coding-agent capabilities progressively.

### Non-users for MVP

- Non-technical consumers seeking a general AI chatbot
- Teams requiring SSO or admin policy consoles
- Developers expecting a full IDE running on-device

---

## 4. User Journeys

### UJ-1 — Sign in and start a conversation

A new user opens the app, sees the Puku Editor value proposition, signs in with Google or email, and within seconds starts typing in the chat input. Puku responds conversationally with awareness of the developer's coding context.

**Success:** User sends their first message within 90 seconds of first open.

### UJ-2 — Organize work in a project

A developer creates a project called "Dev App", uploads a specification document to its Knowledge base, adds custom instructions telling Puku to behave as a senior iOS engineer, and starts a new chat scoped to that project. Every message in this project chat has access to the knowledge and instructions.

**Success:** A project chat uses the uploaded knowledge and instructions in its responses.

### UJ-3 — Run a live code session

A developer opens the Code tab, taps "New Session", selects the `puku-ai-2.7` model and a Node.js 20 environment, connects their GitHub account, describes "Build a REST API", and watches Puku execute the task. They can toggle "Accept edits automatically" to control how changes are applied.

**Success:** Code session completes and produces observable, reviewable artifacts.

### UJ-4 — Continue a conversation privately

A developer needs to explore a sensitive topic without it being saved to history. They open an Incognito chat, ask their question, and when done, close the incognito session — no history, no memory update.

**Success:** Incognito session leaves no trace in chat history or Puku memory.

### UJ-5 — Review past work

A developer opens the Chats screen, searches for an old conversation, finds it, and continues from where they left off.

**Success:** Full conversation history is searchable and continuable.

---

## 5. Navigation Architecture

### 5.1 Unauthenticated

```
App
└── Auth Screen
    ├── Continue with Google
    └── Email input → [onboarding / sign up / sign in]
```

### 5.2 Authenticated — Navigation Drawer

The primary navigation is a **left-side drawer** accessed by the hamburger icon on any main screen.

```
Navigation Drawer
├── [User avatar + actions]
│
├── Chats          ← chat bubble icon
├── Projects       ← folder icon
├── Artifacts      ← cube icon
└── Code           ← code brackets icon
│
├── RECENTS
│   └── [Recent chat items]
│
└── [New chat button]   ← black button with + icon
```

### 5.3 Main Screens

| Screen | Entry Point |
|---|---|
| Chat (home) | Default after login; New chat from drawer |
| Incognito Chat | Lock icon on Chat header |
| Chats | Drawer → Chats |
| Projects | Drawer → Projects |
| Project Details | Projects list → tap project |
| Project Knowledge | Project Details → Project knowledge card |
| Custom Instructions | Project Details → Custom instructions card |
| Code | Drawer → Code |
| New Session | Code screen → New Session button |
| Settings | Drawer → user avatar area |

---

## 6. Feature Specifications

### 6.1 Authentication

**Screen:** Auth (dark theme, purple gradient sphere background)

#### Layout
- **Logo:** Purple "P" Puku logo top-left
- **Headline:** "The AI Code Editor That Understands Your Entire **Codebase**" (Codebase in orange accent)
- **Subheadline:** "Puku understands your entire codebase, predicts what needs to change next, and guides you through it so you can build faster without losing context."
- **Primary CTA:** White button — "Continue with Google" (Google "G" icon)
- **Divider:** "OR"
- **Secondary input:** Dark email input field — "Enter your email"
- **Legal:** "By continuing, you agree to Puku's Consumer Terms and Usage Policy, and acknowledge their Privacy Policy." (linked text)

#### Acceptance Criteria

- [ ] Google OAuth sign-in completes and navigates to Chat screen.
- [ ] Email input accepts a valid email and proceeds to Puku's sign-up/sign-in flow.
- [ ] Auth tokens are stored in platform-protected secure storage (Keychain / EncryptedSharedPreferences).
- [ ] Auth state survives normal app restarts.
- [ ] Sign-out revokes the session and clears stored tokens.

---

### 6.2 Chat Screen

**Screen:** Chat (light beige/cream background — main home screen after auth)

#### Layout
- **Top bar:** Hamburger icon (left) | Lock icon (right, opens incognito)
- **Center greeting:** Puku "P" logo + "Good afternoon, [username]"
- **Input bar (bottom):**
  - Plus icon — attachment picker
  - Text field — "Chat with Puku..."
  - Model badge — "puku-ai-2.7"
  - Voice/microphone icon
  - Send button — black circle with arrow

#### Behavior
- **Empty state:** Greeting + centered logo until first message is sent.
- **Active chat:** Message list appears above input bar; assistant responses stream in.
- **Model badge** is tappable and shows model selection options.
- **Voice input:** Tap microphone to record; transcription populates input field.
- **Attachment (+):** Opens attachment picker (image from library/camera, file upload).
- **Lock icon** in header navigates to Incognito chat.

#### Acceptance Criteria

- [ ] Chat streams AI responses in real time.
- [ ] Model badge displays the currently active model.
- [ ] Voice transcription populates the input field (user can edit before sending).
- [ ] Attachment button opens the content picker.
- [ ] Send button is disabled while a response is streaming.

---

### 6.3 Incognito Chat

**Screen:** Incognito (light background, ghost icon)

#### Layout
- **Header:** Hamburger icon (left) | "Incognito chat" title (center) | Close X (right)
- **Center state:**
  - Ghost icon
  - "Incognito chats can't access memory. They aren't saved to history or added to memory."
  - "Note: Chat history is still visible to your admin. Learn more about how your data is used with Puku." (linked)
- **Input bar:** Identical to Chat screen ("Chat with Puku...", model badge, voice, send)

#### Behavior
- Incognito chats do not appear in Chat History.
- Incognito chats do not update Puku memory.
- Closing (X) returns user to regular Chat screen.
- All other chat behavior (streaming, model selection, attachments) is identical to Chat.

#### Acceptance Criteria

- [ ] Incognito session does not appear in the Chats list after closing.
- [ ] Puku memory is not updated from incognito messages.
- [ ] Admin-level visibility disclaimer is shown at all times.
- [ ] Close button exits incognito and returns to Chat.

---

### 6.4 Navigation Drawer

**Screen:** Nav Drawer (left-side overlay)

#### Layout
- **Title:** "Puku" (top)
- **Lock icons** (top right — incognito shortcut)
- **Primary navigation links:**
  1. Chats — chat bubble icon
  2. Projects — folder/layers icon
  3. Artifacts — cube/box icon
  4. Code — code brackets `</>` icon
- **Section: "RECENTS"**
  - Scrollable list of recent chat items (title only)
- **Bottom row:**
  - User avatar circle (purple "P")
  - "New chat" button — black, "+" icon + "New chat" text
  - Three-dot menu icon

#### Acceptance Criteria

- [ ] Drawer opens from hamburger icon on any main screen.
- [ ] Tapping a nav item closes the drawer and navigates to the screen.
- [ ] Recents list shows the most recently active chats.
- [ ] Tapping a recent item opens that chat.
- [ ] "New chat" creates a new blank chat and opens the Chat screen.
- [ ] Drawer can be closed by swiping left or tapping outside it.

---

### 6.5 Chats Screen

**Screen:** Chats (full list view)

#### Layout
- **Header:** Back arrow (left) | "Chats" title | Archive icon | Delete icon (right)
- **Search bar:** Search icon + "Search Chats" placeholder
- **Chat list items:**
  - Colored chat icon (orange for recent, gray for older)
  - Chat title
  - Date (e.g., "May 11, 2026")

#### Behavior
- Search filters the displayed list in real time.
- Archive icon archives selected chats.
- Delete icon deletes selected chats.
- Tapping a chat item opens it in the Chat screen.

#### Acceptance Criteria

- [ ] All saved chats are listed, newest first.
- [ ] Search filters by chat title and message content.
- [ ] Archive action moves chat to an archived state (not visible in main list).
- [ ] Delete action removes the chat permanently (with confirmation).
- [ ] Tapping a chat item resumes that conversation.

---

### 6.6 Projects Screen

**Screen:** Projects (list view)

#### Layout
- **Header:** Hamburger icon (left) | "Projects" title | Filter/settings icon (right)
- **Search bar:** Search icon + "Search projects" placeholder
- **Project list items:**
  - Project name (e.g., "Dev App")
  - Subtitle: last edited time (e.g., "Edited Just now")
- **Bottom bar:** "New project" button — black, "+" icon

#### Acceptance Criteria

- [ ] All user projects are listed.
- [ ] Search filters by project name.
- [ ] Tapping a project navigates to Project Details.
- [ ] "New project" button opens the Create Project modal.

---

### 6.7 Project Details Screen

**Screen:** Proj Details

#### Layout
- **Header:** Back arrow (left) | Project name (e.g., "Dev App") | Three-dot menu (right)
- **Project meta:** Subtitle | "Created by Puku" with Puku icon | "Private" with lock icon
- **Two action cards:**
  1. **Project knowledge** card — "Add knowledge" link (orange)
  2. **Custom instructions** card — "Add instructions" link (orange)
- **Empty state area:**
  - Chat bubble + cloud icon
  - "Chats you've had with Puku will show up here."
- **Bottom bar:** "New chat" button — black, "+" icon

#### Behavior
- "Add knowledge" link navigates to the Project Knowledge screen.
- "Add instructions" link navigates to the Custom Instructions screen.
- "New chat" opens a new chat scoped to this project.
- Three-dot menu exposes: Edit project, Delete project.
- Once chats exist, the empty state is replaced by the chat list.

#### Acceptance Criteria

- [ ] Project name, description, creator, and visibility are displayed correctly.
- [ ] "Add knowledge" navigates to Project Knowledge screen.
- [ ] "Add instructions" navigates to Custom Instructions screen.
- [ ] "New chat" starts a new chat with this project's knowledge and instructions applied.
- [ ] Associated project chats appear in the list below.

---

### 6.8 Project Knowledge Screen

**Screen:** Knowledge

#### Layout
- **Header:** Back arrow (left) | "Project Knowledge" title | Three-dot menu (right)
- **Capacity indicator:** "0% of project capacity used" (progress bar)
- **Empty state:**
  - Folder with plus icon
  - "Add relevant documents, text, code, or other files here so Puku can use them as context for all your chats within [Project Name]."
- **Bottom bar:** "Add Content" button — black, document icon + text

#### Behavior
- Capacity bar shows percentage of project knowledge storage used.
- "Add Content" opens the Add Content bottom sheet.
- Once files are uploaded, the empty state is replaced by a file list.

#### Acceptance Criteria

- [ ] Capacity indicator reflects actual storage usage from Puku API.
- [ ] "Add Content" opens the Add Content bottom sheet.
- [ ] Uploaded files appear in the list with name, type, and size.
- [ ] Files in this knowledge base are sent as context with every chat in this project.

---

### 6.9 Add Content Bottom Sheet

**Screen:** Add Content (bottom sheet modal)

#### Layout
- **Header:** Close X (left) | "Add Content to Project" title | "Upload files or create new text content" subtitle
- **Options (icon + label):**
  1. Upload from device — upload arrow icon
  2. Take picture — camera icon
  3. Choose image — image/photo icon
  4. Create new document — pen/edit icon

#### Behavior
- "Upload from device" opens the device file picker (PDF, DOCX, TXT, CSV, XLSX, code files).
- "Take picture" opens the device camera.
- "Choose image" opens the device photo library.
- "Create new document" opens an in-app text editor for pasting/typing content.
- Successful upload dismisses the bottom sheet and refreshes the Knowledge file list.

#### Acceptance Criteria

- [ ] All four options work on iOS and Android.
- [ ] Unsupported file types show an error before upload begins.
- [ ] File size limit is enforced with a user-facing message.
- [ ] Successful upload is reflected immediately in the Knowledge file list.

---

### 6.10 Custom Instructions Screen

**Screen:** Instructions

#### Layout
- **Header:** "Cancel" button (left, gray) | "Custom Instructions" title (center) | "Save" button (right, orange/red)
- **Description:** "Tell Puku how to behave in this project — tone, expertise level, format preferences, and anything else relevant."
- **Large multiline text area:** Placeholder — "e.g. You are a senior iOS engineer. Respond concisely with code examples. Prefer Swift over Objective-C..."
- **Bottom bar:** "Save Instructions" button — disabled (gray) until content is entered

#### Behavior
- Free-form text instructions that are prepended to the system prompt for all chats in this project.
- "Save" button activates once text is entered.
- "Cancel" discards changes and navigates back.
- Saving updates the project's instructions and the "Add instructions" card shows a preview.

#### Acceptance Criteria

- [ ] Instructions are saved to the project and persist across sessions.
- [ ] Saved instructions are applied to every new chat in this project.
- [ ] Cancel discards unsaved changes (with a confirmation if changes exist).
- [ ] Character count or limit is shown when approaching the maximum.

---

### 6.11 Code Screen

**Screen:** Code (empty state)

#### Layout
- **Header:** Back arrow (left) | "Code" title
- **Empty state:**
  - Terminal window with `</>` icon
  - "No sessions found" heading
  - "Start a new code session to get help with coding tasks, run code, and iterate with Puku."
- **Bottom bar:** "New Session" button — black, "+" icon

#### Behavior
- Once code sessions exist, they appear as a list in place of the empty state.
- "New Session" opens the New Session modal.
- Tapping an existing session reopens it.

#### Acceptance Criteria

- [ ] Empty state is shown when no sessions exist.
- [ ] Existing sessions are listed with their name, environment, and last-active time.
- [ ] "New Session" opens the New Session modal.
- [ ] Tapping a session navigates into the active session view.

---

### 6.12 New Session Modal

**Screen:** New Session (modal overlay on Code screen)

#### Layout
- **Header:** Close X (left) | "New Session" title
- **MODEL section:** Dropdown — "puku-ai-2.7" with Puku icon + chevron
- **ENVIRONMENT section:** Dropdown — "Node.js 20" with Node.js icon + chevron
- **SUGGESTED section:** Chip buttons:
  - "Build a REST API"
  - "Create a React component"
  - "Write unit tests"
  - "Debug my code"
- **GitHub section:** "Connect to GitHub" button with GitHub icon + chevron
- **Settings:**
  - Label: "Accept edits automatically"
  - Description: "Puku will apply code changes without confirmation"
  - Toggle switch: OFF by default
- **Input bar (bottom):**
  - Plus icon
  - "Describe what you want to build..." placeholder
  - Model badge "puku-ai-2.7"
  - Voice icon
  - Send button (gray/disabled until text is entered)

#### Behavior
- **Model dropdown:** Shows available Puku models.
- **Environment dropdown:** Shows supported runtimes (Node.js 20, Python, etc.). `[API DEPENDENCY]`
- **Suggested chips:** Tapping a chip populates the description input field.
- **GitHub connection:** Opens OAuth flow to connect GitHub account. Once connected, shows connected repo/account.
- **"Accept edits automatically" toggle:** When ON, Puku applies code changes without asking; when OFF, each change requires user confirmation.
- **Send button:** Activates when description field has content; tapping starts the session.
- **Close X:** Dismisses modal, returns to Code screen.

#### Acceptance Criteria

- [ ] Model selector shows Puku-API-sourced model list.
- [ ] Environment selector shows Puku-API-sourced runtime list.
- [ ] Suggested chips populate the description field.
- [ ] GitHub connection persists across sessions once authorized.
- [ ] "Accept edits automatically" toggle state is saved per session.
- [ ] Tapping Send with a description starts a code session and navigates into it.
- [ ] Send button is disabled when description field is empty.

---

### 6.13 Create Project Modal

**Screen:** Create Project (modal overlay on Projects screen)

#### Layout
- **Header:** Close X (left) | "Create a project" title
- **Field 1 — Project Name:**
  - Label: "What are you working on?"
  - Input: "Name your project" placeholder
- **Field 2 — Description:**
  - Label: "What are you trying to achieve?"
  - Textarea: "Describe your project, goals, subject, etc..." placeholder
- **Field 3 — Visibility:**
  - Label: "Visibility"
  - Dropdown button: Shows selected option with icon (e.g., "Private" + lock icon, highlighted in orange/red when selected)
  - Options:
    - **[Organization name]** — "Everyone in your organization can view and use this project" (building/org icon)
    - **Private** — "Only invited members can view and use this project" (lock icon)
- **Bottom bar:** "Create project" button — disabled (gray) until name is entered

#### Behavior
- "Create project" activates once a project name is entered.
- Visibility defaults to "Private".
- Tapping "Create project" creates the project via Puku API and navigates to Project Details.

#### Acceptance Criteria

- [ ] Project name is required; "Create project" button is disabled without it.
- [ ] Visibility options reflect the user's organization membership from Puku API.
- [ ] Successfully created project appears immediately in the Projects list.
- [ ] Close X dismisses without saving.

---

### 6.14 Settings Screen

**Screen:** Settings

#### Layout
- **Header:** Hamburger icon (left) | "Settings" title | Info icon (right)
- **User profile section:**
  - Email: e.g., "puku@puku.net"
  - Username: "@puku"
  - Badge: Black "Team" pill with dropdown arrow
- **Settings list (icon, label, optional subtitle, chevron):**
  1. Profile
  2. Billing
  3. Usage
  4. Capabilities — subtitle: "2 enabled"
  5. Permissions
  6. Font style — subtitle: "Default"
  7. Voice
  8. Haptic feedback — toggle switch (ON/OFF)
  9. Notifications
  10. Shared links
- **Bottom action:** "Log out" — red text

#### Screen Descriptions

| Setting | Description |
|---|---|
| Profile | Manage display name, avatar, account details |
| Billing | Subscription plan, invoices, payment method |
| Usage | Model usage metrics, quota, and credit consumption |
| Capabilities | Feature flags enabled for this account (e.g., experimental features) |
| Permissions | Control what Puku can access (files, repo, external services) |
| Font style | Select font size/style for chat rendering (default: Default) |
| Voice | Configure voice input/output preferences |
| Haptic feedback | Toggle physical vibration feedback on actions |
| Notifications | Control notification categories and delivery preferences |
| Shared links | Manage publicly shared conversation or code links |
| Log out | Sign out and clear local session data |

#### Acceptance Criteria

- [ ] User email, username, and team badge are fetched from Puku API.
- [ ] Tapping any settings row navigates to the corresponding detail screen.
- [ ] Haptic feedback toggle takes effect immediately.
- [ ] "Log out" clears all session tokens and returns user to Auth screen.

---

## 7. Backend API Contracts

All mobile features depend on APIs provided and hosted by Puku. The following table maps each mobile feature to the required API capability. Exact endpoint paths, schemas, and authentication flows must be confirmed with Puku before implementation begins.

| Feature | Required Puku API capability |
|---|---|
| Google / email sign-in | OAuth / OIDC or equivalent mobile-safe auth flow |
| Token refresh + revocation | Short-lived access tokens with refresh + logout invalidation |
| User profile + team | `/users/me` — profile, email, username, team/org membership |
| Chat — send message | POST message with SSE streaming response |
| Chat — conversation history | List and paginate past conversations |
| Chat — model selection | Config endpoint returning available models |
| Incognito chat | Send message without persistence (server-side no-save mode) |
| Projects — CRUD | Create, read, update, delete project entities |
| Project knowledge — upload | Upload document/image to project knowledge base |
| Project knowledge — capacity | Capacity and usage metadata per project |
| Custom instructions | Store and retrieve per-project system prompt text |
| Code sessions — list | List active and historical sessions |
| Code sessions — create | Create a session with model, environment, and description |
| Code sessions — stream | Real-time event stream for session progress |
| Environments | Config endpoint returning available runtime environments |
| GitHub connection | OAuth flow to connect and identify a GitHub account |
| Accept edits flag | Per-session permission mode (auto-accept vs. manual confirmation) |
| Usage + quota | `/users/me/usage` — model usage, quota, credits |
| Capabilities | Feature flags / capabilities enabled for the account |
| Notifications | Push token registration and notification preference management |
| Shared links | CRUD for publicly shared chat or code session links |

### 7.1 Real-time Requirements

- Chat message streaming must use SSE (Server-Sent Events) or WebSocket.
- Code session events (agent progress, file edits, command output) must use SSE or WebSocket with event replay / cursor support to recover from disconnects.
- Puku must provide stable event identifiers and ordering guarantees.

### 7.2 Integration Contract Requirements

- All endpoints must be documented with OpenAPI or equivalent before mobile sprint begins.
- Puku provides: base URL per environment, auth documentation, error codes, pagination, rate limits, and versioning policy.
- Unsupported API capabilities must be feature-gated in the app — not simulated client-side.

---

## 8. Non-Functional Requirements

### 8.1 Performance

| Requirement | Target |
|---|---|
| App cold start → first screen visible | < 3 seconds (mid-range Android) |
| Chat screen loads (cached state) | < 2 seconds |
| First streaming token after send | < 1.5 seconds (p95) |
| Event render after receipt | < 250 ms |
| Projects / Chats list loads | < 1 second |

### 8.2 Security

| Control | Requirement |
|---|---|
| All network traffic | TLS 1.3 minimum |
| Auth tokens | Stored in `flutter_secure_storage` (Keychain / EncryptedSharedPreferences) |
| Puku credentials | Short-lived, scoped, revocable; never hardcoded in app bundle |
| Sensitive approvals (code sessions) | Require authenticated app session; biometric re-auth available |
| Source code / secrets in logs | Excluded from telemetry and support bundles by default |

### 8.3 Reliability

| Requirement | Target |
|---|---|
| Crash-free sessions | ≥ 99.5% |
| Chat and session state after reconnect | Reconciled from Puku API; no duplicate sends |
| Offline mode | Recent chats and project metadata readable; marked with last-sync time |

### 8.4 Accessibility

- WCAG 2.2 AA contrast and labeling on all user-facing screens.
- Dynamic text (system font size) support.
- Full screen-reader compatibility (VoiceOver / TalkBack).
- Minimum 44 × 44 pt touch targets.
- Non-color-only indicators (e.g., diff changes not conveyed by color alone).

### 8.5 Compatibility

| Platform | Note |
|---|---|
| iOS | Minimum version: TBD (see §10) |
| Android | Minimum version: TBD (see §10) |
| Flutter | Pinned via FVM; all commands run as `fvm flutter` / `fvm dart` |

---

## 9. MVP Scope

### 9.1 In Scope

- iOS and Android apps.
- Authentication: Google OAuth + email sign-in.
- Chat: streaming AI conversation, model selection, voice input, attachments.
- Incognito chat: private, history-free conversations.
- Navigation drawer: Chats, Projects, Artifacts (placeholder), Code.
- Chat history: list, search, archive, delete.
- Projects: create, list, view details.
- Project knowledge: upload documents/images/text.
- Custom instructions: per-project system prompt.
- Code sessions: create with model + environment selection, GitHub connection, accept-edits toggle.
- Settings: profile, billing, usage, capabilities, permissions, appearance, notifications, log out.
- Push notifications for code session events.

### 9.2 Out of Scope for MVP

| Feature | Notes |
|---|---|
| Artifacts section | Navigation slot present; content deferred to v2 |
| Full code diff review | Deferred to v2 |
| Multi-agent / team orchestration | Deferred |
| On-device code execution | Puku cloud environments only |
| Web app | Mobile-only for v1 |
| iPad / tablet layout | Responsive but not optimized |
| RTL language support | Deferred post-launch |
| Admin / SSO / enterprise policy | Not in MVP |

---

## 10. Open Questions

| # | Question | Owner | Required by |
|---|---|---|---|
| OQ-1 | Minimum iOS and Android versions | Engineering + Puku | Sprint 1 |
| OQ-2 | Puku authentication flow: OAuth/OIDC spec, token TTLs, refresh, MFA | Puku | Sprint 1 |
| OQ-3 | Available environments for Code sessions (beyond Node.js 20) | Puku | Sprint 2 |
| OQ-4 | Knowledge base capacity limits and supported file types/sizes | Puku | Sprint 2 |
| OQ-5 | Real-time protocol: SSE vs. WebSocket for chat and code session events | Puku | Sprint 1 |
| OQ-6 | Exact model list returned by Puku config API; is `puku-ai-2.7` the only model at launch? | Puku | Sprint 1 |
| OQ-7 | Organization/team API: how is the "Team" badge populated? What org entities are available? | Puku | Sprint 1 |
| OQ-8 | Billing and usage screens: does Puku provide a web URL to embed, or do we build native UI? | Puku | Sprint 3 |
| OQ-9 | Artifacts: what is an artifact in Puku's data model? What should this screen show? | Puku + Product | Sprint 2 |
| OQ-10 | GitHub OAuth: Puku-owned flow or mobile-owned? Does connection persist in Puku account? | Puku | Sprint 2 |
| OQ-11 | Shared links: what content can be shared and how are public links managed? | Puku | Sprint 3 |
| OQ-12 | App name in stores — is it "Puku" or "Puku Editor"? Trademark status? | Puku / Legal | Pre-submission |
| OQ-13 | Analytics platform: does Puku require a specific SDK (Mixpanel, Amplitude, etc.)? | Puku | Sprint 1 |
| OQ-14 | Notification types: which session events trigger push notifications from Puku backend? | Puku | Sprint 2 |

---

*Document version: 2.0 — June 2026 — based on Figma: https://scoop-white-12890271.figma.site/*

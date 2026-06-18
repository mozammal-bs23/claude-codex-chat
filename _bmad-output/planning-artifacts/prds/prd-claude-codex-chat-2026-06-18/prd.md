---
title: claude-codex-chat — AI Chat Mobile App
status: draft
created: 2026-06-18
updated: 2026-06-18
---

# PRD: claude-codex-chat — AI Chat Mobile App

## 0. Document Purpose

This PRD is the authoritative source of requirements for the claude-codex-chat mobile application — a cross-platform AI chat app for iOS and Android, modeled on the experience of Claude and ChatGPT. It is written for the product owner, downstream UX, architecture, and engineering workflows. Features are grouped with FRs nested; cross-cutting NFRs are in their own section; assumptions are tagged inline and indexed in §10. This PRD builds on the requirements analysis produced in June 2026 (located at `requirements-analysis.md` in the project root).

---

## 1. Vision

claude-codex-chat puts the power of the world's leading AI models — Anthropic Claude, OpenAI GPT, and Google Gemini — into a single, polished mobile experience that feels as natural as sending a text message. Users switch between models in a tap, carry full conversation history across devices, and send text, images, files, and voice without friction.

The product does not try to reinvent AI chat. It wins by executing the established pattern better on mobile: faster streaming, a cleaner UI, reliable multi-provider access, and a privacy model users can trust. For the many people who pay separately for Claude Pro and ChatGPT Plus, this app offers one subscription that covers all three leading providers.

Success in v1 is a daily-active user base that returns because the app is reliably fast, honest about its limits, and pleasant to use — not because of any single novel feature.

---

## 2. Target User

### 2.1 Jobs To Be Done

- **Get a fast, capable answer** without switching between three different apps or browser tabs.
- **Continue a conversation later** from any device without losing context.
- **Ask about an image or document** by attaching it directly in the chat.
- **Dictate a question** hands-free while cooking, commuting, or otherwise occupied.
- **Control which model answers** when one consistently works better for a given task.
- **Trust that my conversations are private** and can be deleted entirely on demand.
- **Stay within a budget** by knowing which tier I'm on and how close I am to limits.

### 2.2 Non-Users (v1)

- Enterprises requiring SSO, audit logs, or team-level administration — out of scope for v1.
- Developers needing raw API access or custom model fine-tuning.
- Users who need real-time web search grounding (deferred to v2).

### 2.3 Key User Journeys

**UJ-1. Priya gets a quick answer on the go.**
Priya, a graduate student, unlocks her phone between classes. She is already authenticated. She opens the app, taps the compose bar, and types a question about a statistics concept. The app routes to her default model (Claude Sonnet), streams the response in under 3 seconds, renders the LaTeX-style formula inline, and she reads the answer before her next class starts. The conversation is saved automatically; she can return to it on her laptop later.

**UJ-2. Marcus switches models for a coding task.**
Marcus, a freelance developer, is mid-conversation debugging a Python script. The response quality isn't clicking for him. He taps the model badge in the conversation header, selects GPT-4o from the bottom sheet, and continues the conversation — the history carries over. He finds GPT-4o's response more useful for this task and continues.

**UJ-3. Aisha asks about a photo.**
Aisha, a home cook, is at the grocery store and photographs an ingredient label she can't read. She opens a new conversation, taps the attachment icon, picks the photo from her gallery, types "what's in this?" and sends. The app routes to Claude Sonnet (image-capable), streams a plain-English breakdown of the ingredients. She makes her purchasing decision in under 30 seconds.

**UJ-4. James converts speech to a message.**
James is commuting and wants to draft a complex question without typing. He taps the microphone in the input bar, dictates his question, the app transcribes it inline for review, he confirms and sends. The AI responds; he toggles text-to-speech to have it read aloud through his earbuds.

**UJ-5. Fatima hits her free tier limit and upgrades.**
Fatima, a casual user, hits the 20-message daily cap. The app shows a friendly modal: "You've used today's free messages. Upgrade to Pro for unlimited access." She taps "See Pro," reviews the feature comparison, taps "Start Pro — $9.99/month," completes Apple Pay, and immediately sends her next message. No restart required.

**UJ-6. David deletes his data before leaving.**
David is moving to a competing app. He opens Settings → Privacy → Export Data, downloads a JSON archive of all his conversations, then taps Delete All Data. The app asks for confirmation, executes the deletion, and signs him out. His account and all server-side data are purged within 30 days per GDPR.

---

## 3. Glossary

- **Conversation** — A named, persistent thread of messages between the user and one AI model. A user may have many Conversations. Conversations persist on the server and sync across devices.
- **Message** — A single unit of content sent by either the User or the AI within a Conversation. Messages may contain text, images, or file attachments.
- **Model** — A specific AI model variant provided by a Provider (e.g., "Claude Sonnet 4.6", "GPT-4o"). Each Model has a declared capability set (text-only, image-capable, etc.).
- **Provider** — An AI company whose API the backend proxies: Anthropic, OpenAI, or Google.
- **Streaming** — The technique of delivering AI response tokens to the client incrementally as they are generated, rather than waiting for the full response.
- **System Prompt** — A hidden instruction prepended to a Conversation that shapes the AI's behavior for that Conversation.
- **Custom Instructions** — User-defined global text applied as a System Prompt prefix to every new Conversation unless overridden.
- **Persona** — A saved System Prompt template the user can apply to any Conversation.
- **Tier** — The user's subscription level: Free or Pro.
- **Daily Cap** — The maximum number of Messages a Free-tier user may send per calendar day (UTC).
- **Attachment** — An image or file linked to a Message before it is sent.

---

## 4. Features

### 4.1 Authentication & Account Management

**Description:** Users create accounts with email/password or via Google and Apple Sign-In. Sessions persist across devices via JWT refresh tokens. Accounts are deletable. Realizes UJ-6.

**Functional Requirements:**

#### FR-1: Email registration and login
The User can register with an email address and password. The system validates email format and enforces a minimum password length of 8 characters. On success, the system issues a JWT access token and a refresh token.

**Consequences:**
- Duplicate email returns HTTP 409 with a user-facing message "An account with this email already exists."
- Invalid credentials return HTTP 401; the client shows "Incorrect email or password."
- After 5 consecutive failed attempts, the account is rate-limited for 15 minutes.

#### FR-2: Social login — Google and Apple
The User can authenticate via Google Sign-In or Apple Sign-In using OAuth 2.0 / Sign in with Apple flows. On first login, the system creates an account. On subsequent logins, the system links to the existing account by provider identity.

**Consequences:**
- If the social email matches an existing email/password account, the system prompts the user to link accounts rather than creating a duplicate.
- Apple's "hide my email" relay addresses are accepted.

#### FR-3: Password reset
The User can request a password reset via email. The system sends a one-time link valid for 60 minutes.

#### FR-4: Session management
The system issues JWT access tokens (15-minute expiry) and rotating refresh tokens (30-day expiry). The client refreshes silently in the background. On refresh token expiry, the user is signed out and prompted to re-authenticate.

#### FR-5: Account deletion
The User can request full account deletion from Settings. The system immediately signs the user out, queues data erasure, and completes erasure within 30 days. [ASSUMPTION: 30-day erasure window is sufficient for GDPR compliance given backup retention policies.]

---

### 4.2 Conversation Management

**Description:** Users create, browse, rename, search, pin, archive, and delete Conversations. History syncs across devices in real time. Realizes UJ-1, UJ-6.

**Functional Requirements:**

#### FR-6: Create a new Conversation
The User can start a new Conversation via a prominent "New chat" button. The system assigns a default title derived from the first Message sent (first 60 characters, truncated with ellipsis).

**Consequences:**
- A new Conversation is not persisted to the server until the first Message is sent.

#### FR-7: Conversation list
The system displays all the User's Conversations in a scrollable list, ordered by most recent Message timestamp descending. Each row shows: title, model name, and relative timestamp ("2 hours ago").

**Consequences:**
- Free-tier users see Conversations from the last 30 days only; older ones are grayed out with an upgrade prompt.
- Pro-tier users see unlimited history.

#### FR-8: Rename a Conversation
The User can rename any Conversation by long-pressing the row or from within the Conversation via the header overflow menu. Max title length: 100 characters.

#### FR-9: Delete a Conversation
The User can delete a Conversation (with a confirmation dialog). Deletion is soft-deleted on the server for 7 days before permanent erasure, allowing recovery. [ASSUMPTION: 7-day soft-delete is acceptable; no explicit undo UI in v1.]

#### FR-10: Search Conversations
The User can search Conversations by keyword. The system performs a full-text search across Conversation titles and Message content. Results are ranked by recency. Realizes UJ-1.

**Consequences:**
- Search is scoped to the user's own Conversations only.
- Results highlight the matching keyword in context.

#### FR-11: Pin a Conversation
The User can pin up to 5 Conversations. Pinned Conversations appear at the top of the list, separated by a divider. [ASSUMPTION: 5-pin limit is sufficient for v1.]

#### FR-12: Archive a Conversation
The User can archive a Conversation. Archived Conversations are hidden from the main list but remain searchable and accessible via an "Archived" filter.

---

### 4.3 AI Chat & Streaming

**Description:** The core interaction loop. Users send Messages and receive Streaming AI responses with rich Markdown rendering, code highlighting, and controls to regenerate, edit, copy, and stop. Realizes UJ-1, UJ-2, UJ-3, UJ-4.

**Functional Requirements:**

#### FR-13: Send a text Message
The User can compose and send a text Message. The system appends the Message to the Conversation, routes it to the active Model via the backend proxy, and begins streaming the response.

**Consequences:**
- The input bar is disabled while a response is streaming.
- Empty Messages (whitespace only) cannot be sent.

#### FR-14: Streaming response rendering
The system renders AI response tokens incrementally as they arrive via Server-Sent Events (SSE). The client appends tokens to the Message bubble in real time with a blinking cursor.

**Consequences:**
- First token must appear within 3 seconds for 95th-percentile requests under normal network conditions. [ASSUMPTION: Provider latency is within the 3-second budget for the models in scope.]
- If the SSE stream drops, the client shows a retry button.

#### FR-15: Markdown rendering
The system renders Markdown in AI response Messages: headers, bold/italic, lists, blockquotes, tables, inline code, and fenced code blocks.

#### FR-16: Code block display
Code blocks are rendered with syntax highlighting (via a lightweight client-side library) and a "Copy" button. The detected language is displayed as a label. [ASSUMPTION: Client-side highlighting library adds < 200 KB to bundle size.]

#### FR-17: Stop response
The User can cancel an in-progress streaming response via a "Stop" button. The system closes the SSE stream and preserves whatever tokens were already rendered.

#### FR-18: Regenerate response
The User can regenerate the AI's last response. The system re-sends the preceding user Message and replaces the previous AI response.

#### FR-19: Edit and resubmit a Message
The User can tap a sent Message to edit its text and resubmit. The system replaces all subsequent Messages in the Conversation with the new exchange. The original Messages are discarded. [ASSUMPTION: Discarding subsequent messages on edit is the expected behavior; no branching/versioning in v1.]

#### FR-20: Copy a Message
The User can copy any Message's text content to the system clipboard via a long-press or tap-hold context menu.

#### FR-21: Share a Conversation
The User can export the current Conversation as plain text or a screenshot image via the native system share sheet.

---

### 4.4 Multi-Model Support

**Description:** Users select the active Model per Conversation from a bottom sheet. The model badge in the header reflects the current selection. Capability badges inform the user what each Model supports. Realizes UJ-2.

**Functional Requirements:**

#### FR-22: Model selector
The User can change the active Model for a Conversation by tapping the model badge in the conversation header. The system presents a bottom sheet listing available Models grouped by Provider.

**Consequences:**
- Changing the Model mid-Conversation applies to subsequent Messages only; prior Messages retain their original model context.
- Free-tier users see a lock icon on models above their tier with an upgrade prompt.

#### FR-23: Model availability by tier

| Model | Tier Required |
|-------|--------------|
| Claude Haiku 4.5 | Free |
| Claude Sonnet 4.6 | Pro |
| Claude Opus 4.8 | Pro |
| GPT-4o mini | Free |
| GPT-4o | Pro |
| Gemini 1.5 Flash | Free |
| Gemini 1.5 Pro | Pro |

[ASSUMPTION: Model availability and tier mapping may change as provider pricing evolves; the list above reflects v1 intent.]

#### FR-24: Model capability badges
Each Model in the selector displays capability badges: "Text", "Images", "Files". Selecting an image-capable Model when an image is attached proceeds normally; selecting a text-only Model when an image is attached shows a warning that the attachment will be ignored.

#### FR-25: Default model preference
The User can set a default Model in Settings. New Conversations open with the default Model pre-selected.

---

### 4.5 Multimodal Input

**Description:** Users attach images and files to Messages and use voice for both input composition and AI response playback. Realizes UJ-3, UJ-4.

**Functional Requirements:**

#### FR-26: Image attachment
The User can attach one or more images to a Message via camera capture or gallery picker. Preview thumbnails are shown in the input area before sending. The system accepts JPEG, PNG, WEBP, and HEIC formats up to 20 MB per image.

**Consequences:**
- Attaching an image to a text-only Model triggers a warning (see FR-24).
- Up to 5 images per Message. [ASSUMPTION: 5-image limit is consistent with provider constraints.]

#### FR-27: File attachment
The User can attach files (PDF, DOCX, TXT) up to 25 MB per file to a Message. The system extracts text content server-side and includes it in the Model's context.

**Consequences:**
- Unsupported file types are rejected with a user-facing error naming the accepted formats.
- Files that exceed 25 MB are rejected before upload with a size error.

#### FR-28: Voice input
The User can tap a microphone button in the input bar to dictate a Message. The system uses on-device speech recognition to transcribe the dictation inline. The user can review and edit the transcription before sending.

**Consequences:**
- Voice input requires microphone permission; if denied, the button is disabled with a tooltip explaining how to grant it.
- Transcription is performed on-device (no audio sent to the backend).

#### FR-29: Voice output
The User can enable text-to-speech for AI responses via a speaker icon on any Message bubble. The system reads the response text aloud using the platform's native TTS engine. The User can toggle auto-read in Settings. Realizes UJ-4.

**Consequences:**
- TTS is skipped for code blocks.

---

### 4.6 Custom Instructions & Personas

**Description:** Users define global custom instructions that shape every AI response, and save named Personas (System Prompt templates) for quick reuse. Realizes UJ-1.

**Functional Requirements:**

#### FR-30: Global custom instructions
The User can write a global custom instructions text (max 1,500 characters) in Settings. The system prepends this text as a System Prompt prefix on every new Conversation.

**Consequences:**
- Custom instructions do not retroactively affect existing Conversations.
- The user can disable global custom instructions without deleting them.

#### FR-31: Per-conversation system prompt override
The User can set a System Prompt specific to a Conversation (max 3,000 characters) via the Conversation settings sheet. This overrides the global custom instructions for that Conversation.

#### FR-32: Saved Personas
The User can save named Personas (name + System Prompt text) and apply them to any Conversation from the model/settings sheet. Up to 20 saved Personas. [ASSUMPTION: 20 Persona limit is sufficient for v1.]

---

### 4.7 Subscription & Monetization

**Description:** The app is freemium. Free-tier users have a Daily Cap and access to a subset of Models. Pro-tier users get unlimited Messages, all Models, and multimodal features. In-app purchase via App Store and Google Play. Realizes UJ-5.

**Functional Requirements:**

#### FR-33: Free tier enforcement
The system tracks Messages sent per user per calendar day (UTC). When the Daily Cap (20 Messages) is reached, subsequent send attempts are blocked and the user is shown an upgrade prompt.

**Consequences:**
- The cap resets at 00:00 UTC.
- The UI displays a usage meter ("14 / 20 messages today") visible in the conversation list header.

#### FR-34: Pro tier features
Pro-tier users have: unlimited Messages, access to all Models (FR-23), image attachments (FR-26), file attachments (FR-27), voice I/O (FR-28, FR-29), unlimited conversation history, and priority request queue.

#### FR-35: Subscription purchase
The User can subscribe to Pro from the upgrade prompt or from Settings → Subscription. The purchase is processed via the native store (Apple App Store / Google Play Billing) with RevenueCat as the entitlement layer. Monthly ($9.99/month) and annual ($89.99/year — [ASSUMPTION: annual pricing at ~25% discount]) plans are offered.

**Consequences:**
- On successful purchase, Pro features activate immediately without restart.
- RevenueCat webhook updates the backend entitlement; the client polls for confirmation with a 5-second timeout then falls back to local receipt validation.

#### FR-36: Subscription management
The User can view their subscription status (tier, renewal date, plan), and navigate to the platform's subscription management screen to cancel or change plan, from Settings → Subscription.

#### FR-37: Restore purchases
The User can restore purchases on a new device via a "Restore" button on the Subscription screen.

---

### 4.8 Settings & Personalization

**Description:** Users control appearance, defaults, notifications, and language. Dark mode is available from day one.

**Functional Requirements:**

#### FR-38: Appearance
The User can choose Light, Dark, or System (follows OS setting) theme. The system applies the chosen theme immediately and persists across sessions.

#### FR-39: Font size
The User can choose from three font size presets (Small, Medium, Large) that scale the chat message text. [ASSUMPTION: Three presets are sufficient; full dynamic type support is deferred to v2.]

#### FR-40: Default model
The User can set a default Model (see FR-25) from Settings.

#### FR-41: Auto-read TTS toggle
The User can enable or disable automatic TTS playback for all AI responses from Settings (default: off).

#### FR-42: Notification preferences
The User can enable or disable push notifications for: (a) response complete when the app is backgrounded. [ASSUMPTION: Background push is desirable but depends on OS background processing limits; implementation feasibility to be confirmed in architecture.]

#### FR-43: App language
The system defaults to the device language for UI strings. [ASSUMPTION: v1 supports English only; additional locales deferred to v2.]

---

### 4.9 Data & Privacy Controls

**Description:** Users can export their data, delete conversations or their entire account, and control server-side history storage. Realizes UJ-6.

**Functional Requirements:**

#### FR-44: Data export
The User can request a full export of their conversation data in JSON format from Settings → Privacy → Export Data. The system generates the archive and delivers a download link via email within 24 hours. [ASSUMPTION: Async email delivery is acceptable; in-app download deferred to v2.]

#### FR-45: Conversation history opt-out
The User can disable server-side conversation history storage from Settings → Privacy. When disabled, Messages are processed by the Model but not persisted; the Conversation is purged from the server at session end. Local caching is also disabled.

#### FR-46: Data deletion
The User can delete all conversation data (without deleting the account) from Settings → Privacy → Delete All Conversations. Confirmation is required. Permanent deletion completes within 7 days.

Account deletion with full erasure is covered in FR-5.

---

## 5. Non-Goals (Explicit)

- **Web app companion** — v1 mobile only.
- **Team / workspace features** — no shared conversations, admin panels, or team billing.
- **Real-time web search grounding** — deferred to v2.
- **Code execution sandbox** — deferred to v2.
- **Self-hosted or local LLM support** — not in scope.
- **Video input** — image and audio only in v1.
- **Branching conversation history** — edited Messages replace subsequent history (see FR-19); no tree view.
- **Custom model fine-tuning or API access** — consumer app only.
- **Enterprise SSO / SAML** — not in scope for v1.

---

## 6. Platform & Information Architecture

**Platform:** iOS 16+ and Android 8.0+, built with Flutter. The backend is Python 3.12 + FastAPI. AI provider APIs are proxied server-side; no API keys are stored in the mobile app.

**Top-Level Surfaces:**

1. **Onboarding** — Value prop screens → Sign Up / Sign In.
2. **Conversation List** (drawer / sidebar) — All, Pinned, Archived, Search.
3. **Chat Screen** — Message thread, input bar (text, attach, voice, send), model badge.
4. **Model Selector** (bottom sheet) — Provider groups, capability badges, tier locks.
5. **Settings** — Profile, Subscription, Appearance, Custom Instructions, Personas, Privacy, About.
6. **Subscription Screen** — Tier comparison, purchase, manage, restore.

---

## 7. Aesthetic and Tone

The app's visual language should feel clean, spacious, and trustworthy — closer to the stripped-down elegance of the native Claude mobile app than the feature-dense density of some competitor apps. [ASSUMPTION: Final visual design direction to be confirmed with a designer in the UX phase.]

- **Color:** Neutral backgrounds, a single accent color used sparingly for actions and highlights.
- **Typography:** System fonts (SF Pro on iOS, Roboto on Android) for readability and native feel.
- **Motion:** Subtle, purposeful animations — streaming text cursor, message send animation. No decorative motion.
- **Tone of app-generated text** (error messages, empty states, upgrade prompts): Direct, honest, friendly. Never condescending. Never uses AI-generated filler.
- **Dark mode:** Full dark theme from v1; not an afterthought.

---

## 8. Cross-Cutting Non-Functional Requirements

### 8.1 Performance

- **NFR-1:** First token rendered within 3 seconds for 95th-percentile requests under normal mobile network conditions.
- **NFR-2:** App cold start under 3 seconds on a mid-range device (e.g., iPhone 12, Pixel 6).
- **NFR-3:** Conversation list loads within 1 second for users with up to 500 Conversations.
- **NFR-4:** Offline mode: cached Conversations are readable without internet; sending is blocked with a clear "No connection" indicator.

### 8.2 Security

- **NFR-5:** AI provider API keys are stored exclusively on the backend. The mobile app holds only the user's JWT.
- **NFR-6:** All client-backend communication is over HTTPS with TLS 1.3 minimum.
- **NFR-7:** Passwords are hashed with Argon2id server-side.
- **NFR-8:** All API endpoints enforce rate limiting (per user and per IP).
- **NFR-9:** JWT access tokens expire in 15 minutes; refresh tokens rotate on use and expire in 30 days.
- **NFR-10:** File uploads are scanned for malware server-side before being forwarded to AI providers. [ASSUMPTION: A lightweight malware scanning step (e.g., ClamAV) is feasible within acceptable latency.]
- **NFR-11:** Input length limits enforced server-side: Messages max 32,000 characters; System Prompts max 3,000 characters.

### 8.3 Scalability & Reliability

- **NFR-12:** Backend is stateless and horizontally scalable.
- **NFR-13:** API uptime target: 99.5% monthly.
- **NFR-14:** Provider failover: if a Provider API is unavailable, the system surfaces a clear error and suggests the user switch to another Provider. [ASSUMPTION: Automatic cross-provider failover is out of scope for v1; manual fallback is sufficient.]

### 8.4 Accessibility

- **NFR-15:** WCAG 2.1 AA contrast ratios in both light and dark themes.
- **NFR-16:** All interactive elements have accessible labels for TalkBack (Android) and VoiceOver (iOS).
- **NFR-17:** Minimum touch target size 44×44 pt.
- **NFR-18:** The app is operable without voice features (voice is additive, not required).

---

## 9. Constraints and Guardrails

### 9.1 Privacy & Compliance

- The app must comply with GDPR (EU) and CCPA (California) at launch.
- A Privacy Policy and Terms of Service must be published before App Store / Play Store submission.
- User data export (FR-44) and full erasure (FR-5, FR-46) must be implemented before launch.
- Conversation data must not be used for AI model training without explicit opt-in consent. [ASSUMPTION: Default is opt-out of training data use; users may opt in for a benefit if a program is introduced in v2.]

### 9.2 Cost

- Per-token AI provider costs are absorbed by the backend. Free-tier Daily Cap (FR-33) is the primary cost control mechanism.
- [ASSUMPTION: At 20 messages/day per free user on lightweight models (Haiku, Flash, GPT-4o mini), blended cost per free user is manageable at launch scale. Cost modeling to be validated before GA launch.]

### 9.3 App Store

- The app must follow Apple App Store Review Guidelines and Google Play Developer Policy, including in-app purchase requirements (no external payment links in-app for iOS).
- Age rating: 12+ (AI-generated content, no explicit material guardrails in v1 beyond provider content policies).

---

## 10. Monetization

| | Free | Pro ($9.99/mo / $89.99/yr) |
|---|---|---|
| Messages per day | 20 | Unlimited |
| Models | Haiku, Flash, GPT-4o mini | All models |
| Image attachments | — | ✓ |
| File attachments | — | ✓ |
| Voice I/O | — | ✓ |
| Conversation history | 30 days | Unlimited |
| Priority queue | — | ✓ |

Billing is managed through RevenueCat for both stores. Stripe is reserved for a future web companion.

---

## 11. MVP Scope

### 11.1 In Scope (Phase 1 — months 1–3)

- Auth: email/password + Google + Apple Sign-In (FR-1 through FR-5)
- Conversation create, list, delete, rename (FR-6 through FR-9)
- Text chat with streaming for Claude and OpenAI (FR-13 through FR-20)
- Model selector — Claude Haiku/Sonnet, GPT-4o mini/4o (FR-22 through FR-25)
- Free tier enforcement + usage meter (FR-33)
- Pro subscription via App Store / Google Play (FR-35 through FR-37)
- Dark / light mode (FR-38)
- Basic settings (default model, appearance)

### 11.2 Phase 2 — months 4–5

- Gemini provider integration
- Image and file attachments (FR-26, FR-27)
- Voice input and output (FR-28, FR-29)
- Custom instructions and Personas (FR-30 through FR-32)
- Search, pin, archive Conversations (FR-10 through FR-12)
- Data export and deletion (FR-44 through FR-46)
- Conversation history opt-out (FR-45)

### 11.3 Phase 3 — month 6

- Performance hardening and profiling
- Accessibility audit
- App Store and Play Store submission
- GA launch

---

## 12. Success Metrics

**Primary**

- **SM-1:** Day-7 retention ≥ 30% of new installs. Validates UJ-1 (users find ongoing value). Validates FR-7, FR-13, FR-14.
- **SM-2:** Free-to-Pro conversion rate ≥ 5% within 14 days of first use. Validates FR-33, FR-35. UJ-5.
- **SM-3:** Streaming first-token latency P95 ≤ 3 seconds (client-side measured). Validates NFR-1.

**Secondary**

- **SM-4:** Conversations per active user per week ≥ 3. Validates UJ-1.
- **SM-5:** Multimodal feature adoption (images or voice used) ≥ 20% of Pro users within 30 days. Validates FR-26, FR-28. UJ-3, UJ-4.
- **SM-6:** App Store and Google Play rating ≥ 4.3 stars within 60 days of launch.
- **SM-7:** Monthly churn of Pro subscribers ≤ 5%.

**Counter-metrics (do not optimize at the expense of these)**

- **SM-C1:** Average Messages per session. Counterbalances SM-1 and SM-4 — high message volume may indicate the AI is failing to give useful answers, not high engagement.
- **SM-C2:** Support ticket volume on data deletion / privacy. Counterbalances SM-2 — a high conversion rate achieved by making cancellation hard is not success.

---

## 13. Open Questions

1. **App name** — "claude-codex-chat" is a working title. Final brand name and App Store listing name are TBD.
2. **Annual pricing** — $89.99/year is assumed (~25% discount vs monthly). Needs pricing strategy confirmation.
3. **Push notifications (FR-42)** — Background processing constraints on iOS (BGAppRefreshTask) may limit reliability. Architecture to confirm feasibility.
4. **File extraction server-side (FR-27)** — DOCX extraction library choice (e.g., python-docx, unstructured) and handling of encrypted / corrupted files TBD.
5. **Malware scanning (NFR-10)** — ClamAV adds latency; alternative lightweight scanning approach TBD by architecture.
6. **Data export delivery (FR-44)** — Async email delivery assumed; in-app download preferred but deferred. Revisit if user research shows friction.
7. **Conversation history for Free tier (FR-7)** — 30-day limit assumed. Confirm this is compelling enough to drive upgrades without alienating free users.
8. **Provider failover (NFR-14)** — Manual fallback only in v1. Automatic failover is a v2 candidate depending on incident frequency.

---

## 14. Assumptions Index

- **§4.1 / FR-5** — 30-day account erasure window is GDPR-compliant given backup retention.
- **§4.2 / FR-9** — 7-day soft-delete with no undo UI is acceptable in v1.
- **§4.2 / FR-11** — 5 pinned conversations is a sufficient limit.
- **§4.3 / FR-14** — Provider latency is within the 3-second first-token budget for models in scope.
- **§4.3 / FR-16** — Client-side syntax highlighting library adds < 200 KB to bundle.
- **§4.3 / FR-19** — Discarding subsequent Messages on edit is the expected behavior.
- **§4.4 / FR-23** — Model/tier mapping reflects v1 intent; subject to provider pricing changes.
- **§4.5 / FR-26** — 5-image-per-message limit is consistent with provider constraints.
- **§4.6 / FR-32** — 20 Persona limit is sufficient.
- **§4.7 / FR-35** — Annual plan at $89.99 (~25% discount); needs pricing confirmation.
- **§4.8 / FR-43** — v1 English only; additional locales deferred to v2.
- **§4.9 / FR-44** — Async email delivery of export archive is acceptable.
- **§7** — Final visual design direction to be confirmed with designer in UX phase.
- **§9.1** — Default is opt-out of training data use.
- **§9.2** — Blended per-free-user cost is manageable at launch scale; requires cost modeling before GA.
- **§8.2 / NFR-10** — Lightweight malware scanning is feasible within acceptable latency.

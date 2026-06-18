# Product Requirements Document
## AI Chat Mobile App

**Version:** 1.0  
**Date:** June 2026  
**Status:** Draft

---

## Table of Contents

1. [Product Overview](#1-product-overview)
2. [Goals & Success Metrics](#2-goals--success-metrics)
3. [User Personas](#3-user-personas)
4. [User Stories](#4-user-stories)
5. [Feature Specifications](#5-feature-specifications)
6. [UI/UX Specifications](#6-uiux-specifications)
7. [Backend API Contracts](#7-backend-api-contracts)
8. [Data Models](#8-data-models)
9. [Non-Functional Requirements](#9-non-functional-requirements)
10. [Out of Scope (v1)](#10-out-of-scope-v1)
11. [Phased Delivery Plan](#11-phased-delivery-plan)
12. [Risks & Mitigations](#12-risks--mitigations)
13. [Open Decisions](#13-open-decisions)

---

## 1. Product Overview

### 1.1 Vision

A polished, consumer-grade AI chat application for iOS and Android that gives users access to the world's leading AI models — Anthropic Claude, OpenAI GPT, and Google Gemini — in one unified interface. The app delivers a fluid, streaming chat experience with multimodal input support, conversation management, and a transparent freemium model.

### 1.2 Problem Statement

Users who want to interact with multiple AI providers must juggle separate apps with different UX patterns, separate subscriptions, and no unified conversation history. Power users pay for overlapping plans, while casual users face high paywalls for basic access.

### 1.3 Solution

A single app that:
- Aggregates the best AI models behind a unified, polished chat UX.
- Proxies all AI calls through a secure backend — no API key exposure on device.
- Offers a generous free tier and a single Pro subscription for full access.

### 1.4 Platforms

| Platform | Target |
|----------|--------|
| iOS | 16.0+ |
| Android | API 29+ (Android 10+) |
| Web | Out of scope for v1 |

---

## 2. Goals & Success Metrics

### 2.1 Business Goals

| Goal | KPI | Target (6 months post-launch) |
|------|-----|-------------------------------|
| User acquisition | Total registered users | 50,000 |
| Retention | D30 retention | ≥ 25% |
| Monetization | Pro conversion rate | ≥ 5% of MAU |
| Revenue | MRR | $25,000 |
| Quality | App Store / Play Store rating | ≥ 4.4 stars |

### 2.2 Product Goals

- Time-to-first-message for new users ≤ 90 seconds from app open.
- Streaming latency ≤ 100 ms token-to-render.
- Zero incidents of AI API keys leaked to client.
- 99.5% backend uptime.

---

## 3. User Personas

### Persona 1 — The Everyday Explorer (Primary)

- **Name:** Maya, 28, content writer
- **Tech comfort:** Medium — uses ChatGPT occasionally, not a developer
- **Goals:** Quick answers, creative writing help, summarizing articles
- **Frustrations:** Paywalls after a few messages, confusing model names, no history sync
- **Key needs:** Simple UX, fast responses, affordable price, dark mode

### Persona 2 — The Power User (Secondary)

- **Name:** Arjun, 34, software engineer
- **Tech comfort:** High — pays for multiple AI subscriptions
- **Goals:** Code review, debugging, technical explanations, comparing model outputs
- **Frustrations:** Separate apps for each model, no custom system prompts, no file uploads
- **Key needs:** Model switching, file/image uploads, custom instructions, markdown + syntax highlighting

### Persona 3 — The Student (Secondary)

- **Name:** Zoe, 21, university student
- **Tech comfort:** Medium — heavy mobile user, budget-conscious
- **Goals:** Homework help, essay drafts, quick fact lookups
- **Frustrations:** Expensive subscriptions, session limits mid-conversation
- **Key needs:** Free tier with meaningful limits, voice input, conversation history

---

## 4. User Stories

### 4.1 Authentication

| ID | As a… | I want to… | So that… | Priority |
|----|-------|-----------|----------|----------|
| US-01 | New user | Sign up with email and password | I can create an account | Must |
| US-02 | New user | Sign up with Google or Apple | I can skip form filling | Must |
| US-03 | Returning user | Log in and stay logged in | I don't re-enter credentials each session | Must |
| US-04 | User | Reset my password via email | I can recover access | Must |
| US-05 | User | Delete my account and all data | I can exercise my right to erasure | Must |

### 4.2 Conversations

| ID | As a… | I want to… | So that… | Priority |
|----|-------|-----------|----------|----------|
| US-06 | User | Start a new conversation | I can talk about a new topic | Must |
| US-07 | User | See all my past conversations in a list | I can continue where I left off | Must |
| US-08 | User | Rename a conversation | I can identify it later | Should |
| US-09 | User | Delete a conversation | I can clean up clutter | Must |
| US-10 | User | Search conversations by keyword | I can find a specific thread | Should |
| US-11 | User | Pin/star a conversation | Important chats are always at the top | Should |
| US-12 | User | Archive a conversation | I can hide old threads without deleting | Could |
| US-13 | User | Access conversations on a new device | History is always in sync | Must |

### 4.3 Messaging

| ID | As a… | I want to… | So that… | Priority |
|----|-------|-----------|----------|----------|
| US-14 | User | Send a message and see a streaming response | The AI feels fast and responsive | Must |
| US-15 | User | See formatted responses (markdown, code blocks) | Technical content is readable | Must |
| US-16 | User | Copy a code block with one tap | I can use code quickly | Must |
| US-17 | User | Regenerate the last AI response | I can get a different answer | Must |
| US-18 | User | Edit my sent message and re-submit | I can fix a typo without restarting | Should |
| US-19 | User | Stop a response mid-stream | I can interrupt a long wrong answer | Must |
| US-20 | User | Copy any message to clipboard | I can share or use the content elsewhere | Should |
| US-21 | User | Share a conversation as text or screenshot | I can share useful AI answers | Could |

### 4.4 Model Selection

| ID | As a… | I want to… | So that… | Priority |
|----|-------|-----------|----------|----------|
| US-22 | User | Switch the AI model for a conversation | I can choose the best model for my task | Must |
| US-23 | User | See which model is active at a glance | I'm not confused about who I'm talking to | Must |
| US-24 | User | Set a default model in settings | New conversations start with my preference | Should |
| US-25 | User | See model capability badges | I know if a model supports images before uploading | Should |

### 4.5 Multimodal Input

| ID | As a… | I want to… | So that… | Priority |
|----|-------|-----------|----------|----------|
| US-26 | Pro user | Upload an image from gallery or camera | I can ask questions about visuals | Must |
| US-27 | Pro user | Upload a PDF or document | I can get summaries or analysis | Must |
| US-28 | User | Record a voice message as my input | I can chat hands-free | Should |
| US-29 | User | Hear AI responses read aloud | I can consume content without reading | Could |
| US-30 | User | Preview attachments before sending | I can confirm I'm sending the right file | Must |

### 4.6 Custom Instructions

| ID | As a… | I want to… | So that… | Priority |
|----|-------|-----------|----------|----------|
| US-31 | User | Set global instructions applied to every chat | The AI always knows my context/preferences | Should |
| US-32 | User | Override the system prompt per conversation | I can give different personas per context | Should |
| US-33 | User | Save named prompt templates | I can quickly apply a "Code Review" or "Tutor" persona | Could |

### 4.7 Settings & Personalization

| ID | As a… | I want to… | So that… | Priority |
|----|-------|-----------|----------|----------|
| US-34 | User | Switch between dark and light mode | I can use the app comfortably | Must |
| US-35 | User | Change the font size | The text is comfortable to read | Should |
| US-36 | User | Set my preferred response language | The AI replies in my language | Could |

### 4.8 Monetization

| ID | As a… | I want to… | So that… | Priority |
|----|-------|-----------|----------|----------|
| US-37 | Free user | See how many messages I have left today | I know when I'll hit the limit | Must |
| US-38 | User | Upgrade to Pro from within the app | I can unlock all features seamlessly | Must |
| US-39 | Pro user | Manage or cancel my subscription | I'm in control of billing | Must |
| US-40 | User | Restore a previous purchase | My Pro status survives reinstalls | Must |

### 4.9 Data & Privacy

| ID | As a… | I want to… | So that… | Priority |
|----|-------|-----------|----------|----------|
| US-41 | User | Export all my data as JSON or plain text | I have a copy of my conversations | Must |
| US-42 | User | Delete all my conversation history | I can wipe my data without deleting the account | Must |
| US-43 | User | Toggle server-side history saving off | My conversations are never stored on the server | Should |

---

## 5. Feature Specifications

### 5.1 Authentication

#### 5.1.1 Email/Password Registration

**Flow:**
1. User enters email + password (min 8 chars, 1 uppercase, 1 number).
2. Backend validates uniqueness, hashes password with Argon2.
3. Sends verification email with a 24-hour token link.
4. User taps link → account activated → JWT + refresh token issued.

**Acceptance Criteria:**
- [ ] Duplicate email returns HTTP 409 with a user-facing message.
- [ ] Password field shows/hides toggle.
- [ ] Verification email arrives within 60 seconds.
- [ ] Unverified users can log in but see a banner prompting verification.

#### 5.1.2 Social Login — Google & Apple

**Flow:**
1. User taps "Continue with Google/Apple".
2. App invokes platform OAuth (Google Sign-In SDK / Sign in with Apple).
3. Backend receives id_token, verifies with provider, creates or retrieves user record.
4. JWT + refresh token issued and stored in secure storage.

**Acceptance Criteria:**
- [ ] Apple Sign-In required on iOS for App Store compliance.
- [ ] Google login works on both platforms.
- [ ] If email already registered via email/password, accounts are merged (or user is prompted).
- [ ] Tokens stored in Flutter Secure Storage (Keychain / Android Keystore backed).

#### 5.1.3 JWT Session Management

- Access token TTL: 15 minutes.
- Refresh token TTL: 30 days, rotated on each use.
- Refresh tokens stored in httpOnly cookie on backend; mobile uses secure local storage.
- Silent refresh: app auto-refreshes access token before expiry.
- Revocation: logout invalidates refresh token server-side.

#### 5.1.4 Password Reset

1. User enters email on forgot-password screen.
2. Backend always returns success (no email enumeration).
3. If email exists, sends reset link (valid 1 hour, single use).
4. New password subject to same validation rules.

---

### 5.2 Conversation Management

#### 5.2.1 Conversation List

**Data shown per row:**
- Conversation title (auto-generated from first message, user-editable)
- Last message preview (first 80 chars)
- Timestamp (relative: "2 hours ago", absolute on hover/long-press)
- Active model badge
- Pinned indicator (if pinned)

**Sorting:** Pinned first, then recency descending.

**Pagination:** Infinite scroll, 20 conversations per page.

**Acceptance Criteria:**
- [ ] List loads within 1 second on launch.
- [ ] Swipe-left on a row shows Delete and Archive actions.
- [ ] Long-press shows contextual menu: Rename, Pin/Unpin, Archive, Delete.
- [ ] Real-time update when a new message arrives in another conversation (via WebSocket or polling).

#### 5.2.2 Conversation Auto-Title

- Backend generates a title by sending the first user message to a lightweight model (Haiku / GPT-4o-mini) with prompt: `"Summarize the following in 6 words or fewer: <message>"`.
- Title generated asynchronously after first assistant response.
- Falls back to first 40 chars of user's message if generation fails.

#### 5.2.3 Search

- Client-side search on the loaded conversation list for instant results.
- Server-side full-text search (PostgreSQL `tsvector`) for queries matching older conversations not yet loaded.
- Debounce: 300 ms.
- Results highlight matching keywords.

---

### 5.3 Messaging & AI Chat

#### 5.3.1 Message Sending & Streaming

**Request flow:**
1. User taps Send.
2. App POSTs to `/api/v1/conversations/{id}/messages` with user message + attachments.
3. Backend appends user message to DB, then opens SSE stream to AI provider.
4. Each SSE `data:` chunk is forwarded to the client as it arrives.
5. On stream end, backend persists the complete assistant message and usage metadata.

**Client rendering:**
- Tokens appended to the bubble in real time.
- Cursor blink animation while streaming.
- Message status: `sending → streaming → complete | error`.

**Acceptance Criteria:**
- [ ] First token appears within 1.5 seconds of send (p95).
- [ ] Streaming renders at ≥ 30 fps without jank.
- [ ] Network loss mid-stream shows an error state with a Retry button.
- [ ] Duplicate sends prevented (button disabled until previous response completes or is cancelled).

#### 5.3.2 Markdown Rendering

Supported elements:

| Element | Rendering |
|---------|-----------|
| `**bold**`, `_italic_` | Bold, italic text |
| `` `inline code` `` | Monospace, tinted background |
| ```` ```lang\ncode\n``` ```` | Syntax-highlighted block with language label + copy button |
| `# H1` – `### H3` | Scaled heading text |
| `- item` / `1. item` | Bullet and numbered lists |
| `> quote` | Left-bordered quote block |
| `| table |` | Scrollable table (horizontal scroll if overflow) |
| `[link](url)` | Tappable link, opens in in-app browser |
| `---` | Horizontal rule |

**Streaming note:** Markdown parser must handle partial tokens gracefully — no flickering or broken rendering mid-stream.

#### 5.3.3 Code Block

- Language detected from fence tag (e.g., ` ```python `).
- Syntax highlight library: `flutter_highlight` or `re_highlight`.
- Copy button top-right corner → copies raw code string to clipboard → shows "Copied!" toast for 1.5 s.
- Long code blocks scroll vertically within the bubble; max height 300 dp before scroll.

#### 5.3.4 Message Actions

Accessed via long-press on a message bubble:

| Action | User message | Assistant message |
|--------|-------------|------------------|
| Copy | ✓ | ✓ |
| Edit & Re-submit | ✓ | ✗ |
| Regenerate | ✗ | ✓ (last message only) |
| Delete | ✓ | ✓ |
| Share | ✓ | ✓ |

**Edit & Re-submit:** Editing a user message truncates all subsequent messages in the conversation (with a confirmation dialog: "This will remove all messages after this one. Continue?").

**Regenerate:** Deletes the last assistant message and re-sends the last user message to the same model.

#### 5.3.5 Stop / Cancel

- Cancel button appears below the streaming bubble.
- On tap: client sends `DELETE /api/v1/conversations/{id}/messages/current-stream`.
- Backend terminates the AI provider connection.
- Partial response is saved as-is with a `[response cancelled]` suffix.

---

### 5.4 Multi-Model Support

#### 5.4.1 Model Selector Bottom Sheet

Triggered from model badge in the conversation header.

**Layout:** Grouped by provider:

```
Anthropic
  ○ Claude Haiku 4.5       — Fast & affordable    [Free]
  ● Claude Sonnet 4.6      — Balanced             [Pro]
  ○ Claude Opus 4.8        — Most capable         [Pro]

OpenAI
  ○ GPT-4o mini            — Fast & affordable    [Free]
  ○ GPT-4o                 — Multimodal flagship  [Pro]

Google
  ○ Gemini 1.5 Flash       — Fastest              [Free]
  ○ Gemini 1.5 Pro         — Advanced reasoning   [Pro]
```

- Pro-only models shown with a lock icon to free users; tapping prompts upgrade.
- Model change takes effect from the next message (history is preserved).
- Selected model persists per conversation.

#### 5.4.2 Model Capability Badges

Shown next to model name:

| Badge | Meaning |
|-------|---------|
| 📷 Images | Accepts image attachments |
| 📄 Files | Accepts PDF/DOCX |
| ⚡ Fast | Optimized for low latency |
| 🧠 Powerful | High reasoning capability |

---

### 5.5 Multimodal Input

#### 5.5.1 Image Upload

- Sources: device camera (via `image_picker`) or photo library.
- Formats: JPEG, PNG, WEBP, GIF (first frame).
- Max size: 20 MB per image; max 5 images per message.
- Client compresses images > 5 MB to ≤ 5 MB before upload.
- Upload flow: client uploads to `/api/v1/upload/image` → receives `attachment_id` → includes in message payload.
- Backend re-encodes to provider-required format before forwarding.

**Acceptance Criteria:**
- [ ] Thumbnails shown in input bar preview strip.
- [ ] Tap thumbnail to preview full image; long-press to remove.
- [ ] Unsupported model + image combo shows tooltip: "This model doesn't support images. Switch to [recommended model]."

#### 5.5.2 File Upload

- Formats: PDF, DOCX, TXT, CSV, XLSX.
- Max size: 25 MB per file; max 3 files per message.
- Client shows file name + size + file type icon in preview strip.
- Backend extracts text content server-side and injects into the prompt context for models that don't natively accept files (e.g., GPT-4o with text extraction fallback).

#### 5.5.3 Voice Input (Speech-to-Text)

- Uses device-native STT: `speech_to_text` Flutter package.
- Hold-to-talk button in input bar.
- Transcription appears in the text input field — user can edit before sending.
- Language: auto-detect or follows response language setting.

#### 5.5.4 Voice Output (Text-to-Speech)

- Uses device TTS: `flutter_tts`.
- Triggered by tapping a speaker icon on an assistant message.
- Plays back response sentence by sentence as it streams.
- Respects device silent/ring mode.

---

### 5.6 Custom Instructions

#### 5.6.1 Global Instructions

- Found in Settings → Custom Instructions.
- Stored as a single text field (max 2,000 chars) per user in the database.
- Prepended to every conversation's system prompt as: `"The user has provided the following instructions:\n{instructions}"`.
- Can be temporarily disabled with a toggle without deleting.

#### 5.6.2 Per-Conversation System Prompt

- Found in conversation header → "…" menu → Set System Prompt.
- Overrides global instructions for that conversation only.
- Stored per conversation record; shown in a distinct "System" bubble at the top of the chat.

#### 5.6.3 Prompt Templates (Personas)

- User can save named templates (e.g., "Code Reviewer", "English Tutor").
- Stored in the `prompt_templates` table.
- Applying a template fills the per-conversation system prompt field.
- Templates are editable and deletable.
- Max 20 templates per user.

---

### 5.7 Settings & Personalization

| Setting | Options | Default | Storage |
|---------|---------|---------|---------|
| Theme | Light / Dark / System | System | Local + remote |
| Font size | Small / Medium / Large / XL | Medium | Local + remote |
| Default model | Any available model | Claude Haiku 4.5 | Remote (user profile) |
| Response language | Auto / English / Spanish / French / German / Japanese / Arabic / … (20 languages) | Auto | Remote |
| Notifications | On / Off per type | All on | Remote |
| App language | Device default / 10 languages | Device default | Local |
| Server-side history | On / Off | On | Remote |

---

### 5.8 Monetization

#### 5.8.1 Free Tier Limits

| Resource | Limit |
|----------|-------|
| Messages per day | 20 (across all free models) |
| Models available | Claude Haiku 4.5, GPT-4o mini, Gemini 1.5 Flash |
| Image uploads | Not available |
| File uploads | Not available |
| Voice I/O | Not available |
| Conversation history retention | 30 days |
| Custom instructions | Available |

**Limit enforcement:** Backend tracks daily message count per user in Redis (key: `msg_count:{user_id}:{date}`, TTL midnight UTC). At 20, the API returns HTTP 429 with `{"error": "daily_limit_reached"}`. The app shows an upgrade prompt.

**Usage meter:** Visible on the conversation list screen header: "18 / 20 messages used today".

#### 5.8.2 Pro Tier — $9.99/month

| Resource | Limit |
|----------|-------|
| Messages per day | Unlimited |
| Models | All models |
| Image uploads | ✓ (up to 5/message) |
| File uploads | ✓ (up to 3/message) |
| Voice I/O | ✓ |
| Conversation history | Unlimited |
| Priority queue | ✓ |

#### 5.8.3 In-App Purchase

- Platform: RevenueCat SDK (wraps StoreKit 2 on iOS, Google Play Billing v6 on Android).
- Entitlement name: `pro`.
- Product IDs: `com.app.chat.pro.monthly` (iOS + Android).
- Annual plan: `com.app.chat.pro.annual` at $79.99/year (20% discount) — Phase 2.

**Purchase flow:**
1. User taps Upgrade on paywall screen.
2. App calls RevenueCat `Purchases.purchasePackage()`.
3. On success, RevenueCat webhooks the backend → backend sets `user.tier = 'pro'`.
4. App receives entitlement grant → UI updates immediately.

**Restore purchases:** Explicit Restore button on subscription screen; calls `Purchases.restorePurchases()`.

#### 5.8.4 Subscription Management Screen

Shows:
- Current plan (Free / Pro)
- Next billing date (for Pro)
- Cancel subscription (deep-links to App Store / Play Store subscription management)
- Restore purchase button

---

### 5.9 Data & Privacy

#### 5.9.1 Data Export

- User requests export from Settings → Data & Privacy → Export Data.
- Backend queues an async job; sends an email with a download link within 10 minutes.
- Format: ZIP containing:
  - `conversations.json` — all conversations and messages.
  - `profile.json` — account info and settings.
  - `attachments/` — uploaded files (if history saving is on).
- Download link expires in 48 hours.

#### 5.9.2 Data Deletion

- "Delete all conversation history" — wipes all messages and conversations; account remains.
- "Delete account" — full GDPR erasure: user record, messages, attachments, all personal data. Confirmation email sent. Process completes within 30 days per GDPR requirement.

#### 5.9.3 Disable Server-Side History

When toggled off:
- Backend still processes messages to generate AI responses but does not persist them to the database.
- Conversation list is cleared on the server.
- A "Private mode" banner is shown in the chat header.

---

## 6. UI/UX Specifications

### 6.1 Navigation Structure

```
App
├── Onboarding (unauthenticated)
│   ├── Welcome / Value Prop Screen
│   ├── Sign Up Screen
│   └── Log In Screen
│
└── Main App (authenticated)
    ├── Sidebar Drawer
    │   ├── New Chat button
    │   ├── Conversation List (scrollable)
    │   ├── ── divider ──
    │   ├── Settings
    │   └── User profile + plan badge
    │
    ├── Chat Screen (main content area)
    │   ├── Header: conversation title + model badge + "..." menu
    │   ├── Message List (scrollable)
    │   └── Input Bar: text field + attach + voice + send
    │
    └── Settings Screen (full-screen modal)
        ├── Profile
        ├── Subscription
        ├── Custom Instructions
        ├── Appearance
        ├── Data & Privacy
        └── About
```

### 6.2 Key Screen Layouts

#### Chat Screen — Input Bar

```
┌────────────────────────────────────────────────┐
│  [📎]  Type a message…                [🎤] [▶] │
└────────────────────────────────────────────────┘
```

- 📎 opens attachment picker (image + file options).
- 🎤 hold-to-talk voice input.
- ▶ send button; transforms to ⏹ stop button while streaming.
- Input bar expands up to 5 lines, then scrolls.
- Attachment previews appear above the input bar.

#### Message Bubble Layout

```
[User message]                    ╔══════════════╗
                                  ║ Message text ║
                                  ╚══════════════╝

[Assistant message]
╔════════════════════════════════════╗
║ [model icon] Claude Sonnet 4.6     ║
║                                    ║
║ Message text with *markdown*       ║
║                                    ║
║ ```python                          ║
║ def hello(): print("hi")           ║
║ ```              [Copy]            ║
╚════════════════════════════════════╝
```

### 6.3 Theming

| Token | Light | Dark |
|-------|-------|------|
| Background | `#FFFFFF` | `#0F0F0F` |
| Surface | `#F5F5F5` | `#1A1A1A` |
| User bubble | `#E8F0FE` | `#1E3A5F` |
| AI bubble | `#FFFFFF` | `#242424` |
| Primary accent | `#5C6BC0` | `#7986CB` |
| Code block bg | `#F3F4F6` | `#161B22` |
| Text primary | `#111111` | `#E8E8E8` |
| Text secondary | `#666666` | `#999999` |
| Destructive | `#E53935` | `#EF5350` |

Typography: System font (SF Pro / Roboto). Code font: `JetBrains Mono` or `Fira Code`.

### 6.4 Animations & Micro-interactions

| Trigger | Animation |
|---------|-----------|
| New message send | Bubble slides up from input bar (200 ms ease-out) |
| Streaming token | Fade-in per word chunk (80 ms) |
| Model selector sheet | Slide up (250 ms spring) |
| Swipe-to-delete | Slide reveal with red background |
| Upgrade prompt | Confetti burst on successful subscription |
| Copy code | Button flash + "Copied!" tooltip |

---

## 7. Backend API Contracts

Base URL: `https://api.{app-domain}.com/api/v1`

All endpoints require `Authorization: Bearer {access_token}` unless noted.

### 7.1 Auth

| Method | Path | Description |
|--------|------|-------------|
| POST | `/auth/register` | Register with email + password |
| POST | `/auth/login` | Login; returns access + refresh tokens |
| POST | `/auth/logout` | Revoke refresh token |
| POST | `/auth/refresh` | Exchange refresh token for new access token |
| POST | `/auth/forgot-password` | Send reset email |
| POST | `/auth/reset-password` | Apply new password with token |
| POST | `/auth/oauth/google` | Google OAuth sign-in |
| POST | `/auth/oauth/apple` | Apple Sign-In |

**POST /auth/register**

```json
Request:
{
  "email": "user@example.com",
  "password": "SecurePass123"
}

Response 201:
{
  "access_token": "eyJ...",
  "refresh_token": "...",
  "user": { "id": "uuid", "email": "...", "tier": "free" }
}

Error 409:
{ "error": "email_already_registered" }
```

### 7.2 Conversations

| Method | Path | Description |
|--------|------|-------------|
| GET | `/conversations` | List conversations (paginated) |
| POST | `/conversations` | Create new conversation |
| GET | `/conversations/{id}` | Get conversation + messages |
| PATCH | `/conversations/{id}` | Update title, pinned, archived |
| DELETE | `/conversations/{id}` | Delete conversation |
| GET | `/conversations/search?q={query}` | Full-text search |

**GET /conversations**

```
Query params: page (default 1), limit (default 20)

Response 200:
{
  "items": [
    {
      "id": "uuid",
      "title": "Python async question",
      "last_message_preview": "Thanks! One more thing...",
      "last_message_at": "2026-06-18T14:30:00Z",
      "model_id": "claude-sonnet-4-6",
      "pinned": false,
      "archived": false,
      "message_count": 12
    }
  ],
  "total": 87,
  "page": 1,
  "limit": 20
}
```

### 7.3 Messages & Streaming

| Method | Path | Description |
|--------|------|-------------|
| POST | `/conversations/{id}/messages` | Send message; returns SSE stream |
| DELETE | `/conversations/{id}/messages/current-stream` | Cancel active stream |
| PATCH | `/conversations/{id}/messages/{msg_id}` | Edit message |
| DELETE | `/conversations/{id}/messages/{msg_id}` | Delete message |

**POST /conversations/{id}/messages — SSE**

```
Request (multipart/form-data if attachments, else application/json):
{
  "content": "Explain async/await in Python",
  "model_id": "claude-sonnet-4-6",
  "attachment_ids": []
}

Response: text/event-stream
data: {"type": "token", "content": "Async"}
data: {"type": "token", "content": "/await"}
data: {"type": "token", "content": " in Python..."}
data: {"type": "done", "message_id": "uuid", "usage": {"input_tokens": 12, "output_tokens": 340}}
data: [DONE]
```

### 7.4 File Upload

| Method | Path | Description |
|--------|------|-------------|
| POST | `/upload/image` | Upload image; returns attachment_id |
| POST | `/upload/file` | Upload document; returns attachment_id |

**POST /upload/image**

```
Request: multipart/form-data, field: "file"
Max size: 20 MB

Response 201:
{
  "attachment_id": "uuid",
  "url": "https://cdn.../thumb.jpg",
  "mime_type": "image/jpeg",
  "size_bytes": 1204800
}

Error 413: { "error": "file_too_large", "max_bytes": 20971520 }
```

### 7.5 User & Settings

| Method | Path | Description |
|--------|------|-------------|
| GET | `/users/me` | Get current user profile + settings |
| PATCH | `/users/me` | Update display name, avatar, settings |
| DELETE | `/users/me` | Delete account (GDPR erasure) |
| GET | `/users/me/usage` | Daily message count + limits |
| POST | `/users/me/export` | Request data export |

### 7.6 Subscription

| Method | Path | Description |
|--------|------|-------------|
| POST | `/subscription/verify` | Verify RevenueCat purchase |
| GET | `/subscription/status` | Get current subscription status |

**POST /subscription/verify** (called by RevenueCat webhook, not the app directly)

```json
{
  "event": "INITIAL_PURCHASE",
  "app_user_id": "uuid",
  "product_id": "com.app.chat.pro.monthly",
  "entitlements": ["pro"]
}
```

---

## 8. Data Models

### 8.1 Users

```sql
CREATE TABLE users (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email           TEXT UNIQUE NOT NULL,
    password_hash   TEXT,                          -- null for OAuth-only users
    display_name    TEXT,
    avatar_url      TEXT,
    tier            TEXT NOT NULL DEFAULT 'free',  -- 'free' | 'pro'
    email_verified  BOOLEAN NOT NULL DEFAULT FALSE,
    oauth_providers JSONB DEFAULT '[]',            -- [{provider, provider_user_id}]
    settings        JSONB DEFAULT '{}',            -- theme, font_size, default_model, etc.
    custom_instructions TEXT,
    history_saving  BOOLEAN NOT NULL DEFAULT TRUE,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now(),
    deleted_at      TIMESTAMPTZ                    -- soft delete for GDPR
);
```

### 8.2 Conversations

```sql
CREATE TABLE conversations (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id         UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    title           TEXT NOT NULL DEFAULT 'New Chat',
    model_id        TEXT NOT NULL,
    system_prompt   TEXT,
    pinned          BOOLEAN NOT NULL DEFAULT FALSE,
    archived        BOOLEAN NOT NULL DEFAULT FALSE,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT now(),
    last_message_at TIMESTAMPTZ
);

CREATE INDEX idx_conversations_user_updated ON conversations(user_id, updated_at DESC);
CREATE INDEX idx_conversations_search ON conversations USING GIN(to_tsvector('english', title));
```

### 8.3 Messages

```sql
CREATE TABLE messages (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    conversation_id UUID NOT NULL REFERENCES conversations(id) ON DELETE CASCADE,
    role            TEXT NOT NULL,                 -- 'user' | 'assistant' | 'system'
    content         TEXT NOT NULL,
    model_id        TEXT,                          -- set for assistant messages
    attachment_ids  UUID[] DEFAULT '{}',
    input_tokens    INTEGER,
    output_tokens   INTEGER,
    cancelled       BOOLEAN NOT NULL DEFAULT FALSE,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE INDEX idx_messages_conversation ON messages(conversation_id, created_at ASC);
CREATE INDEX idx_messages_search ON messages USING GIN(to_tsvector('english', content));
```

### 8.4 Attachments

```sql
CREATE TABLE attachments (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id         UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    type            TEXT NOT NULL,                 -- 'image' | 'file'
    original_name   TEXT NOT NULL,
    mime_type       TEXT NOT NULL,
    size_bytes      INTEGER NOT NULL,
    storage_key     TEXT NOT NULL,                 -- S3/GCS object key
    thumbnail_url   TEXT,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

### 8.5 Prompt Templates

```sql
CREATE TABLE prompt_templates (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id         UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    name            TEXT NOT NULL,
    content         TEXT NOT NULL,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

### 8.6 Refresh Tokens

```sql
CREATE TABLE refresh_tokens (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id         UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    token_hash      TEXT NOT NULL UNIQUE,
    expires_at      TIMESTAMPTZ NOT NULL,
    revoked         BOOLEAN NOT NULL DEFAULT FALSE,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

### 8.7 Daily Usage (Redis)

```
Key:    msg_count:{user_id}:{YYYY-MM-DD}
Value:  integer (incremented per assistant message)
TTL:    Midnight UTC of the given day + 1 hour
```

---

## 9. Non-Functional Requirements

### 9.1 Performance

| Requirement | Target | Measurement |
|-------------|--------|-------------|
| App cold start | < 3 seconds | Device: mid-range Android (Pixel 6a) |
| First meaningful paint | < 2 seconds | Time to conversation list visible |
| First token latency | < 1.5 seconds (p95) | Send tap → first streaming token |
| Token render rate | ≥ 30 fps | No frame drops during stream |
| API response (non-streaming) | < 500 ms (p99) | Auth, list, CRUD endpoints |
| Offline read | Cached conversations readable with no network | |

### 9.2 Scalability

| Component | Strategy |
|-----------|---------|
| FastAPI workers | Stateless; horizontal scaling via container orchestration |
| Database | PostgreSQL with read replicas for list/search queries |
| Streaming | SSE; one long-lived connection per active message |
| Rate limiting | Redis sliding window per user per endpoint |
| File storage | S3/GCS with CDN for attachment delivery |

### 9.3 Security

| Control | Implementation |
|---------|---------------|
| AI API key management | Keys stored in backend env/secrets manager only; never in app bundle or responses |
| Transport security | TLS 1.3 enforced; HSTS header |
| Token storage (mobile) | `flutter_secure_storage` (Keychain on iOS, EncryptedSharedPreferences on Android) |
| Password hashing | Argon2id (memory: 64 MB, iterations: 3, parallelism: 4) |
| Rate limiting | 60 req/min per IP on auth endpoints; 200 req/min per user on chat endpoints |
| Input sanitization | Prompt injection mitigation at the API layer (content length limit, blocked patterns) |
| JWT | HS256 access token (15 min); refresh token rotation on every use; revocation list in Redis |
| File validation | MIME type + magic bytes check on upload; antivirus scan (ClamAV or equivalent) |
| CORS | Explicit origin allowlist; `credentials: true` only for verified origins |

### 9.4 Compliance

| Requirement | Implementation |
|-------------|---------------|
| GDPR Art. 17 (Right to erasure) | Account deletion API; 30-day completion SLA |
| GDPR Art. 20 (Data portability) | JSON/ZIP export on request |
| CCPA | Privacy policy; opt-out of data sale (N/A — we don't sell data) |
| App Store privacy labels | Accurately reflect all data types collected |
| Play Store data safety | Form completed before submission |
| COPPA | No service to users under 13; age gate on sign-up |

### 9.5 Availability

| Component | SLA |
|-----------|-----|
| API backend | 99.5% monthly uptime |
| Database | 99.9% (managed PostgreSQL with automated failover) |
| Object storage | 99.99% (S3/GCS SLA) |

---

## 10. Out of Scope (v1)

| Feature | Notes |
|---------|-------|
| Web app companion | Evaluate for v2 |
| Web search / grounding | Requires Tavily or Brave API integration — v2 |
| Code execution sandbox | Complex infra (container sandboxing) — v2 |
| Self-hosted LLM option | Ollama / local model support — not planned |
| Annual subscription plan | RevenueCat config ready; launch after iOS approval |
| Team / enterprise accounts | Single-user product for v1 |
| Plugin / tool ecosystem | No function calling or custom tools in v1 |
| Video input | Gemini supports it; deferred to v2 |
| RTL language support | Deferred after launch |
| iPad / tablet layout | Responsive but not optimized — v2 |

---

## 11. Phased Delivery Plan

### Phase 1 — MVP (Months 1–3)

**Goal:** Functional app on both platforms, shippable for beta.

| Feature | Notes |
|---------|-------|
| Email + Google/Apple auth | Core auth flow |
| Text chat — Claude Haiku + Sonnet | Streaming SSE |
| Text chat — GPT-4o mini + 4o | Streaming SSE |
| Conversation CRUD | Create, list, delete, auto-title |
| Markdown + syntax highlighting | Full renderer |
| Dark/light mode | System default |
| Free tier (20 messages/day) | Redis counter |
| Pro subscription (RevenueCat) | Monthly only |
| Basic settings | Theme, font size, default model |

**Exit criteria:** Internal beta available on TestFlight and Play Console internal testing.

### Phase 2 — Feature Complete (Months 4–5)

| Feature | Notes |
|---------|-------|
| Gemini Pro + Flash | Provider gateway update |
| Image uploads | Camera + gallery |
| File uploads | PDF, DOCX, TXT |
| Voice input (STT) | `speech_to_text` |
| Voice output (TTS) | `flutter_tts` |
| Custom instructions (global + per-convo) | Settings screen |
| Data export + deletion | Async job |
| Conversation search | FTS + client |
| Conversation rename, pin, archive | Swipe + context menu |
| Edit message & re-submit | Truncation confirmation |

**Exit criteria:** Feature freeze; QA testing begins; App Store / Play Store submission.

### Phase 3 — Polish & Launch (Month 6)

| Activity | Notes |
|----------|-------|
| Performance optimization | Cold start, render profiling |
| Accessibility audit | TalkBack / VoiceOver testing |
| Localization (English + 2 additional) | i18n framework in place |
| Analytics instrumentation | Mixpanel / Amplitude events |
| Crash reporting | Sentry or Firebase Crashlytics |
| App Store submission | Review time buffer: 1–3 days |
| Play Store submission | Review time buffer: 1–3 days |
| Soft launch (limited geography) | Validate metrics before global rollout |

---

## 12. Risks & Mitigations

| # | Risk | Likelihood | Impact | Mitigation |
|---|------|-----------|--------|-----------|
| R-01 | AI provider API pricing changes eat margin | Medium | High | Abstract provider layer; usage-based throttling; cost alerts |
| R-02 | App Store rejection (AI content policy) | Medium | High | Review Apple AI app guidelines; add content filters; prepare appeal |
| R-03 | Streaming latency issues on slow networks | High | Medium | Graceful degradation; retry logic; offline message queuing |
| R-04 | RevenueCat webhook delays cause entitlement lag | Low | High | Optimistic entitlement grant with server confirmation |
| R-05 | GDPR/data breach notification requirement | Low | Very High | Minimal data retention; encryption at rest; incident response plan |
| R-06 | Provider outage (Anthropic/OpenAI/Google down) | Medium | Medium | Per-provider error states; fallback model suggestion |
| R-07 | Flutter version fragmentation / breaking changes | Low | Medium | Pin Flutter version via FVM; staging environment for upgrades |

---

## 13. Open Decisions

| # | Decision | Owner | Target Date | Options |
|---|----------|-------|-------------|---------|
| OD-01 | App name and branding | Product | Month 1 | TBD — needs trademark check |
| OD-02 | Hosting provider | Engineering | Month 1 | Railway vs. Render vs. AWS ECS |
| OD-03 | State management library | Engineering | Month 1 | Riverpod vs. BLoC |
| OD-04 | Analytics platform | Product | Month 2 | Mixpanel vs. Amplitude vs. PostHog |
| OD-05 | Annual plan pricing | Product | Month 3 | $79.99 (20% off) vs. $69.99 (30% off) |
| OD-06 | Web search (v2 scope) | Product | Post-launch | Tavily vs. Brave Search API |

---

*Document version: 1.0 — June 2026*

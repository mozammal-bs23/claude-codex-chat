# Requirements Clarification Questions

## Platform & Audience

1. Target platform — iOS only, Android only, or both?
2. Who is the primary audience — general consumers, enterprise users, developers, a specific niche (students, writers, coders)?
3. Do you have a target region or language (English-only, multilingual, RTL support needed)?

## AI & Backend

4. Which AI provider(s) will power the app — Anthropic (Claude), OpenAI, Gemini, or your own model?
5. Will you need multi-model support (let users switch between models)?
6. Do you want to host your own backend, or call AI APIs directly from the app (proxy server vs. direct)?

## Core Features

7. Beyond basic chat, which features matter most — image input, file uploads, voice input/output, web search, code execution?
8. Do you need conversation history synced across devices, or local-only?
9. Should users be able to create/manage multiple conversations (like ChatGPT threads)?
10. Do you want system prompt / persona customization (e.g., "custom GPTs" style)?

## Auth & Users

11. How will users sign in — email/password, social login (Google/Apple), or anonymous/guest mode?
12. Should there be user tiers (free vs. paid), and if so, what limits (message quota, model access)?

## Monetization

13. What is the business model — subscription, one-time purchase, freemium, ads, B2B license?
14. Will you charge per token/message or offer flat-rate plans?

## Design & UX

15. Do you have a design direction or brand in mind, or is that part of this project?
16. Dark mode support — required from day one or later?

## Data & Privacy

17. Will conversations be stored on your servers? Any compliance requirements (GDPR, HIPAA, CCPA)?
18. Should users be able to export or delete their data?

## Technical Constraints

19. What tech stack are you leaning toward — Flutter, React Native, native Swift/Kotlin, or no preference?
20. Do you have an existing backend/infrastructure, or is this greenfield?

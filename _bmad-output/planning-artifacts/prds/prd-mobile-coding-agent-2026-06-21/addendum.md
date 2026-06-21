# Addendum: Provisional Technical Context

This addendum preserves technical implications from `requirement_v2.md`. It informs architecture work but is not part of the product capability contract.

## Ownership boundary

The confirmed product boundary is:

- **Poco Editor:** owns the editor, backend services, repositories/workspaces, agent execution, models, commands, Git operations, permissions, authoritative state, and APIs.
- **Our team:** owns the iOS/Android experience, secure credential handling, Poco API integration, mobile state/cache, push-notification presentation, mobile analytics/crash reporting, accessibility, app-store delivery, and mobile QA.

The mobile app should communicate only through documented Poco APIs. It should not connect directly to developer machines, Git providers, model providers, or execution runtimes unless Poco explicitly defines that integration as part of its supported API contract.

Whether Poco executes locally, in the cloud, or through a hybrid system is an internal Poco concern unless it changes mobile-visible availability or state semantics.

## State and transport implications

- Poco API state is authoritative for commands and tool actions.
- Commands and approval requests need durable unique IDs.
- Poco must supply ordered or cursor-based event replay and state reconciliation.
- An approval must be bound to the exact action payload presented to the user.
- Push notifications are advisory navigation; they do not independently authorize actions.
- Checkpoints cover workspace file changes, not external side effects.

## Security implications

- Workspace and organization boundaries must be enforced by Poco APIs.
- Mobile access and refresh credentials must be short-lived, scoped, and revocable.
- Sign-out must remove tokens and sensitive cached Poco data.
- Mobile diagnostics should exclude code, prompts, secrets, and command output by default.
- Persistent permission rules require scope visibility and revocation.
- An unrestricted bypass mode should only be considered in disposable isolated environments.

## Poco API dependency checklist

- Authentication, refresh, logout, MFA/SSO, and account recovery.
- User profile, organizations, entitlements, and feature flags.
- Projects, workspaces, repositories, branches, and worktrees.
- Session create/list/resume/cancel and transcript pagination.
- Real-time agent text, tool events, progress, errors, and replay cursor.
- Questions, permission requests, approval/denial, expiry, and audit data.
- File tree, file content, changed files, diff pagination, and checkpoints.
- Test/build results, usage/quota, commit/push/pull-request results.
- Device registration and push-notification routing.
- Environment URLs, schemas, error catalog, rate limits, versioning, and sandbox data.

## References

The requirements use current category behavior as research input, without committing to Anthropic's implementation:

- <https://code.claude.com/docs/en/how-claude-code-works>
- <https://code.claude.com/docs/en/remote-control>
- <https://code.claude.com/docs/en/permissions>
- <https://code.claude.com/docs/en/sessions>

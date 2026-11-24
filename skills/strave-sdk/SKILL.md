---
name: strave-sdk
description: Call Strave Public API v2, Sandbox mode, OIDC Trusted Workloads, cursor pagination, and live SSE streams with @strave/sdk in TypeScript or Effect v4.
---

# Strave Public API v2 SDK (`@strave/sdk`)

Choose between two first-class flavors from the same package:

- `@strave/sdk`: Zero-dependency plain TypeScript client (`createStraveClient`, `paginate`, `exchangeOidcWorkloadToken`).
- `@strave/sdk/effect`: Effect v4 `Context.Service` (`StraveClient`), `StraveClient.layer`, `StraveClient.sandboxLayer`, `StraveClient.layerFromEnv`, typed `Schema.TaggedError` failures, and `Stream` helpers.

## Rules

1. Prefer `oidcWorkload` over static `apiKey` in production. Routes like `replays.createViewerSession` strictly reject static API keys.
2. Start with `sandbox: true` (or `StraveClient.sandboxLayer()`) for zero-cost integration testing against fixed Siege test data.
3. Always pass strong `revision` values on writes (`If-Match`) and keep `Idempotency-Key` headers on POST commands.
4. Use `events.stream(eventId)` (or `streamEvent` in Effect v4) instead of polling loops.

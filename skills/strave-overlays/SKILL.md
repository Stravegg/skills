---
name: strave-overlays
description: Build self-hosted OBS overlays driven by Strave live SSE streams and Broadcast Control with @strave/overlay-sdk.
---

# Self-Hosted Strave OBS Overlays

Strave's hosted third-party overlay runtime is not online yet. Build custom OBS browser sources on your own host or local machine today by connecting `@strave/overlay-sdk` to a live SSE stream.

## Architecture

1. Your backend authenticates to Strave Public API v2 using an **OIDC Trusted Workload** token (`createStraveClient({ oidcWorkload })`) and proxies `GET /api/v2/events/{eventId}/stream` to your OBS browser source. Never put Strave OIDC tokens or secrets inside browser HTML.
2. During development, point directly at the zero-cost Sandbox stream `https://strave.gg/api/v2/sandbox/events/event_sbx_spring_major/stream` or run `strave overlay dev`.
3. Inside your OBS browser source, subscribe with `createLiveOverlaySource`:

```ts
import { createLiveOverlaySource } from "@strave/overlay-sdk";

const overlay = createLiveOverlaySource({
	streamUrl: "https://your-backend.example.com/api/strave-stream",
});

overlay.subscribe(({ revision, value }) => {
	renderScoreboard(value, revision);
});
```

## Updating live scores

Use Broadcast Control routes (`PUT /playdays/{playdayId}/overlay-score`, `POST /activate`) with strong `If-Match` revision tags and `Idempotency-Key` headers.

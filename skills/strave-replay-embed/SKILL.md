---
name: strave-replay-embed
description: Mint OIDC-authenticated 2D replay viewer sessions and control Strave's hosted /embed/replay player with @strave/replay-embed.
---

# Strave 2D Replay Viewer Embeds

Use this skill when embedding Strave's interactive 2D Rainbow Six Siege replay viewer inside a third-party tournament site, team portal, or match recap page.

## Security invariants

1. **OIDC JWT required in production**: `POST /api/v2/replays/{matchId}/{mapIndex}/viewer-sessions` enforces a short-lived JWKS-signed OIDC/OAuth 2.1 access token (`oidcWorkload` or OAuth 2.1 flow). Static `strave_sk_` API keys are rejected with `401 invalid_token`.
2. **No raw `.rec` files**: The embed loads compressed `.stv` viewer files from Strave storage through short-lived session tokens. Never attempt to feed raw `.rec` files client-side.
3. **Origin binding**: Pass the exact `https://` origin of the page that mounts the iframe. Wildcards are rejected.
4. **Required watermark**: Strave renders the "Powered by Strave" badge inside the iframe origin.

## Step 1: Mint a viewer session on your backend

```ts
import { createStraveClient } from "@strave/sdk";
import { getVercelOidcToken } from "@vercel/oidc";

const strave = createStraveClient({
	oidcWorkload: {
		clientId: process.env.STRAVE_CLIENT_ID!,
		getAssertion: () =>
			getVercelOidcToken({ audience: "https://strave.gg/api/auth" }),
		scopes: ["event:read"],
	},
});

const { data: session } = await strave.replays.createViewerSession(matchId, 0, {
	eventId,
	origin: "https://tournaments.example.com",
	ttlSeconds: 300,
	initialRound: 1,
});
```

For zero-cost local development, mint a Sandbox session with `createStraveClient({ sandbox: true })` and `origin: "http://localhost:3000"`.

## Step 2: Mount and control the iframe in the browser

```tsx
import { StraveReplayEmbed } from "@strave/replay-embed/react";

export function MatchReplayPanel({ embedUrl }: { embedUrl: string }) {
	return (
		<div className="aspect-video w-full">
			<StraveReplayEmbed
				embedUrl={embedUrl}
				onEvent={(event) => {
					if (event.type === "ready") {
						console.log("Rounds:", event.rounds);
					}
				}}
			/>
		</div>
	);
}
```

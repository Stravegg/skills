---
name: strave-auth
description: Authenticate Rainbow Six Siege players with @strave/auth and authenticate backend servers via zero-secret OIDC Trusted Workloads.
---

# Login with Strave & OIDC Trusted Workloads

## Sign players in (`@strave/auth`)

Strave is an OpenID Connect provider (`https://strave.gg/api/auth`). Use
`@strave/auth` to sign players in with PKCE S256 and read their verified
Ubisoft Siege profiles and Discord ID at zero cost.

- Scopes: `openid profile email siege discord`
- Helpers: `readStraveIdentity`, `primarySiegeProfile`, `fetchStraveUserInfo`
- Better Auth plugin: `@strave/auth/better-auth`
- React button: `@strave/auth/react`

## Authenticate backend servers with OIDC Trusted Workloads (recommended)

Never use static `strave_sk_` API keys when your server runs on Vercel, GitHub
Actions, GitLab CI, or Google Cloud. High-security endpoints such as
`POST /api/v2/replays/{matchId}/{mapIndex}/viewer-sessions` strictly require
short-lived OIDC/OAuth 2.1 JWT access tokens and reject static API keys.

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
```

---
name: strave-webhooks
description: Receive, verify, and locally test Strave Public API v2 webhooks using HMAC SHA-256 headers and @strave/cli.
---

# Strave Webhooks (`@strave/sdk/webhooks`)

Strave signs every webhook delivery with HMAC SHA-256 over `${timestamp}.${rawBody}` and sends three headers:

- `strave-webhook-id`
- `strave-webhook-timestamp` (Unix seconds)
- `strave-webhook-signature` (`v1=<hex>`)

Always verify the raw request text before parsing JSON.

## Route handler (Next.js, Hono, Bun, Elysia, Cloudflare Workers)

```ts
import { createWebhookHandler } from "@strave/sdk/webhooks";

export const POST = createWebhookHandler({
	secret: process.env.STRAVE_WEBHOOK_SECRET!,
	onEvent: async ({ deliveryId, event }) => {
		console.log(deliveryId, event.type, event.data.resourceId);
	},
});
```

## Effect v4 handler

```ts
import { Effect } from "effect";
import { verifyWebhookSignatureEffect } from "@strave/sdk/effect";

export const verify = (rawBody: string, headers: Headers, secret: string) =>
	verifyWebhookSignatureEffect({ rawBody, headers, secret });
```

## Local testing with `@strave/cli`

```sh
bunx @strave/cli trigger match.result_accepted --url http://127.0.0.1:3000/api/webhooks/strave
```

# fetch-tweets

> Recent tweets from any X/Twitter handle. No developer account, no $100/month Basic tier — pay-per-request in USDC.

A tiny example that fetches a user's recent tweets without dealing with X's pricing tiers, rate limits, or developer-account approval. [EntRoute](https://entroute.com) discovers the best-ranked Twitter data endpoint; [x402](https://x402.org) handles payment.

**Why this exists:** X's API tier ladder starts at $100/month and goes up fast. For agents that need tweets occasionally, that's absurd. x402 lets you pay $0.001–$0.01 per call.

---

## Quickstart — Claude Code (recommended, zero config)

```bash
claude mcp add entroute -- npx @entroute/mcp-server
```

Then ask Claude: *"What did @paulg post this week?"* — it discovers a verified endpoint, pays in USDC on Base, and returns the tweets. No API keys, no wallet setup, no env vars.

[About the MCP server →](https://entroute.com/docs/mcp)

---

## Use it from your own code

```bash
npm install @entroute/sdk-agent-ts
```

Inspect the top-ranked endpoint:

```ts
import { EntRouteClient } from '@entroute/sdk-agent-ts';

const client = new EntRouteClient({ baseUrl: 'https://api.entroute.com' });
const result = await client.discover({ capability_id: 'social.twitter.user' });
console.log(result.ranked_endpoints[0]);
```

The discovery response includes a `sample_request` showing exactly which params each endpoint expects (often `username`, sometimes `user_id`). To call it with automatic payment handling, pass a Base wallet via `client.discoverAndCall()`. See the [SDK docs](https://entroute.com/docs/sdk).

---

## Try the demo

Discovery only — prints the top 3 endpoints with score, latency, success rate, and price. No payment, no wallet needed.

```bash
git clone https://github.com/entroute/fetch-tweets && cd fetch-tweets
pnpm install
pnpm start
```

Source: [`src/index.ts`](./src/index.ts).

---

## Why EntRoute

The only x402 directory that pays real USDC to verify each endpoint actually works. Probes run every 10 minutes; failed endpoints get demoted automatically.

- **Site:** https://entroute.com  •  **Docs:** https://entroute.com/docs/quickstart
- **MCP server:** [`@entroute/mcp-server`](https://www.npmjs.com/package/@entroute/mcp-server)
- **TypeScript SDK:** [`@entroute/sdk-agent-ts`](https://www.npmjs.com/package/@entroute/sdk-agent-ts)

[x402](https://x402.org) is the open pay-per-request protocol behind it (Coinbase + Linux Foundation). Server returns 402, client pays in USDC on Base, retries with payment header.

---

## Related skills

- [`get-crypto-prices`](https://github.com/entroute/get-crypto-prices) — Token prices without CoinGecko
- [`web-search-agent`](https://github.com/entroute/web-search-agent) — Web search for AI agents
- [`summarize-url`](https://github.com/entroute/summarize-url) — Summarize any article URL
- [`describe-image`](https://github.com/entroute/describe-image) — Image alt-text + understanding
- [`get-weather`](https://github.com/entroute/get-weather) — Weather without sign-up
- [`place-search`](https://github.com/entroute/place-search) — Find places nearby without Google Places
- [`qr-generator`](https://github.com/entroute/qr-generator) — QR codes via x402

## License

MIT

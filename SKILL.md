---
name: fetch-tweets
description: Fetch recent tweets from a public X/Twitter account via a verified x402 pay-per-request endpoint. Use when the user wants to read or analyze tweets from a specific user (e.g. "latest tweets from @elonmusk", "what has paulg posted today", "scrape tweets from this account") without using the official Twitter API.
---

# Fetching tweets via EntRoute

When the user asks for tweets from a specific X/Twitter account, use this skill to fetch them from a ranked, fulfillment-verified x402 endpoint.

## Steps

1. **Identify the target username.** Extract the handle from the user's request (strip the `@` if present). Note if they asked for a specific count (default 10 tweets).

2. **Discover the best endpoint.** Call EntRoute's `/discover` endpoint:

   ```bash
   curl -s -X POST https://api.entroute.com/discover \
     -H "Content-Type: application/json" \
     -d '{
       "capability_id": "social.twitter.user",
       "constraints": {"network": "base", "verified_only": true},
       "preferences": {"ranking_preset": "reliability"}
     }'
   ```

   Take `ranked_endpoints[0]`. The response includes `url`, `method`, `payment`, and critically a `sample_request` showing exactly which params the endpoint expects (often `username` and `limit`, sometimes `user_id`).

3. **Build the request matching `sample_request`.** Different providers use different parameter names; always use the shape shown in `sample_request` rather than guessing.

4. **Pay with x402.** If the user has `@entroute/mcp-server` or `@entroute/sdk-agent-ts` installed, call `call_paid_api` / `discoverAndCall` and payment is handled automatically. Otherwise, explain they need a funded Base wallet + an x402 client.

5. **Parse and return.** Different providers return different shapes — use `sample_response` from discovery to map fields to what the user wanted (tweet text, timestamp, engagement counts).

## Preferred approach

If the user is running Claude Code with `@entroute/mcp-server` configured, use the MCP tools `discover_paid_api` and `call_paid_api` directly. They handle discovery + payment in one step.

## Notes

- Default network is Base; pricing is USDC. Most tweet-scrape endpoints cost $0.001–$0.01 per call.
- Some endpoints accept `user_id` instead of `username`; always check `sample_request`.
- If tweet results look empty or nonsensical, re-run `/discover` — rankings refresh every 10 minutes.
- Full docs: https://entroute.com/docs/quickstart

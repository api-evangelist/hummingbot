---
name: Swap tokens on a DEX via Gateway
description: >-
  Get a swap quote and execute a token swap on any Gateway-supported chain and
  DEX connector (Jupiter, Raydium, Meteora, Uniswap, PancakeSwap, 0x, Orca).
api: openapi/hummingbot-gateway-openapi-original.json
generated: '2026-07-19'
method: generated
operations:
  - GET /trading/swap/quote
  - POST /trading/swap/execute
---

# Swap tokens on a DEX via Gateway

Hummingbot Gateway standardizes DEX access behind one REST API (default
`http://localhost:15888`). Use it to quote and execute swaps without writing
chain-specific code. All endpoints below are grounded in the published Gateway
OpenAPI (`openapi/hummingbot-gateway-openapi-original.json`).

## Preconditions
- Gateway is running with a wallet passphrase set (`pnpm start --passphrase=...`).
  In production it serves HTTPS with TLS certs; use `--dev` for local HTTP.
- A wallet has been added and set as default (`POST /wallet/add`,
  `POST /wallet/setDefault`).
- Requests and responses are JSON. See `conventions/hummingbot-conventions.yml`
  for the request/response and error-envelope shape, and
  `rate-limits/hummingbot-rate-limits.yml` (100 requests/minute).

## Steps

1. **Quote the swap** — `GET /trading/swap/quote`
   Query params include `chainNetwork` (e.g. `solana-mainnet-beta`,
   `ethereum-mainnet`), `connector` (e.g. `jupiter/router`, `raydium/amm`,
   `uniswap/clmm`), `baseToken`, `quoteToken`, `amount`, and `side` (`BUY`|`SELL`).
   Inspect the returned price and estimated amounts before executing.

2. **Execute the swap** — `POST /trading/swap/execute`
   Submit the same chain/connector/token/amount/side parameters (plus the wallet)
   to broadcast the swap transaction. The response returns the transaction result.

## Notes
- Prefer connector-specific endpoints (e.g. `/connectors/jupiter/router/quote-swap`)
  when you need connector-native behavior; `/trading/swap/*` is the chain-agnostic
  path that routes via the network's configured swap provider.
- On failure the API returns a FastAPI-style `{"detail": ...}` JSON envelope with
  a 400/404/500 status — see `errors/hummingbot-problem-types.yml`.
- Authentication is process-level (passphrase-encrypted wallet + TLS), not a
  per-request token — see `authentication/hummingbot-authentication.yml`.

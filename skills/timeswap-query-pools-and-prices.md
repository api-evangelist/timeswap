---
name: Query Timeswap pools and token prices
description: >-
  Read Timeswap V2 pool data, pool caps, tracked tokens, and token spot prices
  from the public api.timeswap.io service.
api: openapi/timeswap-api-openapi.json
operations:
  - submit_token_pools_tokens_get_get
  - get_pool_pools_pools_get_get
  - get_cap_pools_cap_get
  - submit_token_token_spotPrice_get
---

# Query Timeswap pools and token prices

Base URL: `https://api.timeswap.io` — no authentication required (public read
endpoints, see `authentication/timeswap-authentication.yml`).

## Steps

1. **List tokens for a chain** — `GET /pools/tokens/get` (operationId
   `submit_token_pools_tokens_get_get`) with `chain_id=<EVM chain id>`.
   Use the returned token contract addresses in the next steps.
2. **Fetch pools** — `GET /pools/pools/get` (operationId
   `get_pool_pools_pools_get_get`) with optional `chain_id`,
   `token0_address`, `token1_address`, and `is_prod=true` to filter to
   production pools for a token pair.
3. **Check a pool's cap** — `GET /pools/cap` (operationId
   `get_cap_pools_cap_get`) with the `poolId` from step 2.
4. **Price a token** — `GET /token/spotPrice` (operationId
   `submit_token_token_spotPrice_get`) with `chainId` and `address`;
   pass `refresh=true` only when a cached price is not acceptable.

## Rules

- All parameters are query parameters; invalid or missing required parameters
  return a `422` FastAPI validation envelope (`detail[]` of
  `{loc, msg, type}` — see `errors/timeswap-problem-types.yml`).
- There is no pagination, idempotency contract, or rate-limit signaling
  (see `conventions/timeswap-conventions.yml`); keep polling modest and rely
  on the server-side cache rather than `refresh=true`.
- Do not call the POST/PUT pool and token submission operations — they are
  registry-maintenance endpoints for the Timeswap team, not consumer surface.

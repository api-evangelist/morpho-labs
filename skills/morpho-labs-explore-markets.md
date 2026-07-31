---
name: Explore Morpho Blue markets and rates
description: Discover Morpho Blue lending markets on a chain, inspect a market's assets/APYs/LLTV, and assess collateral at risk.
api: graphql/morpho-labs-schema-introspection.json
operations: [markets, marketById, marketCollateralAtRisk]
auth: none (public GraphQL API)
endpoint: https://api.morpho.org/graphql
---

# Explore Morpho Blue markets and rates

Use the Morpho public GraphQL API to find and analyze Morpho Blue lending markets. No API key is required. Stay under 750 requests/minute and keep query complexity below 1,000,000 (base cost 10 per operation).

## Steps

1. **List markets on a chain.** Query `markets(first, skip, where, orderBy, orderDirection)`. Filter with a `where` that sets `chainId_in` and, optionally, loan/collateral asset symbols. Read `pageInfo { countTotal count }` to paginate with `first`/`skip`.
2. **Inspect one market.** For a specific market call `marketById(id, chainId)` (or `markets(where: { uniqueKey_in: [...] })`). Read `loanAsset`, `collateralAsset`, `oracle`, `lltv`, and `state { supplyApy borrowApy utilization supplyAssetsUsd borrowAssetsUsd }`.
3. **Assess risk.** Call `marketCollateralAtRisk(uniqueKey, chainId, numberOfPoints)` to estimate collateral that would be liquidated at simulated price levels.

## Rules

- Always scope queries by `chainId` — the same asset symbol exists on multiple chains.
- Prefer address/uniqueKey-based lookups over deprecated ID/compound-key fields (see the API changelog for scheduled field removals).
- On HTTP `429`, back off; sustained abuse leads to multi-day cooldowns.
- Errors arrive in the GraphQL top-level `errors[]` array alongside partial `data`.

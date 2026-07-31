---
name: Analyze a Morpho Vault and a user position
description: Find a Morpho Vault, read its allocation and APY, and look up a specific user's vault and market positions.
api: graphql/morpho-labs-schema-introspection.json
operations: [vaults, vaultByAddress, vaultPosition, marketPositions, userByAddress]
auth: none (public GraphQL API)
endpoint: https://api.morpho.org/graphql
---

# Analyze a Morpho Vault and a user position

Use the Morpho public GraphQL API to analyze Morpho Vaults and a user's onchain lending positions. No authentication is required; respect the 750 req/min and 1,000,000 complexity limits.

## Steps

1. **Find a vault.** Query `vaults(first, skip, where, orderBy, orderDirection)` filtering `where: { chainId_in, whitelisted }` and order by `orderBy: TotalAssetsUsd`. Or fetch a known vault with `vaultByAddress(address, chainId)`.
2. **Read vault economics.** From the vault read `asset`, `state { apy netApy totalAssetsUsd fee }`, `allocators`, and `state.allocation { market supplyAssetsUsd }` to see how deposits are spread across Morpho Blue markets.
3. **Resolve a user.** Call `userByAddress(address, chainId)` to confirm the account exists on the chain.
4. **Read the user's vault position.** Call `vaultPosition(userAddress, vaultAddress, chainId)` for shares, assets, and USD value.
5. **Read the user's market positions.** Call `marketPositions(first, where: { userAddress_in, chainId_in })` to enumerate supply/borrow/collateral across markets, including `healthFactor`.

## Rules

- Scope every query by `chainId`; positions are per-chain.
- Vault V2 has parallel fields — use `vaultV2ByAddress` / `vaultV2PositionByAddress` for V2 vaults.
- Watch the changelog for deprecated fields (e.g. legacy PnL and reward fields have been removed post-Merkl migration).
- Handle GraphQL `errors[]` and HTTP `429` with backoff.

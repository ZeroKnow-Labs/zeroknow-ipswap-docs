# Atomic IP Marketplace

[![CI](https://github.com/unixfundz/Atomic-IP-Marketplace/actions/workflows/ci.yml/badge.svg)](https://github.com/unixfundz/Atomic-IP-Marketplace/actions/workflows/ci.yml)

Soroban smart contracts for atomic IP swaps using USDC, IP registry, and ZK verification.

## Overview
- **`atomic_swap`**: Atomic swaps with USDC payments, pause functionality, buyer/seller indexing.
- **`ip_registry`**: Register and query IP assets with TTL.
- **`zk_verifier`**: Merkle tree ZK proof verification with TTL.

See [contracts/](/contracts/) for sources.

## Build & Test
```bash
./scripts/build.sh
./scripts/test.sh
```

## Deploy (Testnet)\n```bash\n./scripts/deploy_testnet.sh\n```\n\n## Frontend - Decryption Key Reveal (Issue #34)\n\nAfter swap completion, use the frontend to retrieve the decryption key:\n\n1. Deploy contracts (updates `.env` with `CONTRACT_ATOMIC_SWAP`)\n2. Open `frontend/index.html` in browser\n3. Input contract ID (from `.env`), your swap ID\n4. Fetch status/key (testnet RPC), copy key securely\n5. Optional demo IPFS decrypt stub\n\n**Note:** Pure JS demo with RPC stub; extend with @stellar/stellar-sdk for full RPC XDR.

## Security
[SECURITY.md](./SECURITY.md)

## License
TBD (add LICENSE file if needed).

---

*Workspace using Soroban SDK v22.0.0*

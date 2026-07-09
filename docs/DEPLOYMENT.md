# Deployment Guide - ZeroKnow IPSwap

## Overview

This guide covers deploying ZeroKnow IPSwap contracts to Stellar Testnet and Mainnet.

## Prerequisites

- Stellar CLI installed
- Rust toolchain with wasm32 target
- Testnet/Mainnet account with XLM
- Contract source code from [zeroknow-ipswap-contracts](https://github.com/ZeroKnow-Labs/zeroknow-ipswap-contracts)

## Testnet Deployment

### 1. Setup Environment

```bash
cp .env.example .env
```

Configure `.env`:

```env
STELLAR_NETWORK=testnet
STELLAR_RPC_URL=https://soroban-testnet.stellar.org
ATOMIC_SWAP_ADMIN=GXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
ATOMIC_SWAP_FEE_RECIPIENT=GYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY
ATOMIC_SWAP_FEE_BPS=500  # 5%
ATOMIC_SWAP_CANCEL_DELAY_SECS=3600  # 1 hour
```

### 2. Build Contracts

```bash
./scripts/build.sh
```

This compiles all contracts to WebAssembly.

### 3. Deploy Contracts

```bash
./scripts/deploy_testnet.sh
```

**Deployment Steps:**

1. Contract binary is uploaded to Stellar
2. Contract is instantiated on testnet
3. Admin configuration is applied
4. Contract IDs are saved to deployment config

**Output:**

```
atomic_swap contract ID: CXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
ip_registry contract ID: CYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY
zk_verifier contract ID: CZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZ
```

### 4. Verify Deployment

```bash
stellar contract invoke \
  --network testnet \
  --contract CXXXXXXXXXX \
  -- initialize \
  --admin GXXXXXXXXXX \
  --fee_recipient GYYYYYYY \
  --fee_bps 500
```

### 5. Configure Frontend

Update frontend `.env`:

```env
VITE_CONTRACT_ATOMIC_SWAP=CXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
VITE_CONTRACT_IP_REGISTRY=CYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY
VITE_CONTRACT_ZK_VERIFIER=CZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZ
VITE_STELLAR_NETWORK=testnet
VITE_STELLAR_RPC_URL=https://soroban-testnet.stellar.org
```

## Mainnet Deployment

### 1. Pre-Deployment Checklist

- [ ] Contracts tested on testnet
- [ ] Security audit completed
- [ ] Admin keys secured
- [ ] Fee recipient verified
- [ ] Rollback plan prepared

### 2. Setup Production Environment

```bash
STELLAR_NETWORK=mainnet
STELLAR_RPC_URL=https://soroban-mainnet.stellar.org
```

### 3. Deploy to Mainnet

```bash
./scripts/deploy_mainnet.sh
```

⚠️ **This uses real XLM. Verify all settings before proceeding.**

### 4. Post-Deployment Verification

```bash
# Check contract status
stellar contract info \
  --network mainnet \
  --contract CXXXXXXXXXX

# Test a call
stellar contract invoke \
  --network mainnet \
  --contract CXXXXXXXXXX \
  -- get_swap --swap_id 1
```

## Configuration

### Admin Functions

Initialize contracts with admin:

```bash
stellar contract invoke \
  --network mainnet \
  --contract CXXXXXXXXXX \
  -- initialize \
  --admin GXXXXXXXXXX \
  --fee_recipient GYYYYYYY \
  --fee_bps 500
```

### Fee Configuration

Update fee basis points (requires admin):

```bash
stellar contract invoke \
  --network mainnet \
  --contract CXXXXXXXXXX \
  -- update_fee_bps \
  --new_fee_bps 750
```

### Pause/Resume

Pause contract during emergencies:

```bash
stellar contract invoke \
  --network mainnet \
  --contract CXXXXXXXXXX \
  -- pause_contract
```

Resume:

```bash
stellar contract invoke \
  --network mainnet \
  --contract CXXXXXXXXXX \
  -- resume_contract
```

## Monitoring

### Check Contract State

```bash
stellar contract storage \
  --network mainnet \
  --contract CXXXXXXXXXX \
  --key-type contract-data
```

### View Events

```bash
stellar contract events \
  --network mainnet \
  --contract CXXXXXXXXXX \
  --type contract \
  --limit 10
```

### Verify Transactions

```bash
stellar tx \
  --network mainnet \
  --hash XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## Troubleshooting

### Deployment Fails

1. Check RPC connectivity:
   ```bash
   curl https://soroban-testnet.stellar.org/health
   ```

2. Verify account has XLM:
   ```bash
   stellar account info --network testnet --account GXXXXXXXXXX
   ```

3. Check contract bytecode:
   ```bash
   ./scripts/build.sh --check
   ```

### High Transaction Fees

- Try during low-congestion periods
- Batch multiple operations
- Use fee-optimized settings

### Contract Errors

Check error codes in [ERROR_RECOVERY.md](./ERROR_RECOVERY.md)

## Rollback Procedures

### Contract Upgrade

Deploy new contract version:

```bash
./scripts/deploy_testnet.sh --new-version
```

### Data Migration

Transfer data from old to new contract:

```bash
./scripts/migrate_data.sh \
  --source-contract COLD_CONTRACT_ID \
  --target-contract NEW_CONTRACT_ID
```

## Security Considerations

- ✅ Use hardware wallet for admin key
- ✅ Enable transaction signing delays
- ✅ Monitor contract activity
- ✅ Maintain deployment changelog
- ✅ Document all configuration changes
- ✅ Regular security audits

## Resources

- [Stellar CLI Documentation](https://developers.stellar.org/reference/cli)
- [Soroban Deployment](https://soroban.stellar.org/learn/deploying-contracts)
- [Smart Contract Security](https://developers.stellar.org/learn/security)

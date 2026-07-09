# Integration Testing Guide

## Overview

Comprehensive guide for testing ZeroKnow IPSwap across frontend, contracts, and network integration.

## Unit Testing

### Frontend Components

```bash
npm test -- SwapStatusDashboard.test.tsx
```

Test individual React components:

```typescript
import { render, screen } from '@testing-library/react';
import { SwapStatusDashboard } from '@/components/SwapStatusDashboard';

describe('SwapStatusDashboard', () => {
  it('renders swap status', () => {
    render(<SwapStatusDashboard />);
    expect(screen.getByText(/swap status/i)).toBeInTheDocument();
  });
});
```

### Contract Unit Tests

```bash
cargo test --package atomic_swap
cargo test --package ip_registry
cargo test --package zk_verifier
```

## Integration Testing

### End-to-End Workflow

Test complete swap flow:

1. **User Registration**
   ```typescript
   const wallet = await connectWallet();
   expect(wallet.publicKey).toBeDefined();
   ```

2. **IP Registration**
   ```typescript
   const listingId = await registerIP(ipfsHash, merkleRoot);
   expect(listingId).toBeGreaterThan(0);
   ```

3. **Initiate Swap**
   ```typescript
   const swapId = await initiateSwap({
     listingId,
     buyer: buyerAddress,
     seller: sellerAddress,
     amount: 1000000000,
   });
   ```

4. **Confirm Swap**
   ```typescript
   await confirmSwap(swapId, decryptionKey);
   const swap = await getSwap(swapId);
   expect(swap.status).toBe('completed');
   ```

## Testnet Testing

### Setup Testnet Account

```bash
# Generate keypair
stellar keys generate --network testnet

# Fund with testnet XLM (free)
# Visit https://laboratory.stellar.org/

# Verify funding
stellar account info --network testnet --account GXXXXXXXXXX
```

### Deploy to Testnet

```bash
./scripts/deploy_testnet.sh
```

### Run Integration Tests

```bash
STELLAR_NETWORK=testnet npm run test:integration
```

## Performance Testing

### Load Testing

Test contract under load:

```bash
ab -n 1000 -c 10 http://localhost:5173/api/swaps
```

### Network Latency Testing

Simulate network conditions:

```bash
# Linux: Use tc (traffic control)
sudo tc qdisc add dev eth0 root netem delay 200ms

# macOS: Use Network Link Conditioner
sudo defaults write /Library/Preferences/com.apple.networkd latency 200
```

### Polling Performance

Monitor polling efficiency:

```typescript
const metrics = {
  pollCount: 0,
  successRate: 0,
  avgResponseTime: 0,
};

pollingService.on('poll', (response) => {
  metrics.pollCount++;
  metrics.avgResponseTime = calculateAverage();
});
```

## Security Testing

### Input Validation

Test with invalid inputs:

```typescript
describe('Input Validation', () => {
  it('rejects negative amounts', () => {
    expect(() => {
      validateAmount(-100);
    }).toThrow();
  });

  it('rejects invalid addresses', () => {
    expect(() => {
      validateAddress('invalid');
    }).toThrow();
  });
});
```

### Contract Vulnerabilities

```bash
# Run security audit
cargo audit

# Check for common patterns
cargo clippy -- -D warnings
```

### ZK Proof Verification

```rust
#[test]
fn test_valid_proof_verification() {
    let proof = generate_test_proof();
    let result = verify_proof(&proof);
    assert!(result);
}

#[test]
fn test_invalid_proof_rejection() {
    let invalid_proof = generate_invalid_proof();
    let result = verify_proof(&invalid_proof);
    assert!(!result);
}
```

## Data Validation

### Test Different Scenarios

```typescript
const testCases = [
  { amount: 0, valid: false },
  { amount: 1, valid: true },
  { amount: 1000000000000, valid: true },
  { amount: -100, valid: false },
  { amount: 'abc', valid: false },
];

testCases.forEach(({ amount, valid }) => {
  it(`validates amount ${amount}`, () => {
    const result = validateAmount(amount);
    expect(result.isValid).toBe(valid);
  });
});
```

## Contract Testing

### Testbed Environment

```rust
use soroban_sdk::{Env, Symbol};

#[test]
fn test_initialize() {
    let env = Env::default();
    let contract_id = env.register_contract(None, Contract);
    let client = ContractClient::new(&env, &contract_id);
    
    let admin = Address::random(&env);
    let fee_recipient = Address::random(&env);
    
    client.initialize(&admin, &fee_recipient, &500);
    
    let config = client.get_config();
    assert_eq!(config.fee_bps, 500);
}
```

## Browser Testing

### Cross-Browser Compatibility

Test on:
- Chrome/Chromium
- Firefox
- Safari
- Edge

```bash
# Using Playwright
npm run test:browser
```

### Wallet Integration Testing

Test with different wallets:

- Freighter
- xBull
- Lobstr

```typescript
describe('Wallet Integration', () => {
  it('connects with Freighter', async () => {
    const wallet = await connectWallet('freighter');
    expect(wallet).toBeDefined();
  });
});
```

## Regression Testing

### Automated Regression Suite

```bash
npm run test:regression
```

Monitor for:
- UI layout changes
- Function regressions
- Performance degradation
- New bugs introduced

### Test Coverage

```bash
npm run test:coverage
```

Target: >80% code coverage

```
Statements   : 85% ( 42/50 )
Branches     : 75% ( 15/20 )
Functions    : 90% ( 18/20 )
Lines        : 85% ( 40/47 )
```

## Continuous Integration

### GitHub Actions

```yaml
name: Integration Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm install
      - run: npm run typecheck
      - run: npm run lint
      - run: npm run test
      - run: npm run build
```

## Test Checklist

Before release:

- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Contract tests pass
- [ ] No console errors/warnings
- [ ] Performance metrics acceptable
- [ ] Security audit passed
- [ ] Cross-browser tested
- [ ] Wallet compatibility verified
- [ ] Testnet deployment successful
- [ ] Load testing completed

## Resources

- [Testing Library Docs](https://testing-library.com/)
- [Jest Documentation](https://jestjs.io/)
- [Soroban Testing Guide](https://soroban.stellar.org/)
- [Stellar SDK Examples](https://developers.stellar.org/reference/sdk)

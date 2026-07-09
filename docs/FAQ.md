# Frequently Asked Questions (FAQ)

## General Questions

### What is ZeroKnow IPSwap?

ZeroKnow IPSwap is a decentralized platform for trading intellectual property assets on Stellar using atomic swaps with zero-knowledge proof verification. It enables trustless, instant settlement of IP ownership transfers with USDC payments.

### How does it work?

1. **IP Registration** - Asset owner registers IP on blockchain with IPFS metadata
2. **Listing Creation** - Owner lists IP asset with price
3. **Swap Initiation** - Buyer initiates swap, locks USDC payment
4. **Verification** - Seller submits ZK proof confirming asset validity
5. **Settlement** - Funds transfer, ownership transfers automatically

### Is it audited?

The smart contracts undergo regular security reviews. For production use, a formal third-party audit is recommended. Check latest security report in [SECURITY.md](./SECURITY.md).

### What networks does it support?

Currently supported:
- **Testnet** - Development and testing
- **Mainnet** - Production (when ready)

## Technical Questions

### What tokens can be used?

Currently: **USDC only** on Stellar

Future versions may support additional tokens.

### What are the fees?

Fees are configurable but default to **5% (500 basis points)**. Fees go to designated fee recipient address.

### How long do swaps take?

- **Initiation** - 5-10 seconds
- **Confirmation** - Dependent on seller action
- **Settlement** - Automatic after confirmation

### What happens if something goes wrong?

Swaps have built-in safeguards:
- **Automatic cancellation** - If seller doesn't confirm within 1 hour (configurable)
- **Dispute window** - 24 hours after swap creation
- **Rollback capability** - Funds returned if any step fails

See [ERROR_RECOVERY.md](./SECURITY.md#error-recovery) for details.

### Can swaps be reversed?

Only in specific conditions:
- If seller cancels before confirmation
- If buyer initiates cancellation after delay
- If ZK proof fails verification
- During dispute window

No reversal possible after settlement.

## Wallet & Account Questions

### Which wallets are supported?

- Freighter (browser extension)
- xBull (browser extension)
- Lobstr (mobile app)

Others with Stellar integration may work.

### Do I need testnet account?

Yes, for development/testing:
- Get testnet XLM: https://laboratory.stellar.org/
- Free and unlimited

### How do I get testnet XLM?

```
1. Go to https://laboratory.stellar.org/
2. Click "Get testnet XLM"
3. Enter your account address
4. You'll receive 10,000 testnet XLM
```

### What's the minimum balance?

- **Stellar minimum** - 2 XLM (Stellar requirement)
- **Gas costs** - Typically < 1 XLM per transaction
- **USDC** - Amount needed for swaps

### Can I use mainnet with real money?

Yes, but be cautious:
- Mainnet uses real money
- Always test on testnet first
- Verify all details before confirming
- Use small amounts initially

## Smart Contract Questions

### What are the three contracts?

1. **atomic_swap** - Handles swaps and settlements
2. **ip_registry** - Stores IP asset listings
3. **zk_verifier** - Verifies zero-knowledge proofs

### Can I call contracts directly?

Yes, contracts are public and can be called directly using Stellar SDK or CLI:

```bash
stellar contract invoke \
  --contract CXXXXXXXXXX \
  -- method_name \
  --param value
```

### How are contract IDs determined?

Contract IDs are generated when contracts are deployed. They're fixed and cannot be changed.

### What if I lose my admin key?

If admin key is lost:
- Contract cannot be modified
- Consider deployment as permanent
- Plan accordingly for upgrades

### How do I upgrade contracts?

New versions must be deployed as separate contracts. Data migration requires scripting.

## Development Questions

### How do I set up locally?

```bash
git clone https://github.com/ZeroKnow-Labs/zeroknow-ipswap-frontend.git
cd zeroknow-ipswap-frontend
npm install
cp .env.example .env
npm run dev
```

See [DEVELOPMENT.md](./../../DEVELOPMENT.md) for details.

### What's the tech stack?

**Frontend:**
- React 18 + TypeScript
- Vite (build tool)
- Stellar SDK
- Tailwind CSS

**Contracts:**
- Rust
- Soroban SDK
- Stellar contracts

### How do I run tests?

```bash
# Frontend
npm run test

# Contracts
cargo test --workspace

# Integration
npm run test:integration
```

### Where's the documentation?

- [README.md](../README.md) - Overview
- [DEVELOPMENT.md](../../DEVELOPMENT.md) - Setup guide
- [QUICK_START.md](./QUICK_START.md) - Quick start
- [API_REFERENCE.md](./API_REFERENCE.md) - API docs
- [DEPLOYMENT.md](./DEPLOYMENT.md) - Deployment guide
- [TESTING.md](./TESTING.md) - Testing guide
- [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) - Troubleshooting

### How do I contribute?

See [CONTRIBUTING.md](../../CONTRIBUTING.md) for guidelines.

## Security Questions

### Is my private key stored?

No. Private keys stay in your wallet. App never has access.

### How are funds protected?

- Atomic swaps ensure payment and transfer happen together
- Funds in escrow during swap process
- ZK proofs verify asset before release

### What if I encounter a vulnerability?

Report to: security@zeroknow-labs.com

See [SECURITY.md](./SECURITY.md) for full policy.

## Deployment Questions

### How do I deploy to testnet?

```bash
./scripts/deploy_testnet.sh
```

See [DEPLOYMENT.md](./DEPLOYMENT.md) for details.

### How do I deploy to mainnet?

```bash
./scripts/deploy_mainnet.sh
```

⚠️ Use real funds. Verify all settings first.

### Can I rollback deployment?

Rollback requires deploying a new contract version. Old data must be migrated.

### How do I monitor deployment?

```bash
stellar contract events --network testnet --contract CXXXXXXXXXX
```

## Billing & Costs

### Are there transaction fees?

Yes:
- **Stellar base fee** - ~0.00001 XLM per operation
- **Swap fee** - 5% of swap amount (configurable)
- **Network congestion** - May increase base fee

### Do I pay in XLM or USDC?

- **Gas** - Paid in XLM (Stellar requirement)
- **Asset transfers** - In USDC

### What's most expensive?

1. Swap initiation (2-3 operations)
2. Confirmation (3-4 operations)
3. Cancellation (1 operation)

### How can I minimize costs?

- Batch operations
- Swap during low-congestion times
- Use testnet for testing

## Troubleshooting

### Still have questions?

Check:
1. [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) - Common issues
2. [GitHub Issues](https://github.com/ZeroKnow-Labs) - Known issues
3. Community channels - Get help from others

### How do I report a bug?

1. Search existing issues
2. Include error message and steps to reproduce
3. Post on GitHub Issues
4. For security issues: security@zeroknow-labs.com

## Contact & Community

### Where can I get help?

- **GitHub Issues** - Bug reports & feature requests
- **Discussions** - General questions
- **Discord** - Community chat
- **Email** - security@zeroknow-labs.com

### Can I fork and modify?

Yes! Licensed under Apache 2.0. See [LICENSE](../LICENSE) for terms.

### How do I stay updated?

- Watch the GitHub repository
- Follow ZeroKnow Labs on Twitter
- Join our Discord community
- Subscribe to mailing list

### How do I get involved?

- Contribute code - see [CONTRIBUTING.md](../../CONTRIBUTING.md)
- Report issues - GitHub Issues
- Provide feedback - GitHub Discussions
- Write documentation
- Test on testnet and report findings

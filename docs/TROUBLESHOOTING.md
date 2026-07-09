# Troubleshooting Guide

## Common Issues & Solutions

### Frontend Issues

#### Port Already in Use

**Error:** `EADDRINUSE: address already in use :::5173`

**Solutions:**
```bash
# Find and kill process using port 5173
lsof -i :5173
kill -9 <PID>

# Or use different port
npm run dev -- --port 3000
```

#### Module Not Found

**Error:** `Cannot find module '@/components/SwapCard'`

**Solutions:**
1. Clear node_modules:
   ```bash
   rm -rf node_modules package-lock.json
   npm install
   ```

2. Check import path is correct
3. Verify file exists and named correctly

#### TypeScript Errors

**Error:** `Property 'xyz' does not exist on type 'Listing'`

**Solutions:**
```bash
# Check types are correct
npm run typecheck

# Update type definitions
npm install --save-dev @types/stellar-sdk@latest
```

#### Wallet Connection Issues

**Error:** `Wallet connection failed`

**Solutions:**
1. Ensure wallet extension is installed
2. Check wallet is unlocked
3. Verify network is testnet
4. Clear browser cache and cookies
5. Try different wallet extension

#### Hot Module Reload Not Working

**Error:** Changes don't reflect after save

**Solutions:**
```bash
# Restart dev server
npm run dev

# Check vite.config.ts has correct settings
# Force hard refresh in browser (Ctrl+Shift+R)
```

### Contract Issues

#### Contract Deployment Fails

**Error:** `Failed to submit transaction`

**Solutions:**
1. Verify account has XLM:
   ```bash
   stellar account info --network testnet --account GXXXXXXXXXX
   ```

2. Check RPC URL is accessible:
   ```bash
   curl https://soroban-testnet.stellar.org/health
   ```

3. Ensure contract bytecode is valid:
   ```bash
   ./scripts/build.sh
   ```

#### Insufficient Balance

**Error:** `Account balance too low for operation`

**Solutions:**
- Get testnet XLM: https://laboratory.stellar.org/
- Use a funded testnet account
- Check account balance:
  ```bash
  stellar account info --network testnet --account GXXXXXXXXXX
  ```

#### Invalid Contract ID

**Error:** `Contract ID not found`

**Solutions:**
1. Verify contract ID format (starts with 'C')
2. Check correct network (testnet/mainnet)
3. Verify contract was deployed:
   ```bash
   stellar contract info --network testnet --contract CXXXXXXXXXX
   ```

#### Contract Call Timeout

**Error:** `RPC timeout after 30s`

**Solutions:**
1. Check network connectivity
2. Try again - network might be congested
3. Reduce polling frequency
4. Check RPC endpoint is healthy:
   ```bash
   curl https://soroban-testnet.stellar.org/health
   ```

### Integration Issues

#### Swap Initiation Fails

**Error:** `swap initialization failed`

**Possible Causes:**
1. Insufficient USDC allowance
   - Solution: Approve USDC first
   ```typescript
   await approveUSDC(contractAddress, amount);
   ```

2. Listing not found
   - Solution: Verify listing exists
   ```bash
   stellar contract invoke --contract REGISTRY_ID -- get_listing --id 1
   ```

3. Seller address mismatch
   - Solution: Verify listing owner matches seller parameter

#### Confirmation Fails

**Error:** `swap confirmation failed - invalid proof`

**Solutions:**
1. Regenerate ZK proof:
   ```bash
   ./scripts/generate_proof.sh --swap-id 1
   ```

2. Verify proof path:
   ```rust
   let proof = generate_merkle_proof(leaf, tree);
   let valid = verify_proof(&proof);
   ```

3. Check Merkle root is current:
   ```bash
   stellar contract invoke --contract VERIFIER_ID -- get_merkle_root --id 1
   ```

#### Fee Calculation Incorrect

**Error:** `Fee would truncate - amount too small`

**Solutions:**
1. Increase swap amount
2. Reduce fee percentage
3. Check fee basis points:
   ```bash
   stellar contract invoke --contract ATOMIC_ID -- get_config
   ```

### Network Issues

#### RPC Connection Timeout

**Error:** `Connection timeout to RPC endpoint`

**Solutions:**
1. Check RPC URL is correct
2. Verify internet connection
3. Try alternative RPC endpoint:
   - Testnet: `https://soroban-testnet.stellar.org`
   - Mainnet: `https://soroban-mainnet.stellar.org`
4. Check RPC status page

#### High Transaction Fees

**Error:** High fees charged for transaction

**Solutions:**
1. Wait for less congested time
2. Check gas calculation
3. Reduce operation complexity
4. Monitor fee trends

#### Network Congestion

**Error:** Transactions taking very long

**Solutions:**
1. Increase timeout duration
2. Reduce polling frequency
3. Wait and retry
4. Check network status dashboard

### Data Issues

#### Listing Not Appearing

**Error:** Registered listing doesn't show up

**Solutions:**
1. Verify listing was registered:
   ```bash
   stellar contract invoke --contract REGISTRY_ID -- get_listing --id 1
   ```

2. Check listing hasn't expired:
   ```typescript
   const listing = await getListing(id);
   if (listing.expires_at < currentTime) {
     // Listing expired
   }
   ```

3. Query might be cached - refresh browser

#### Swap Status Not Updating

**Error:** Swap status shows "pending" but should be completed

**Solutions:**
1. Check polling is running
2. Increase polling frequency (in config)
3. Manually fetch swap status:
   ```bash
   stellar contract invoke --contract ATOMIC_ID -- get_swap --id 1
   ```

4. Check confirmation was submitted

### Browser Compatibility

#### Feature Not Working in Safari

**Error:** Feature works in Chrome but not Safari

**Solutions:**
1. Check browser compatibility
2. Use polyfills if needed
3. Test with latest Safari version
4. Check for BigInt support (needed for large numbers)

#### Mobile Wallet Connection

**Error:** Wallet doesn't connect on mobile

**Solutions:**
1. Use mobile wallet app (Lobstr)
2. Use mobile browser with wallet support
3. Some features may not work on mobile

### Performance Issues

#### App Loads Slowly

**Error:** Initial page load takes > 5 seconds

**Solutions:**
1. Check bundle size:
   ```bash
   npm run build -- --analyze
   ```

2. Enable caching:
   - Browser cache should be enabled
   - Check network tab in DevTools

3. Reduce initial data:
   - Implement pagination
   - Lazy load components

#### Polling Too Frequent

**Error:** RPC calls every second, too much load

**Solutions:**
1. Increase polling interval:
   ```typescript
   const minInterval = 5000; // 5 seconds
   ```

2. Only poll pending swaps
3. Reduce number of concurrent polls

### Getting Help

#### Debug Information to Collect

Before reporting issue:

1. **Browser & Version:**
   ```javascript
   navigator.userAgent
   ```

2. **Console Errors:**
   - Open DevTools (F12)
   - Go to Console tab
   - Copy error messages

3. **Network Errors:**
   - DevTools → Network tab
   - Check failed requests

4. **Contract State:**
   ```bash
   stellar contract storage --contract CXXXXXXXXX
   ```

5. **Environment Variables:**
   - Verify .env is correct
   - Check RPC URL is valid

#### Reporting Issues

Include in bug report:
- [ ] Exact error message
- [ ] Steps to reproduce
- [ ] Browser and OS
- [ ] Network (testnet/mainnet)
- [ ] Console errors/warnings
- [ ] Recent changes made

## Additional Resources

- [Stellar Developer Docs](https://developers.stellar.org/)
- [Soroban Documentation](https://soroban.stellar.org/)
- [GitHub Issues](https://github.com/ZeroKnow-Labs/zeroknow-ipswap-frontend/issues)
- [Community Discord](https://discord.gg/stellar)

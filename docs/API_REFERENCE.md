# API Reference - ZeroKnow IPSwap Contracts

## Overview

Complete API reference for ZeroKnow IPSwap Soroban contracts.

## atomic_swap Contract

### initialize

Initialize the atomic swap contract with configuration.

```rust
pub fn initialize(
    env: Env,
    admin: Address,
    fee_recipient: Address,
    fee_bps: u32,
) -> Result<(), ContractError>
```

**Parameters:**
- `admin`: Admin address for contract management
- `fee_recipient`: Address to receive swap fees
- `fee_bps`: Fee in basis points (0-10000)

**Returns:** Result with success or error

**Errors:**
- `FeeBpsTooHigh`: Fee exceeds 100% (10000 bps)

### initiate_swap

Initiate a new IP asset swap.

```rust
pub fn initiate_swap(
    env: Env,
    listing_id: u64,
    buyer: Address,
    seller: Address,
    usdc_token: Address,
    amount: i128,
) -> Result<u64, ContractError>
```

**Parameters:**
- `listing_id`: IP asset listing ID
- `buyer`: Buyer wallet address
- `seller`: Seller wallet address
- `usdc_token`: USDC token contract address
- `amount`: Swap amount in stroops

**Returns:** Swap ID on success

**Errors:**
- `UnderpaymentNotAllowed`: Payment < listing price
- `InsufficientAllowance`: Insufficient USDC approved
- `SellerMismatch`: Seller doesn't own listing
- `InvalidToken`: Token not whitelisted

### confirm_swap

Confirm swap and complete transaction.

```rust
pub fn confirm_swap(
    env: Env,
    swap_id: u64,
    decryption_key: Bytes,
) -> Result<(), ContractError>
```

**Parameters:**
- `swap_id`: ID of swap to confirm
- `decryption_key`: ZK proof for asset decryption

**Returns:** Result on success or error

**Errors:**
- `InvalidProof`: ZK proof verification failed
- `SwapNotFound`: Swap ID doesn't exist
- `InvalidStatus`: Swap not in Pending status

### cancel_swap

Cancel pending swap after delay.

```rust
pub fn cancel_swap(
    env: Env,
    swap_id: u64,
) -> Result<(), ContractError>
```

**Parameters:**
- `swap_id`: ID of swap to cancel

**Returns:** Result on success or error

**Errors:**
- `CancelTooEarly`: Cancel delay not elapsed
- `InvalidStatus`: Swap already completed

### get_swap

Get swap status and details.

```rust
pub fn get_swap(env: Env, swap_id: u64) -> Result<Swap, ContractError>
```

**Parameters:**
- `swap_id`: Swap ID to retrieve

**Returns:** Swap struct with current state

**Swap Struct:**
```rust
pub struct Swap {
    pub id: u64,
    pub listing_id: u64,
    pub buyer: Address,
    pub seller: Address,
    pub amount: i128,
    pub fee: i128,
    pub status: SwapStatus,
    pub created_at: u64,
    pub expires_at: u64,
}
```

## ip_registry Contract

### register_ip

Register new intellectual property asset.

```rust
pub fn register_ip(
    env: Env,
    owner: Address,
    ipfs_hash: BytesN<32>,
    merkle_root: BytesN<32>,
) -> Result<u64, ContractError>
```

**Parameters:**
- `owner`: IP owner address
- `ipfs_hash`: IPFS hash of asset metadata
- `merkle_root`: Merkle root for ZK proofs

**Returns:** Listing ID on success

### get_listing

Retrieve IP asset listing details.

```rust
pub fn get_listing(env: Env, listing_id: u64) -> Result<Listing, ContractError>
```

**Returns:** Listing struct with current data

**Listing Struct:**
```rust
pub struct Listing {
    pub id: u64,
    pub owner: Address,
    pub ipfs_hash: BytesN<32>,
    pub price_usdc: i128,
    pub created_at: u64,
    pub expires_at: u64,
}
```

## zk_verifier Contract

### verify_proof

Verify zero-knowledge proof.

```rust
pub fn verify_proof(
    env: Env,
    listing_id: u64,
    leaf: BytesN<32>,
    path: Vec<BytesN<32>>,
) -> Result<bool, ContractError>
```

**Parameters:**
- `listing_id`: IP asset listing ID
- `leaf`: Merkle tree leaf to verify
- `path`: Merkle proof path

**Returns:** True if proof is valid, false otherwise

### set_merkle_root

Set Merkle root for a listing.

```rust
pub fn set_merkle_root(
    env: Env,
    owner: Address,
    listing_id: u64,
    root: BytesN<32>,
) -> Result<(), ContractError>
```

**Parameters:**
- `owner`: Listing owner address
- `listing_id`: Listing ID
- `root`: New Merkle root

**Returns:** Result on success or error

## Error Codes

```rust
pub enum ContractError {
    InsufficientAllowance = 24,
    InvalidToken = 20,
    UnderpaymentNotAllowed = 14,
    FeeBpsTooHigh = 21,
    InvalidProof = 16,
    CancelTooEarly = 18,
    DisputeWindowActive = 19,
    SellerMismatch = 9,
    Overflow = 23,
    FeeWouldTruncate = 15,
}
```

## Types

### SwapStatus

```rust
pub enum SwapStatus {
    Pending = 0,
    Completed = 1,
    Cancelled = 2,
    Disputed = 3,
}
```

## Events

Contracts emit diagnostic events visible in Soroban logs:

- `SwapInitiated` - Swap created
- `SwapConfirmed` - Swap completed
- `SwapCancelled` - Swap cancelled
- `IPRegistered` - IP asset registered
- `ProofVerified` - ZK proof verified

## Rate Limits

- Max 1000 active swaps per user
- Polling interval minimum: 5 seconds
- RPC timeout: 30 seconds

## Resources

- [Full Contracts Source](https://github.com/ZeroKnow-Labs/zeroknow-ipswap-contracts)
- [Stellar SDK Docs](https://developers.stellar.org/learn)
- [Soroban Documentation](https://soroban.stellar.org/)

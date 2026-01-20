# NFT Staking Smart Contract

## Overview

This repository contains a **gas-optimized NFT staking smart contract** that supports **ERC721 and ERC1155 NFTs** across multiple pools.
Users can stake their NFTs to earn ERC20 rewards over time, while the admin can configure reward rates, pools, and limits.

The contract is designed to be:

* Secure
* Flexible
* Upgrade-friendly (reward logic abstracted)
* Easy to integrate with marketplaces and dashboards

---

## Key Features

* ✅ Supports **ERC721 and ERC1155**
* ✅ Multiple staking pools
* ✅ Time-based reward calculation
* ✅ Dynamic reward rate updates
* ✅ Emergency withdrawals
* ✅ Gas-optimized reward accounting
* ✅ Reentrancy protection
* ✅ External ERC20 reward token support

---

## Contract Architecture

### Core Contract

* **`Staking.sol`** (abstract)

  * Handles all staking logic
  * Tracks user stakes and rewards
  * Calculates rewards
  * Exposes admin and user functions
  * Delegates reward distribution to child contract

### Reward Implementation

* Implemented in a child contract: **`NEOstake.sol`**
* Uses an **ERC20 token** for rewards
* Rewards are distributed from a **treasury wallet**

---

## Pools & Reward System

### Pools

* Each pool represents a staking category (e.g. Basic, Standard, Premium)
* Pools are identified by a `poolId`
* Each pool maintains its own:

  * Total staked NFTs
  * Reward conditions
  * User staking data

### Reward Conditions

Admin can define reward rules per pool:

* `timeUnit` → Time interval (in seconds)
* `rewardPerUnit` → Reward per NFT per time unit

**Example:**

* Time unit: 1 day
* Reward per unit: 0.4 tokens per NFT per day

Reward conditions can be updated at any time without affecting rewards already earned by users.

---

## Reward Token & Treasury Management (Admin)

Rewards are paid using an **ERC20 token**, managed through a treasury system.

### Reward Token Setup

* The reward token address is set **once during deployment**
* The contract does **not mint tokens**
* Rewards are transferred from a treasury wallet

```solidity
constructor(address _treasury, address _rewardToken)
```

---

### Treasury Wallet

* Holds all reward tokens
* Must approve the staking contract to spend tokens
* Can be updated by the admin if needed

**Update treasury wallet:**

```solidity
setTreasuryWallet(address newTreasury)
```

---

### Funding Rewards

Admin must fund the treasury before users can claim rewards.

**Steps:**

1. Admin holds ERC20 reward tokens
2. Admin approves the staking contract
3. Admin deposits rewards into the treasury

```solidity
depositRewards(uint256 amount)
```

**Important:**

* If the treasury has insufficient balance, reward claims will fail
* Admin is responsible for keeping the treasury funded

---

### Reward Distribution Flow

1. User stakes NFTs
2. Rewards accumulate over time
3. User claims rewards
4. Contract transfers ERC20 tokens from treasury to user

```solidity
_mintRewards(address to, uint256 amount)
```

---

## Admin Capabilities

Only the contract owner can:

* Create new staking pools
* Set or update reward rates
* Change reward time units
* Set maximum NFTs per transaction
* Fund reward treasury
* Update treasury wallet

Admin **cannot**:

* Withdraw user NFTs
* Claim user rewards
* Modify already earned rewards

---

## User Capabilities

Users can:

* Stake ERC721 NFTs (multiple token IDs)
* Stake ERC1155 NFTs (specific amounts)
* Claim accumulated rewards
* Withdraw staked NFTs
* Emergency withdraw (without rewards)

---

## Reward Calculation (How It Works)

1. Rewards are calculated **per NFT**
2. Rewards depend on:

   * Number of NFTs staked
   * Time elapsed
   * Active reward condition
3. Rewards are updated:

   * On stake
   * On withdraw
   * On reward claim
4. Earned rewards are stored until claimed

---

## Emergency Withdraw

If needed, users can:

* Withdraw their NFTs immediately
* Skip reward calculation
* Protect assets in emergency situations

This feature improves trust and user safety.

---

## Security Measures

* Reentrancy protection (`ReentrancyGuard`)
* Ownership-based access control
* Explicit token type validation
* Safe transfer handling
* No looping over other users’ data
* No admin access to user assets

---

## Supported Standards

* ERC721
* ERC1155
* ERC20 (for rewards)

---

## License

MIT License

---

## Disclaimer

This smart contract is provided as-is.
A professional audit is recommended before deploying to production.

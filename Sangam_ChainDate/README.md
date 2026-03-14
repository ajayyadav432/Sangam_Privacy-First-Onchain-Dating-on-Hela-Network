# Sangam 💜 — Privacy-First Onchain Dating on Hela Network

> **HackJKLU v5.0 · Hela Labs Track**  
> A fully on-chain, ZK-private dating dApp. Swipe, match, and connect — without exposing your identity.

[![HackJKLU v5.0](https://img.shields.io/badge/HackJKLU-v5.0-blueviolet?style=for-the-badge)](https://hackjklu.com)
[![Hela Network](https://img.shields.io/badge/Hela_Network-Testnet-00d4ff?style=for-the-badge)](https://helachain.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

## 🚀 Live App

**[https://chain-date.vercel.app](https://chain-date.vercel.app)**

> Connect MetaMask on **Hela Official Runtime Testnet** (Chain ID: 666888) to interact with the app.

## 🎥 Demo Video

> **[▶ Watch the 5-minute Demo](https://youtu.be/8_Tz6OEL0Ws)**  
> *(Updated with the live YouTube recording)*

---

## Architecture

```
JK/
├── blockchain/          # Hardhat + Solidity smart contracts
│   ├── contracts/
│   │   ├── DatingCore.sol       ← Profile reg, swipes, fees, matching
│   │   ├── EscrowContent.sol    ← Exclusive content escrow unlock
│   │   └── MockZKVerifier.sol   ← ZK proof mock (swap-ready)
│   ├── circuits/
│   │   └── matchVerifier.circom ← Circom ZK circuit (age + interests)
│   ├── scripts/deploy.ts
│   └── test/DatingCore.test.ts
└── frontend/            # Next.js 14 + Tailwind CSS
    ├── app/             # App router pages
    ├── components/      # SwipeCard, WalletConnect, ProfileForm, etc.
    └── lib/             # zk.ts, encryption.ts, contracts.ts
```

## Tech Stack

| Layer | Tech |
|---|---|
| Smart Contracts | Solidity 0.8.20, Hardhat, ethers.js |
| ZK Privacy | Circom circuit + MockZKVerifier (SnarkJS-compatible interface) |
| Frontend | Next.js 14 (App Router), Tailwind CSS, ethers.js |
| Wallet | MetaMask / any EVM wallet via `window.ethereum` |
| Network | **Hela Network** (EVM-compatible, chainId: 666888 testnet) |
| Encryption | Web Crypto API (AES-CBC client-side field encryption) |

## Key Features

- 🔐 **ZK Privacy**: Age + interest overlap proven via zero-knowledge proof. Identity never exposed on-chain.
- ⛓️ **Fully Onchain**: Every swipe, match, and message is a smart contract transaction.
- 💸 **Micro-Economy**: 0.001 HELA per swipe, 0.0005 HELA per message (pay-per-use model).
- 🔒 **Encrypted Profiles**: Name/bio AES-encrypted client-side; only hash stored on-chain.
- 🎁 **Exclusive Content Escrow**: Creators lock content; buyers unlock via `EscrowContent.sol`.

---

## Quick Start

### 1. Blockchain (Smart Contracts)

```bash
cd blockchain
npm install
cp .env.example .env   # fill in PRIVATE_KEY

# Local development
npx hardhat node                              # start local Hardhat node
npx hardhat run scripts/deploy.ts --network localhost

# Deploy to Hela Testnet
npx hardhat run scripts/deploy.ts --network helaTestnet

# Run tests
npx hardhat test
```

### 2. Frontend

```bash
cd frontend
npm install
# Add deployed contract addresses to .env.local:
# NEXT_PUBLIC_DATING_CORE=0x...
# NEXT_PUBLIC_ESCROW_CONTENT=0x...
# NEXT_PUBLIC_ZK_VERIFIER=0x...

npm run dev        # http://localhost:3000
```

---

## Smart Contract Summary

### `DatingCore.sol`
| Function | Fee | Description |
|---|---|---|
| `registerProfile(hash, interests)` | free | Store encrypted profile hash |
| `swipe(target, liked, proof, signals)` | 0.001 HELA | Like/pass with ZK verification |
| `sendMessage(recipient)` | 0.0005 HELA | Pay-per-message (80% to recipient) |
| `storeChatKey(partner, encKey)` | free | Store encrypted E2E chat key |

### `EscrowContent.sol`
| Function | Description |
|---|---|
| `listContent(hash, price)` | Creator lists exclusive content |
| `unlockContent(listingId)` | Buyer pays to unlock (2.5% protocol fee) |
| `cancelListing(listingId)` | Creator reclaims after 7-day lock |

### `MockZKVerifier.sol`
Mirrors the interface of a SnarkJS Groth16 verifier — swap for real ZK verifier in production with zero changes to callers.

---

## ZK Proof Flow

```
User                         ZKP Layer                    Smart Contract
  |                              |                               |
  |--- private inputs ---------> |                               |
  |    (age, interests)          |                               |
  |                              |--- generateMockProof() ----> |
  |                              |    ageValid=1                 |
  |                              |    interestOverlap=N          |
  |                              |                               |
  |<-- proofBytes + signals ---- |                               |
  |                              |                               |
  |--- swipe(proof, signals) -----------------------> DatingCore |
                                                      verifyProof()
                                                      → match logic
```

---

## 📋 Deployment Addresses (Hela Testnet — Chain ID: 666888)

| Contract | Address | Explorer |
|---|---|---|
| `MockZKVerifier` | `0x88e3515B1925b1E19223E99758340C607baeD473` | [View ↗](https://testnet.helascan.io/address/0x88e3515B1925b1E19223E99758340C607baeD473) |
| `DatingCore` | `0x7D722A548862AfC52d8FAec245876218c787d04F` | [View ↗](https://testnet.helascan.io/address/0x7D722A548862AfC52d8FAec245876218c787d04F) |
| `EscrowContent` | `0xf6E635A4400a3faac846D955Bb5d25A69A2FaD0C` | [View ↗](https://testnet.helascan.io/address/0xf6E635A4400a3faac846D955Bb5d25A69A2FaD0C) |

**Deployer:** `0x84d512aD93EA1E954a4A74C5DdE03D4a254Cdc7B`

---

## 🔗 Example Transaction Hashes

| # | Description | Transaction |
|---|---|---|
| 1 | `MockZKVerifier` deploy | [`0xc41478b5...`](https://testnet.helascan.io/tx/0xc41478b51ddbdf4b15307285e6aeffd47247e4a7a906c4b9ae5ef38c47953c98) |
| 2 | `DatingCore` deploy | [`0x0bde33d7...`](https://testnet.helascan.io/tx/0x0bde33d71c88c8113b1d5c16f04cf565436d34f41c2a41820f39ce389b9bcb32) |
| 3 | `EscrowContent` deploy | [`0xf15cfb6d...`](https://testnet.helascan.io/tx/0xf15cfb6d0fea189a0e5f6820e37c8e65ceec0e1128d8a20a06b07d0a8f64352e) |

---

## Hela Network Configuration

| Config | Value |
|---|---|
| Network Name | Hela Testnet |
| Chain ID | 666888 |
| RPC URL | `https://testnet-rpc.helachain.com` |
| Explorer | `https://testnet.helascan.io` |
| Currency | HELA |

---

*Built with 💜 for HackJKLU v5.0 by Team Sangam*


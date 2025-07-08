# Foundry Deployment and Interaction Guide

A comprehensive guide for deploying and interacting with smart contracts using Foundry tools.

## Table of Contents

1. [Base Conversions](#base-conversions)
2. [Wallet Management](#wallet-management)
3. [Contract Deployment](#contract-deployment)
4. [Contract Interaction](#contract-interaction)
5. [Testnet Deployment](#testnet-deployment)
6. [Code Formatting](#code-formatting)

---

## Base Conversions

### Converting Between Number Bases

Use Cast for converting between different number bases:

```bash
cast --to-base 0xac458 dec
```

This command converts the hexadecimal value `0xac458` to decimal format, revealing its original value.

---

## Wallet Management

### Importing Private Keys Securely

Instead of using `.env` files for private keys, use Cast's secure wallet import feature:

```bash
cast wallet import defaultKey --interactive
```

**Steps:**
1. Run the command above
2. Enter your private key when prompted
3. Set a secure password for the wallet

### Verify Wallet Import

Check your imported wallets:

```bash
cast wallet list
```

---

## Contract Deployment

### Method 1: Direct Private Key (Most Risky - Not Recommended)

**⚠️ Warning: This method exposes your private key in command history**

```bash
forge script script/DeploySimpleStorage.s.sol \
  --rpc-url http://127.0.0.1:8545 \
  --broadcast \
  --private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
```

### Method 2: Using Environment Variables (Risky)

**⚠️ Warning: Risk of exposure via GitHub if `.env` file is committed**

1. Create a `.env` file in your project root:
   ```env
   RPC_URL=http://127.0.0.1:8545
   PRIVATE_KEY=0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
   ```

2. Deploy using environment variables:
   ```bash
   forge script script/DeploySimpleStorage.s.sol \
     --rpc-url $RPC_URL \
     --broadcast \
     --private-key $PRIVATE_KEY
   ```

### Method 3: Best Practice (Recommended)

**✅ Secure method using imported wallet**

1. First, complete the [Wallet Management](#wallet-management) setup
2. Deploy using the imported wallet:
   ```bash
   forge script script/DeploySimpleStorage.s.sol \
     --rpc-url $RPC_URL \
     --account defaultKey \
     --sender $PUBLIC_KEY \
     --broadcast \
     -vvvv
   ```

**Notes:**
- You can store `RPC_URL` and `PUBLIC_KEY` in `.env` file (these are safe to expose)
- The command will prompt for the password you set in step 2
- `-vvvv` provides verbose output for debugging

---

## Contract Interaction

### Sending Transactions

#### Using Private Key
```bash
cast send $CONTRACT_ADDRESS "store(uint256)" 123 \
  --rpc-url $RPC_URL \
  --private-key $PRIVATE_KEY
```

#### Using Imported Wallet (Recommended)
```bash
cast send $CONTRACT_ADDRESS "store(uint256)" 123 \
  --rpc-url $RPC_URL \
  --account defaultKey
```

### Calling View Functions

For read-only function calls:

```bash
cast call $CONTRACT_ADDRESS "retrieve()"
```

**Note:** View functions don't require authentication or gas fees.

---

## Testnet Deployment

### Using Alchemy as Node Service Provider

For deploying to Sepolia testnet instead of local Anvil:

1. Set up your Sepolia environment variables:
   ```env
   SEPOLIA_RPC_URL=https://eth-sepolia.g.alchemy.com/v2/YOUR_API_KEY
   SEPOLIA_PRIVATE_KEY=your_sepolia_private_key
   ```

2. Deploy to Sepolia:
   ```bash
   forge script script/DeploySimpleStorage.s.sol \
     --rpc-url $SEPOLIA_RPC_URL \
     --private-key $SEPOLIA_PRIVATE_KEY \
     --broadcast
   ```

**Requirements:**
- Alchemy account and API key
- Sepolia testnet ETH in your wallet
- Sepolia private key

---

## Code Formatting

### Built-in Formatter

Foundry includes a powerful built-in formatter:

```bash
forge fmt
```

**Features:**
- Automatically formats all Solidity files in your project
- Enforces consistent code style
- Very useful for maintaining clean, readable code

---

## Environment Variables Reference

### Common Variables

```env
# Local Development
RPC_URL=http://127.0.0.1:8545
PUBLIC_KEY=0xYourPublicKeyHere

# Sepolia Testnet
SEPOLIA_RPC_URL=https://eth-sepolia.g.alchemy.com/v2/YOUR_API_KEY
SEPOLIA_PRIVATE_KEY=your_sepolia_private_key

# Contract Addresses
CONTRACT_ADDRESS=0xYourDeployedContractAddress
```

### Security Best Practices

1. **Never commit private keys to version control**
2. **Use imported wallets for production deployments**
3. **Store only public keys and RPC URLs in `.env` files**
4. **Add `.env` to your `.gitignore` file**
5. **Use different wallets for different environments**

---

## Troubleshooting

### Common Issues

1. **Transaction fails**: Check gas limits and account balance
2. **RPC connection issues**: Verify RPC URL and network connectivity
3. **Wallet not found**: Ensure wallet is properly imported using `cast wallet import`
4. **Permission denied**: Check if the account has sufficient permissions and ETH balance

### Verification Commands

```bash
# Check wallet list
cast wallet list

# Check account balance
cast balance $PUBLIC_KEY --rpc-url $RPC_URL

# Verify contract deployment
cast code $CONTRACT_ADDRESS --rpc-url $RPC_URL
```
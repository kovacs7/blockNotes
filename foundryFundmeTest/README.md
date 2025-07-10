# Foundry Setup and Testing Guide

## 1. Import Configuration for Chainlink Contracts

### Issue with Direct Imports
In Foundry, you cannot use direct imports like this:
```solidity
import {AggregatorV3Interface} from "@chainlink/contracts/src/v0.8/shared/interfaces/AggregatorV3Interface.sol";
```

Unlike Remix (which uses npm to pull the Chainlink library), Foundry requires local installation of dependencies.

### Solution: Install Dependencies Locally
Install the Chainlink contracts using Forge:
```bash
forge install smartcontractkit/chainlink-brownie-contracts@1.3.0
```

This adds the contracts to your `lib` folder alongside the `forge-std` folder.

### Configure Remappings
To avoid using cumbersome absolute paths, configure your `foundry.toml` file:

```toml
auto_detect_remappings = true
remapping = [
  "@chainlink/contracts=lib/chainlink-brownie-contracts/contracts/",
]
```

For all available customization options, refer to the [Foundry configuration documentation](https://github.com/foundry-rs/foundry/blob/master/crates/config/README.md#all-options).

## 2. Testing Types and Commands

### Test Categories
There are 4 types of tests in Foundry:
- **Unit tests**: Test individual functions/components
- **Integration tests**: Test interactions between components
- **Forked tests**: Simulate real-world blockchain environments
- **Staged tests**: Test on actual deployed contracts

Most development focuses on unit and integration tests, with occasional forked tests for real-world simulations.

### Test Commands

#### Run Individual Tests
```bash
forge test --match-test testVersionOfChainlinkContract -vvvv --fork-url $SEPOLIA_RPC_URL
```

#### Run Entire Test Contract
```bash
forge test -vvvv --fork-url $SEPOLIA_RPC_URL
```

#### Check Test Coverage
```bash
forge coverage --fork-url $SEPOLIA_RPC_URL
```

## 3. Network Management and Mock Deployments

### Refactoring for Multi-Network Support
Refactor code to control different network/blockchain deployments and testing from test scripts, enabling seamless switching between networks.

### Mock Environment Setup
To avoid exhausting Alchemy rate limits, create a mock deployment environment using a `HelperConfig` contract that:
- Sets price feeds with appropriate contract addresses
- Verifies chain ID for network-specific configurations
- Provides mock contracts for Anvil (local) deployments

### Benefits of Mock Contracts
- Simulates real-world environment without external API calls
- Reduces dependency on third-party services during development
- Enables faster testing cycles
- Preserves rate limits for production testing
# Foundry Fund Me - Complete Study Notes

## Project Overview

A minimal smart contract project that allows users to fund the contract owner with donations. The contract accepts ETH donations denominated in USD using Chainlink price feeds, with a minimum USD value requirement.

**Key Features:**
- ETH donations with USD denomination
- Minimum donation value enforcement
- Chainlink price feed integration
- Donor tracking for potential future rewards

## Prerequisites

- [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [Foundry](https://getfoundry.sh/)

Verify installations:
```bash
git --version
forge --version
```

## Project Setup

### Dependencies Installation

Install Chainlink contracts:
```bash
forge install smartcontractkit/chainlink-brownie-contracts@0.6.1
```

### Remappings Configuration

Configure `foundry.toml`:
```toml
auto_detect_remappings = true
remapping = [
  "@chainlink/contracts=lib/chainlink-brownie-contracts/contracts/",
]
```

**Note:** The chainlink-brownie-contracts repository is official and maintained by the Chainlink team. It packages npm releases for Foundry compatibility.

## Testing Framework

### Four Types of Testing

1. **Unit Testing**: Testing individual functions
2. **Integration Testing**: Testing multiple functions together
3. **Forked Testing**: Testing on a forked network
4. **Staging Testing**: Testing on live networks (testnet/mainnet)

### Test Commands

**Basic Testing:**
```bash
forge test
```

**Match Specific Test:**
```bash
forge test --match-test testFunctionName -vvv
```

**Forked Testing:**
```bash
forge test --fork-url $SEPOLIA_RPC_URL
```

**Test Coverage:**
```bash
forge coverage
```

**Important:** The `-m` flag is deprecated. Use `--match-test` or `--mt` instead.

## Advanced Concepts

### Helper Config Pattern

Use `HelperConfig` to manage different network configurations:
- Chain ID verification for network-specific settings
- Mock contracts for local development
- Prevents exhausting API rate limits

**Common Chain IDs:**
- Ethereum Mainnet: 1
- Ethereum Sepolia: 11155111

### Mocking Strategy

Create mock contracts to simulate real-world environments:
- Reduces dependency on external services
- Enables faster testing cycles
- Preserves rate limits for production testing

### Foundry Cheatcodes

Essential testing utilities:
- `vm.expectRevert`: Test for expected reverts
- `vm.prank`: Set msg.sender for next call
- `vm.startPrank`: Set msg.sender for multiple calls
- `makeAddr`: Generate test addresses
- `deal`: Set ETH balance for addresses
- `hoax`: Combination of prank and deal

## Gas Optimization

### Gas Analysis Tools

**Gas Snapshot:**
```bash
forge snapshot
```
Creates `.gas-snapshot` file with gas usage data.

**Gas Reporting:**
- Use `vm.txGasPrice` for gas price manipulation
- `gasLeft()` for remaining gas calculation
- `tx.gasprice` for current gas price

### Storage Optimization

**Storage Layout Understanding:**
- Use `cast storage` to inspect storage slots
- Understand EVM opcodes and gas costs
- Reference: [evm.codes](https://evm.codes)

**Variable Naming Conventions:**
- `s_` prefix for storage variables
- `i_` prefix for immutable variables
- `ALL_CAPS_WITH_UNDERSCORES` for constants

## Deployment and Interaction

### Environment Setup

Required environment variables:
- `SEPOLIA_RPC_URL`: Testnet RPC endpoint
- `PRIVATE_KEY`: Account private key (use test accounts only)
- `ETHERSCAN_API_KEY`: For contract verification

### Deployment Commands

**Local Deployment:**
```bash
forge script script/DeployFundMe.s.sol
```

**Testnet Deployment:**
```bash
forge script script/DeployFundMe.s.sol --rpc-url $SEPOLIA_RPC_URL --private-key $PRIVATE_KEY --broadcast --verify --etherscan-api-key $ETHERSCAN_API_KEY
```

### Contract Interaction

**Using Cast:**
```bash
cast send <CONTRACT_ADDRESS> "fund()" --value 0.1ether --private-key <PRIVATE_KEY>
cast send <CONTRACT_ADDRESS> "withdraw()" --private-key <PRIVATE_KEY>
```

**Using Scripts:**
```bash
forge script script/Interactions.s.sol:FundFundMe --rpc-url sepolia --private-key $PRIVATE_KEY --broadcast
forge script script/Interactions.s.sol:WithdrawFundMe --rpc-url sepolia --private-key $PRIVATE_KEY --broadcast
```

## Development Tools

### Chisel
Interactive Solidity shell for testing code snippets and exploring functionality.

### Makefile
Automate common tasks and commands for streamlined development workflow.

### Code Formatting
```bash
forge fmt
```

## Best Practices

### Testing Methodology
- Follow Arrange, Act, Assert pattern
- Use state tree testing for comprehensive coverage
- Implement both unit and integration tests

### Code Style
- Follow [Chainlink Solidity Style Guide](https://github.com/smartcontractkit/chainlink/blob/develop/contracts/STYLE.md)
- Use NatSpec comments for documentation
- Avoid magic numbers - use named constants

### Version Control
Add to `.gitignore`:
```
.env
lib/
broadcast/
```

## Documentation Standards

- Use NatSpec for function documentation
- Follow README best practices
- Include comprehensive setup instructions
- Document all configuration options

## zkSync Integration

For zkSync development:
- Install [foundry-zksync](https://github.com/matter-labs/foundry-zksync)
- Use `npx zksync-cli dev` for local node setup
- Deploy with `make deploy-zk`

## Key Takeaways

1. **Modular Design**: Use Helper Config pattern for network management
2. **Comprehensive Testing**: Implement multiple testing tiers
3. **Gas Awareness**: Optimize for gas efficiency using analysis tools
4. **Best Practices**: Follow style guides and documentation standards
5. **Mock Strategy**: Use mocks to reduce external dependencies during development
# FundMe - Decentralized Crowdfunding Contract

A smart contract crowdfunding application built with Foundry that allows users to send ETH donations with a minimum USD value requirement, using Chainlink price feeds for accurate real-time ETH/USD conversion.

## About

This project demonstrates a decentralized crowdfunding application where:
- Users can fund the contract with ETH (minimum $5 USD equivalent)
- Real-time ETH/USD price conversion using Chainlink oracles
- Only the contract owner can withdraw accumulated funds
- Complete test coverage with unit and integration tests
- Multi-network deployment support (Sepolia, Mainnet, and Anvil)

## Features

- **Minimum Funding Requirement**: Enforces a minimum of $5 USD in ETH
- **Chainlink Price Feeds**: Accurate USD conversion using Chainlink oracles
- **Owner-Only Withdrawals**: Secure withdrawal mechanism restricted to contract owner
- **Funder Tracking**: Maintains records of all funders and their contributions
- **Gas Optimized**: Includes `cheaperWithdraw()` for gas-efficient withdrawals
- **Fallback Functions**: Accept direct ETH transfers via `receive()` and `fallback()`

## Requirements

- [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
  - You'll know you did it right if you can run `git --version` and you see a response like `git version x.x.x`
- [foundry](https://getfoundry.sh/)
  - You'll know you did it right if you can run `forge --version` and you see a response like `forge 0.2.0 (816e00b 2023-03-16T00:05:26.396218Z)`

## Quickstart

```bash
git clone git@github.com:Doc889/foundry_fund_me.git
cd foundry-fund-me
forge install
forge build
```

## Installation

1. Install dependencies:
```bash
forge install
```

2. Create a `.env` file based on `.env_example`:
```bash
cp .env_example .env
```

3. Configure your `.env` file with your RPC URLs and API keys:
```env
SEPOLIA_RPC_URL=<your-sepolia-rpc-url>
MAINNET_RPC_URL=<your-mainnet-rpc-url>
ETHERSCAN_API_KEY=<your-etherscan-api-key>
```

## Usage

### Build

Compile the smart contracts:
```bash
forge build
```

### Testing

We cover 4 test tiers:
1. Unit Tests
2. Integration Tests
3. Forked Tests
4. Staging Tests

This repo covers Unit and Forked tests.

Run all tests:
```bash
forge test
```

Run tests with verbosity:
```bash
forge test -vv
```

Run specific test functions:
```bash
forge test --match-test testFunctionName
```

Run forked tests (using Sepolia):
```bash
forge test --fork-url $SEPOLIA_RPC_URL
```

### Test Coverage

Check test coverage:
```bash
forge coverage
```

### Gas Estimation

Estimate gas costs by creating a snapshot:
```bash
forge snapshot
```

This generates a `.gas-snapshot` file with gas estimates.

## Deployment

### Deploy to Anvil (Local)

```bash
forge script script/DeployFundMe.s.sol --rpc-url http://localhost:8545 --private-key <PRIVATE_KEY> --broadcast
```

### Deploy to Sepolia Testnet

1. Setup environment variables (see Installation section)

2. Get testnet ETH from [faucets.chain.link](https://faucets.chain.link/)

3. Deploy and verify:
```bash
forge script script/DeployFundMe.s.sol --rpc-url $SEPOLIA_RPC_URL --private-key $PRIVATE_KEY --broadcast --verify --etherscan-api-key $ETHERSCAN_API_KEY
```

**Important**: For development, use a private key that doesn't have any real funds associated with it. [Learn how to export from MetaMask](https://metamask.zendesk.com/hc/en-us/articles/360015289632-How-to-Export-an-Account-Private-Key).

## Interacting with Deployed Contracts

### Using Foundry Scripts

Fund the contract:
```bash
forge script script/Interactions.s.sol:FundFundMe --rpc-url $SEPOLIA_RPC_URL --private-key $PRIVATE_KEY --broadcast
```

Withdraw funds (owner only):
```bash
forge script script/Interactions.s.sol:WithdrawFundMe --rpc-url $SEPOLIA_RPC_URL --private-key $PRIVATE_KEY --broadcast
```

### Using Cast

Fund with cast:
```bash
cast send <FUNDME_CONTRACT_ADDRESS> "fund()" --value 0.1ether --private-key <PRIVATE_KEY> --rpc-url <RPC_URL>
```

Withdraw with cast:
```bash
cast send <FUNDME_CONTRACT_ADDRESS> "withdraw()" --private-key <PRIVATE_KEY> --rpc-url <RPC_URL>
```

## Project Structure

```
foundry-fund-me/
├── src/
│   ├── FundMe.sol              # Main crowdfunding contract
│   └── PriceConverter.sol       # Chainlink price conversion library
├── script/
│   ├── DeployFundMe.s.sol       # Deployment script
│   ├── HelperConfig.s.sol       # Network configuration helper
│   └── Interactions.s.sol       # Fund and withdraw scripts
├── test/
│   ├── unit/
│   │   └── FundMeTest.t.sol     # Unit tests
│   └── integration/
│       └── InteractionsTest.t.sol # Integration tests
├── lib/
│   ├── forge-std/               # Foundry standard library
│   ├── chainlink-brownie-contracts/ # Chainlink contracts
│   └── foundry-devops/          # DevOps utilities
├── .env.example                 # Environment variables template
├── foundry.toml                 # Foundry configuration
└── README.md
```

## Smart Contracts

### FundMe.sol

The main crowdfunding contract that handles:
- Accepting ETH donations with minimum USD requirement
- Tracking funders and contribution amounts
- Owner-only withdrawal functionality
- Gas-optimized operations

**Key Functions:**
- `fund()`: Send ETH to the contract (minimum $5 USD equivalent)
- `withdraw()`: Owner-only function to withdraw all funds
- `cheaperWithdraw()`: Gas-optimized withdrawal function
- `getAddressToAmountFunded(address)`: Check how much an address has funded
- `getFunder(uint256)`: Get funder address by index
- `getOwner()`: Get contract owner address

### PriceConverter.sol

A library for converting ETH amounts to USD using Chainlink price feeds:
- `getPrice(AggregatorV3Interface)`: Get current ETH/USD price from Chainlink
- `getConversionRate(uint256, AggregatorV3Interface)`: Convert ETH amount to USD value

## Technologies Used

- **Foundry**: Ethereum development framework
- **Solidity ^0.8.18**: Smart contract programming language
- **Chainlink**: Decentralized oracle network for price feeds
- **Chainlink Brownie Contracts**: Official Chainlink contract package for Foundry

## Gas Optimization

The contract includes two withdrawal methods:
- `withdraw()`: Standard withdrawal function
- `cheaperWithdraw()`: Gas-optimized version that caches the funders array length

## Security Features

- Custom errors (`NotOwner`) for gas-efficient error handling
- Checks-effects-interactions pattern to prevent reentrancy
- Owner-only access control via `onlyOwnwer` modifier
- Minimum funding requirement to prevent dust attacks
- Immutable owner address for gas savings

## Code Formatting

Format your code:
```bash
forge fmt
```


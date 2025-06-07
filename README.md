# FundMe Smart Contract

A decentralized crowdfunding smart contract built on Ethereum that allows users to fund a project with ETH while enforcing a minimum USD contribution threshold using Chainlink price feeds.

## Overview

The FundMe contract enables decentralized fundraising with the following key features:
- Minimum funding requirement of $5 USD (converted from ETH)
- Real-time ETH/USD price conversion using Chainlink oracles
- Owner-only withdrawal functionality
- Automatic fund() execution via fallback and receive functions

## Smart Contracts

### FundMe.sol
The main crowdfunding contract that:
- Accepts ETH donations above the minimum USD threshold
- Tracks funders and their contribution amounts
- Allows only the contract owner to withdraw funds
- Implements fallback functionality for direct ETH transfers

### PriceConverter.sol
A utility library that:
- Fetches real-time ETH/USD prices from Chainlink price feeds
- Converts ETH amounts to USD equivalents
- Handles price data formatting and calculations

## Features

- **Minimum Contribution**: $5 USD equivalent in ETH
- **Price Oracle Integration**: Uses Chainlink ETH/USD price feeds (Sepolia testnet)
- **Access Control**: Only contract deployer can withdraw funds
- **Gas Optimization**: Uses custom errors and efficient withdrawal patterns
- **Fallback Support**: Automatically processes direct ETH transfers as donations

## Requirements

- Solidity ^0.8.24
- Chainlink contracts
- Access to Ethereum testnet (Sepolia) for deployment

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd fundme-contract
```

2. Install dependencies:
```bash
npm install @chainlink/contracts
```

3. Compile contracts:
```bash
npx hardhat compile
```

## Deployment

The contract is configured for Sepolia testnet deployment using the ETH/USD price feed at:
`0x694AA1769357215DE4FAC081bf1f309aDC325306`

### Deploy Script Example
```javascript
const { ethers } = require("hardhat");

async function main() {
    const FundMe = await ethers.getContractFactory("FundMe");
    const fundMe = await FundMe.deploy();
    await fundMe.deployed();
    
    console.log("FundMe deployed to:", fundMe.address);
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
```

## Usage

### Funding the Contract

```javascript
// Fund with ETH (must be >= $5 USD equivalent)
await fundMe.fund({ value: ethers.utils.parseEther("0.01") });
```

### Checking Version
```javascript
// Get Chainlink price feed version
const version = await fundMe.getVersion();
console.log("Price feed version:", version);
```

### Withdrawal (Owner Only)
```javascript
// Only contract owner can withdraw
await fundMe.withdraw();
```

### Checking Contributions
```javascript
// Check how much an address has funded
const amount = await fundMe.addressToAmountFunded(userAddress);
console.log("Funded amount:", ethers.utils.formatEther(amount), "ETH");
```

## Contract Architecture

```
FundMe Contract
├── State Variables
│   ├── addressToAmountFunded (mapping)
│   ├── funders (address array)
│   ├── i_owner (immutable)
│   └── MINIMUM_USD (constant)
├── Functions
│   ├── fund() - Accept donations
│   ├── withdraw() - Owner withdrawal
│   ├── getVersion() - Price feed version
│   ├── fallback() - Auto-fund on ETH transfer
│   └── receive() - Auto-fund on ETH transfer
└── Modifiers
    └── onlyOwner - Access control
```

## Security Features

- **Access Control**: `onlyOwner` modifier restricts withdrawal to contract deployer
- **Custom Errors**: Gas-efficient error handling with `NotOwner()` error
- **Safe Withdrawal**: Uses call() method for secure ETH transfers
- **Price Validation**: Ensures minimum USD contribution regardless of ETH price fluctuations

## Network Configuration

Currently configured for **Sepolia Testnet**:
- ETH/USD Price Feed: `0x694AA1769357215DE4FAC081bf1f309aDC325306`
- Minimum Funding: $5 USD equivalent

For mainnet deployment, update the price feed address to:
`0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419`

## Testing

Run the test suite:
```bash
npx hardhat test
```

Example test scenarios:
- Funding below minimum threshold (should revert)
- Successful funding above minimum
- Non-owner withdrawal attempt (should revert)
- Owner withdrawal functionality
- Price conversion accuracy

## Gas Optimization

The contract implements several gas optimization techniques:
- Immutable owner variable
- Constant minimum USD value
- Custom errors instead of require strings
- Efficient array resetting in withdrawal

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests for new functionality
5. Submit a pull request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Disclaimer

This smart contract is for educational purposes. Always conduct thorough testing and security audits before deploying to mainnet with real funds.
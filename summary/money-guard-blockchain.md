---
description: >-
  A blockchain-based IOU management system that allows users to create, manage,
  and track financial obligations securely on the blockchain.
---

# Money Guard Blockchain

About

MoneyGuard Smart Contract is composed using Solidity and compatible with any EVM blockchains.

- IOUStorage Smart contract
  * Create new IOU smart contract with genesis data which is the hash of contract pdf
  * Allow backend to update repayment status of IOU contract
  * A getter function for verifying the checksum of encrypted data both on-chain and off-chain
- FactoryIOU Smart contract
  * Manage created IOU smart contract
  * Event IOU smart contract creation
  * Event IOU repayment status updated

### Usage

#### Installation

0. Setup

- Install `Foundry` by following the instructions from [their repository](https://github.com/foundry-rs/foundry#installation).
- Install `Node.js` version 22 from [official website](https://nodejs.org/en) 
- Install `yarn` following [`corepack` doc](https://yarnpkg.com/getting-started/install)

1.  Clone the repository:

```bash
git clone https://github.com/your-username/moneyguard-sc.git
cd moneyguard-sc
```
2.  Install dependencies:

```bash
yarn install
```

In case there is an error with the commands, run `foundryup` and try them again.

3.  Configure environment variables:

```bash
cp .env.example .env
# Edit .env with your configuration
```

#### Commands

```bash
# Run unit-test
yarn test:unit

# Run deep test
yarn test:unit:deep

# Run tests
yarn test

# Format code
yarn lint:fix
yarn lint:sol

# Code coverage check
yarn coverage

# Build code
yarn build
yarn build:optimized

# Deploy smart contract
# CMD format: yarn deploy:<chain-identifier>
# Config the Admin and Operator in `script/Deploy.sol`
yarn deploy:bsc_testnet
yarn deploy:bsc
```

### Development

#### Pre-Requisites

* Foundry
* Node.js (22 or higher)
* yarn 4.7.0
* BSC node access (local or testnet)
* Solidity and TypeScript knowledge
* Deployer Wallet
* Admin Wallet
* Operator Wallet

#### File Structure

```
src/
├── contracts/              # Main smart contracts source code
├── interfaces/             # Interfaces for smart contracts
scripts/                    # Deployment scripts
test/                       # Test files
├── unit.ts                 # Unit test
...
```

#### Build

```bash
yarn build
or 
yarn build:optimized
# The build artifacts will be stored in the out/ or out-via-ir directory
```

#### Deployment

1. Build the application

```bash
yarn build
or 
yarn build:optimized
```

2. Set up smart contract constructor variables

In `script/Deploy.sol`
Update the Admin and Operator address following chains

```javascript
// BSC Testnet
address _testnetAdmin = 0xA8a13cF07b9f3AAa1594c43cbEd9a59e7B856cf6;
address[] memory _testnetOperators = new address[](2);
_testnetOperators[0] = 0x61108593078f3d033444dBd385aE992560da2d78;
_testnetOperators[1] = 0xe1bF24F75E73C02C324aDFF24A25357a65D19A3d;
```

3.  Deploy the smart contract

```bash
# For example, deploy to BSC testnet
yarn deploy:bsc_testnet
```

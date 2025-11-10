# Deployment Guide

This tutorial will guide you through deploying OracleX contracts to BNB Chain Testnet.

## 🎯 Prerequisites

Before starting, ensure you have:

```bash
# Required software
✅ Node.js 18+
✅ Git
✅ MetaMask wallet
✅ BNB Testnet BNB (for gas)

# Required knowledge
✅ Basic Solidity understanding
✅ Command line familiarity
✅ Smart contract concepts
```

## 📦 Step 1: Clone Repository

```bash
# Clone the repository
git clone https://github.com/abdussalam-mustapha/OracleX.git
cd OracleX

# Install dependencies
cd contracts
npm install

# Or use yarn
yarn install
```

**Dependencies Installed:**
```json
{
  "@openzeppelin/contracts": "^5.0.0",
  "@openzeppelin/contracts-upgradeable": "^5.0.0",
  "hardhat": "^2.19.0",
  "ethers": "^6.9.0",
  "@nomicfoundation/hardhat-toolbox": "^4.0.0"
}
```

## ⚙️ Step 2: Environment Setup

### Create `.env` file

```bash
# In contracts directory
touch .env
```

### Add Configuration

```bash
# .env file

# Your deployer wallet private key (NEVER commit this!)
PRIVATE_KEY=your_private_key_here

# BNB Chain Testnet RPC
BSC_TESTNET_RPC=https://data-seed-prebsc-1-s1.binance.org:8545/

# Optional: BSCScan API key for verification
BSCSCAN_API_KEY=your_bscscan_api_key_here

# Contract parameters
INITIAL_ORX_SUPPLY=1000000000000000000000000000  # 1 billion tokens
PLATFORM_FEE=250  # 2.5%
MIN_BET=1000000000000000000  # 1 ORX

# Multisig signers (5 addresses)
MULTISIG_SIGNER_1=0x...
MULTISIG_SIGNER_2=0x...
MULTISIG_SIGNER_3=0x...
MULTISIG_SIGNER_4=0x...
MULTISIG_SIGNER_5=0x...
```

### Get Deployer Private Key

```bash
# In MetaMask:
# 1. Click account icon
# 2. Account Details
# 3. Show Private Key
# 4. Enter password
# 5. Copy private key

# ⚠️ SECURITY WARNING:
# - Never share this key
# - Never commit to git
# - Use a separate deployment wallet
# - Fund only with what you need
```

### Get Testnet BNB

```bash
# 1. Visit BNB Testnet Faucet
https://testnet.bnbchain.org/faucet-smart

# 2. Enter your deployer address
# 3. Complete captcha
# 4. Receive 0.5 BNB (usually enough for deployment)

# Verify balance
npx hardhat run scripts/checkBalance.js --network bscTestnet
```

## 📝 Step 3: Configure Hardhat

### hardhat.config.ts

```typescript
import { HardhatUserConfig } from "hardhat/config";
import "@nomicfoundation/hardhat-toolbox";
import "dotenv/config";

const config: HardhatUserConfig = {
  solidity: {
    version: "0.8.20",
    settings: {
      optimizer: {
        enabled: true,
        runs: 200,
      },
      viaIR: true,  // Better optimization
    },
  },
  
  networks: {
    bscTestnet: {
      url: process.env.BSC_TESTNET_RPC || "",
      accounts: process.env.PRIVATE_KEY ? [process.env.PRIVATE_KEY] : [],
      chainId: 97,
      gasPrice: 10000000000, // 10 gwei
    },
    
    bscMainnet: {
      url: "https://bsc-dataseed.binance.org/",
      accounts: process.env.PRIVATE_KEY ? [process.env.PRIVATE_KEY] : [],
      chainId: 56,
      gasPrice: 5000000000, // 5 gwei
    },
  },
  
  etherscan: {
    apiKey: {
      bscTestnet: process.env.BSCSCAN_API_KEY || "",
      bsc: process.env.BSCSCAN_API_KEY || "",
    },
  },
  
  paths: {
    sources: "./contracts",
    tests: "./test",
    cache: "./cache",
    artifacts: "./artifacts",
  },
};

export default config;
```

### Test Configuration

```bash
# Compile contracts
npx hardhat compile

# Expected output:
# Compiled 15 Solidity files successfully
```

## 🚀 Step 4: Deploy Contracts

### Deploy Script

Create `scripts/deploy.ts`:

```typescript
import { ethers, upgrades } from "hardhat";

async function main() {
  console.log("🚀 Starting OracleX deployment...\n");
  
  const [deployer] = await ethers.getSigners();
  console.log("Deployer address:", deployer.address);
  
  const balance = await ethers.provider.getBalance(deployer.address);
  console.log("Balance:", ethers.formatEther(balance), "BNB\n");
  
  // Step 1: Deploy ORX Token
  console.log("📦 Deploying ORX Token...");
  const ORXToken = await ethers.getContractFactory("ORXToken");
  const orxToken = await ORXToken.deploy(
    deployer.address,  // Initial owner
    ethers.parseEther("1000000000")  // 1 billion tokens
  );
  await orxToken.waitForDeployment();
  const orxAddress = await orxToken.getAddress();
  console.log("✅ ORX Token deployed:", orxAddress, "\n");
  
  // Step 2: Deploy Market Factory (Upgradeable)
  console.log("📦 Deploying Market Factory...");
  const MarketFactory = await ethers.getContractFactory("OracleXMarketFactory");
  const marketFactory = await upgrades.deployProxy(
    MarketFactory,
    [
      orxAddress,  // ORX token address
      deployer.address,  // Oracle address (temporary)
      250,  // 2.5% platform fee
    ],
    {
      initializer: "initialize",
      kind: "uups",
    }
  );
  await marketFactory.waitForDeployment();
  const marketFactoryAddress = await marketFactory.getAddress();
  console.log("✅ Market Factory deployed:", marketFactoryAddress, "\n");
  
  // Step 3: Deploy Staking Contract (Upgradeable)
  console.log("📦 Deploying Staking Contract...");
  const Staking = await ethers.getContractFactory("OracleXStaking");
  const staking = await upgrades.deployProxy(
    Staking,
    [
      orxAddress,  // ORX token address
      deployer.address,  // Treasury address
    ],
    {
      initializer: "initialize",
      kind: "uups",
    }
  );
  await staking.waitForDeployment();
  const stakingAddress = await staking.getAddress();
  console.log("✅ Staking Contract deployed:", stakingAddress, "\n");
  
  // Step 4: Deploy Governance DAO (Upgradeable)
  console.log("📦 Deploying Governance DAO...");
  const Governance = await ethers.getContractFactory("OracleXGovernance");
  const governance = await upgrades.deployProxy(
    Governance,
    [
      orxAddress,  // ORX token address
      stakingAddress,  // Staking contract
    ],
    {
      initializer: "initialize",
      kind: "uups",
    }
  );
  await governance.waitForDeployment();
  const governanceAddress = await governance.getAddress();
  console.log("✅ Governance DAO deployed:", governanceAddress, "\n");
  
  // Step 5: Deploy Dispute Resolution (Upgradeable)
  console.log("📦 Deploying Dispute Resolution...");
  const DisputeResolution = await ethers.getContractFactory("OracleXDisputeResolution");
  const disputeResolution = await upgrades.deployProxy(
    DisputeResolution,
    [
      orxAddress,  // ORX token address
      marketFactoryAddress,  // Market factory
      governanceAddress,  // Governance DAO
    ],
    {
      initializer: "initialize",
      kind: "uups",
    }
  );
  await disputeResolution.waitForDeployment();
  const disputeAddress = await disputeResolution.getAddress();
  console.log("✅ Dispute Resolution deployed:", disputeAddress, "\n");
  
  // Step 6: Deploy Oracle Bridge (Upgradeable)
  console.log("📦 Deploying Oracle Bridge...");
  const OracleBridge = await ethers.getContractFactory("OracleXBridge");
  const oracleBridge = await upgrades.deployProxy(
    OracleBridge,
    [
      marketFactoryAddress,  // Market factory
      deployer.address,  // Backend service address
    ],
    {
      initializer: "initialize",
      kind: "uups",
    }
  );
  await oracleBridge.waitForDeployment();
  const oracleAddress = await oracleBridge.getAddress();
  console.log("✅ Oracle Bridge deployed:", oracleAddress, "\n");
  
  // Step 7: Setup Permissions
  console.log("🔧 Setting up permissions...");
  
  // Set oracle address in market factory
  await marketFactory.updateOracleAddress(oracleAddress);
  console.log("✅ Oracle address set in Market Factory");
  
  // Set dispute contract in market factory
  await marketFactory.setDisputeContract(disputeAddress);
  console.log("✅ Dispute contract set in Market Factory");
  
  // Grant roles to staking contract
  await orxToken.grantRole(
    await orxToken.MINTER_ROLE(),
    stakingAddress
  );
  console.log("✅ Minter role granted to Staking Contract\n");
  
  // Step 8: Initial Setup
  console.log("⚙️ Initial setup...");
  
  // Transfer initial liquidity to contracts
  const liquidityAmount = ethers.parseEther("100000000"); // 100M ORX
  await orxToken.transfer(stakingAddress, liquidityAmount);
  console.log("✅ Transferred 100M ORX to Staking Contract");
  
  const rewardPoolAmount = ethers.parseEther("50000000"); // 50M ORX
  await orxToken.transfer(marketFactoryAddress, rewardPoolAmount);
  console.log("✅ Transferred 50M ORX to Market Factory\n");
  
  // Step 9: Deploy Multisig (Optional but recommended)
  console.log("📦 Deploying Multisig Wallet...");
  const Multisig = await ethers.getContractFactory("MultiSigWallet");
  const multisig = await Multisig.deploy(
    [
      process.env.MULTISIG_SIGNER_1,
      process.env.MULTISIG_SIGNER_2,
      process.env.MULTISIG_SIGNER_3,
      process.env.MULTISIG_SIGNER_4,
      process.env.MULTISIG_SIGNER_5,
    ],
    3  // Require 3 of 5 signatures
  );
  await multisig.waitForDeployment();
  const multisigAddress = await multisig.getAddress();
  console.log("✅ Multisig Wallet deployed:", multisigAddress, "\n");
  
  // Step 10: Transfer Ownership to Multisig (CRITICAL!)
  console.log("🔐 Transferring ownership to Multisig...");
  
  await orxToken.transferOwnership(multisigAddress);
  console.log("✅ ORX Token ownership transferred");
  
  await marketFactory.transferOwnership(multisigAddress);
  console.log("✅ Market Factory ownership transferred");
  
  await staking.transferOwnership(multisigAddress);
  console.log("✅ Staking Contract ownership transferred");
  
  await governance.transferOwnership(multisigAddress);
  console.log("✅ Governance DAO ownership transferred");
  
  await disputeResolution.transferOwnership(multisigAddress);
  console.log("✅ Dispute Resolution ownership transferred");
  
  await oracleBridge.transferOwnership(multisigAddress);
  console.log("✅ Oracle Bridge ownership transferred\n");
  
  // Final Summary
  console.log("=" .repeat(60));
  console.log("🎉 DEPLOYMENT COMPLETE!");
  console.log("=".repeat(60));
  console.log("\n📋 Contract Addresses:");
  console.log("   ORX Token:          ", orxAddress);
  console.log("   Market Factory:     ", marketFactoryAddress);
  console.log("   Staking:            ", stakingAddress);
  console.log("   Governance DAO:     ", governanceAddress);
  console.log("   Dispute Resolution: ", disputeAddress);
  console.log("   Oracle Bridge:      ", oracleAddress);
  console.log("   Multisig Wallet:    ", multisigAddress);
  console.log("\n🔗 Network: BNB Chain Testnet (Chain ID: 97)");
  console.log("🔍 View on BSCScan:");
  console.log(`   https://testnet.bscscan.com/address/${orxAddress}`);
  console.log("\n💾 Save these addresses for your .env file!");
  console.log("=".repeat(60));
  
  // Save to file
  const addresses = {
    network: "bscTestnet",
    chainId: 97,
    contracts: {
      orxToken: orxAddress,
      marketFactory: marketFactoryAddress,
      staking: stakingAddress,
      governance: governanceAddress,
      disputeResolution: disputeAddress,
      oracleBridge: oracleAddress,
      multisig: multisigAddress,
    },
    deployer: deployer.address,
    timestamp: new Date().toISOString(),
  };
  
  const fs = require("fs");
  fs.writeFileSync(
    "deployments/bscTestnet.json",
    JSON.stringify(addresses, null, 2)
  );
  console.log("\n✅ Addresses saved to deployments/bscTestnet.json");
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

### Run Deployment

```bash
# Deploy to testnet
npx hardhat run scripts/deploy.ts --network bscTestnet

# Expected output:
# 🚀 Starting OracleX deployment...
# 
# Deployer address: 0x...
# Balance: 0.5 BNB
# 
# 📦 Deploying ORX Token...
# ✅ ORX Token deployed: 0x...
# 
# ... (continues for all contracts)
# 
# 🎉 DEPLOYMENT COMPLETE!

# Deployment takes ~5-10 minutes
```

## ✅ Step 5: Verify Contracts

### Verify on BSCScan

```bash
# Install verification plugin
npm install --save-dev @nomiclabs/hardhat-etherscan

# Verify ORX Token
npx hardhat verify --network bscTestnet \
  0xYourORXTokenAddress \
  "0xYourOwnerAddress" \
  "1000000000000000000000000000"

# Verify Market Factory (proxy)
npx hardhat verify --network bscTestnet \
  0xYourMarketFactoryAddress

# Repeat for all contracts
```

### Automated Verification Script

Create `scripts/verify.ts`:

```typescript
import { run } from "hardhat";
import deployments from "../deployments/bscTestnet.json";

async function main() {
  console.log("Verifying contracts on BSCScan...\n");
  
  // Verify ORX Token
  try {
    await run("verify:verify", {
      address: deployments.contracts.orxToken,
      constructorArguments: [
        deployments.deployer,
        "1000000000000000000000000000",
      ],
    });
    console.log("✅ ORX Token verified");
  } catch (error) {
    console.log("ORX Token already verified or error:", error.message);
  }
  
  // Verify Market Factory
  try {
    await run("verify:verify", {
      address: deployments.contracts.marketFactory,
    });
    console.log("✅ Market Factory verified");
  } catch (error) {
    console.log("Market Factory already verified or error:", error.message);
  }
  
  // Continue for all contracts...
  
  console.log("\n✅ Verification complete!");
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

Run verification:

```bash
npx hardhat run scripts/verify.ts --network bscTestnet
```

## 🧪 Step 6: Test Deployment

### Create Test Script

`scripts/testDeployment.ts`:

```typescript
import { ethers } from "hardhat";
import deployments from "../deployments/bscTestnet.json";

async function main() {
  console.log("🧪 Testing deployment...\n");
  
  // Get contracts
  const orxToken = await ethers.getContractAt(
    "ORXToken",
    deployments.contracts.orxToken
  );
  
  const marketFactory = await ethers.getContractAt(
    "OracleXMarketFactory",
    deployments.contracts.marketFactory
  );
  
  // Test 1: Check ORX Token
  console.log("Test 1: ORX Token");
  const name = await orxToken.name();
  const symbol = await orxToken.symbol();
  const totalSupply = await orxToken.totalSupply();
  console.log(`  Name: ${name}`);
  console.log(`  Symbol: ${symbol}`);
  console.log(`  Total Supply: ${ethers.formatEther(totalSupply)}`);
  console.log("  ✅ Passed\n");
  
  // Test 2: Check Market Factory
  console.log("Test 2: Market Factory");
  const orxAddress = await marketFactory.orxToken();
  const platformFee = await marketFactory.platformFee();
  console.log(`  ORX Token: ${orxAddress}`);
  console.log(`  Platform Fee: ${platformFee / 100}%`);
  console.log("  ✅ Passed\n");
  
  // Test 3: Create Test Market
  console.log("Test 3: Creating test market");
  const tx = await marketFactory.createMarket(
    "Will Bitcoin reach $100k by end of 2025?",
    "Bitcoin",
    Math.floor(Date.now() / 1000) + 86400, // Tomorrow
    Math.floor(Date.now() / 1000) + 86400 * 30, // 30 days
    ethers.parseEther("1"), // 1 ORX min bet
    ["Yes", "No"]
  );
  const receipt = await tx.wait();
  console.log(`  Transaction: ${receipt.hash}`);
  console.log("  ✅ Market created\n");
  
  // Test 4: Check Staking
  console.log("Test 4: Staking Contract");
  const staking = await ethers.getContractAt(
    "OracleXStaking",
    deployments.contracts.staking
  );
  const stakingBalance = await orxToken.balanceOf(
    deployments.contracts.staking
  );
  console.log(`  Staking Balance: ${ethers.formatEther(stakingBalance)} ORX`);
  console.log("  ✅ Passed\n");
  
  console.log("=" .repeat(60));
  console.log("✅ ALL TESTS PASSED!");
  console.log("=".repeat(60));
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

Run tests:

```bash
npx hardhat run scripts/testDeployment.ts --network bscTestnet
```

## 📱 Step 7: Update Frontend

Update `frontend/.env`:

```bash
# Contract addresses from deployment
VITE_ORX_TOKEN_ADDRESS=0x7eE4...824ed79
VITE_MARKET_FACTORY_ADDRESS=0x273C...7c5E1b
VITE_STAKING_ADDRESS=0x007A...dABa2
VITE_GOVERNANCE_ADDRESS=0x0b88...1EfF8F
VITE_DISPUTE_ADDRESS=0x...
VITE_ORACLE_ADDRESS=0x...

# Network
VITE_CHAIN_ID=97
VITE_RPC_URL=https://data-seed-prebsc-1-s1.binance.org:8545/

# API
VITE_API_URL=http://localhost:3000
```

## 🔄 Step 8: Upgrade Contracts (Later)

When you need to upgrade:

```typescript
// scripts/upgrade.ts
import { ethers, upgrades } from "hardhat";
import deployments from "../deployments/bscTestnet.json";

async function main() {
  console.log("Upgrading Market Factory...");
  
  const MarketFactoryV2 = await ethers.getContractFactory(
    "OracleXMarketFactoryV2"
  );
  
  const upgraded = await upgrades.upgradeProxy(
    deployments.contracts.marketFactory,
    MarketFactoryV2
  );
  
  console.log("Market Factory upgraded:", await upgraded.getAddress());
}

main();
```

## 📊 Post-Deployment Checklist

```typescript
// ✅ Deployment Checklist
const checklist = [
  "✅ All contracts deployed",
  "✅ Contracts verified on BSCScan",
  "✅ Ownership transferred to multisig",
  "✅ Initial liquidity provided",
  "✅ Roles and permissions set",
  "✅ Test market created successfully",
  "✅ Frontend environment variables updated",
  "✅ Backend connected to contracts",
  "✅ Addresses saved and backed up",
  "✅ Deployment documented",
];
```

## 🚨 Troubleshooting

### Issue: Insufficient funds

```bash
Error: insufficient funds for intrinsic transaction cost

# Solution: Get more testnet BNB
https://testnet.bnbchain.org/faucet-smart
```

### Issue: Nonce too low

```bash
Error: nonce has already been used

# Solution: Reset account in MetaMask
# Settings → Advanced → Reset Account
```

### Issue: Contract size too large

```bash
Error: contract size exceeds limit

# Solution: Enable optimizer in hardhat.config.ts
optimizer: {
  enabled: true,
  runs: 200,  // Reduce for smaller size
}
```

### Issue: Verification failed

```bash
Error: Failed to verify contract

# Solution: Wait 1-2 minutes after deployment
# Then retry verification
```

## 📚 Resources

- **Hardhat Docs**: https://hardhat.org/docs
- **OpenZeppelin**: https://docs.openzeppelin.com/
- **BSCScan**: https://testnet.bscscan.com/
- **BNB Chain Docs**: https://docs.bnbchain.org/

---

<div style="background: linear-gradient(135deg, #FFD700, #9333EA); padding: 1.5rem; border-radius: 12px; color: white;">
  <strong>🎉 Congratulations!</strong> You've successfully deployed OracleX to BNB Chain Testnet. Next, deploy the backend and frontend to complete your setup.
</div>

# Ethereum Smart Contracts Deployment Guide

## Prerequisites

- Node.js 18+
- Hardhat
- Web3 wallet with ETH for gas
- Infura or Alchemy RPC endpoint

## Networks Supported

### Ethereum Mainnet
- Contract verification on Etherscan
- Production deployment

### Sepolia Testnet
- Free testnet ETH from faucet
- Recommended for testing

## Environment Setup

```bash
# Copy environment template
cp .env.example .env

# Edit .env with your values
ETHEREUM_PRIVATE_KEY=your_private_key
ETHEREUM_RPC_URL=https://sepolia.infura.io/v3/YOUR_KEY
ETHERSCAN_API_KEY=your_etherscan_key
```

## Deployment Steps

### 1. Local Testing

```bash
cd ethereum

# Compile contracts
npx hardhat compile

# Run tests
npx hardhat test

# Local deployment (for testing)
npx hardhat run scripts/deploy.js --network localhost
```

### 2. Testnet Deployment (Sepolia)

```bash
# Get testnet ETH from faucet
# https://sepoliafaucet.com/

# Deploy to Sepolia
npx hardhat run scripts/deploy.js --network sepolia

# Verify contract on Etherscan
npx hardhat verify --network sepolia DEPLOYED_CONTRACT_ADDRESS
```

### 3. Mainnet Deployment

```bash
# ⚠️  IMPORTANT: Verify everything works on testnet first!

# Deploy to mainnet
npx hardhat run scripts/deploy.js --network ethereum

# Verify contract on Etherscan
npx hardhat verify --network ethereum DEPLOYED_CONTRACT_ADDRESS
```

## Deployment Output

After deployment, you'll see:

```
Deploying LawnsterToken to Ethereum...
Deploying with account: 0x...
✅ LawnsterToken deployed to: 0x...

Token Name: Lawnster
Token Symbol: LAWN
Total Supply: 1000000000000000000000000000
Decimals: 18

📋 Deployment info saved to ethereum/deployments.json
```

## Verify Deployment

### Check Contract on Blockchain

```bash
# Sepolia Testnet
https://sepolia.etherscan.io/address/0xYOUR_CONTRACT_ADDRESS

# Mainnet
https://etherscan.io/address/0xYOUR_CONTRACT_ADDRESS
```

### Verify Using Web3

```javascript
const ethers = require('ethers');

const provider = new ethers.providers.JsonRpcProvider(
  'https://sepolia.infura.io/v3/YOUR_KEY'
);

const tokenAddress = '0x...';
const abi = require('./abi.json');

const contract = new ethers.Contract(tokenAddress, abi, provider);
const totalSupply = await contract.totalSupply();
console.log('Total Supply:', totalSupply.toString());
```

## Post-Deployment Tasks

### 1. Verify on Etherscan

```bash
npx hardhat verify --network sepolia CONTRACT_ADDRESS
```

### 2. Save Deployment Info

The deployment script automatically saves to `deployments.json`:

```json
{
  "network": "sepolia",
  "token": {
    "name": "Lawnster",
    "symbol": "LAWN",
    "address": "0x...",
    "totalSupply": "1000000000000000000000000000",
    "decimals": 18,
    "deployedAt": "2024-01-01T00:00:00Z"
  }
}
```

### 3. Update Application

Update your backend and frontend to use the deployed contract address.

## Contract Interactions

### Transfer Tokens

```javascript
const tx = await contract.transfer(recipientAddress, amount);
await tx.wait();
console.log('Transfer complete:', tx.hash);
```

### Burn Tokens

```javascript
const tx = await contract.burn(amount);
await tx.wait();
```

### Pause Transfers

```javascript
const tx = await contract.pause();
await tx.wait();
```

## Gas Optimization

Current gas usage (approximate):
- **Deployment**: ~2-3M gas
- **Transfer**: ~65K gas
- **Approve**: ~46K gas
- **Burn**: ~40K gas

## Safety Checks

Before mainnet deployment:

- [ ] Test on testnet (Sepolia)
- [ ] Verify contract on Etherscan
- [ ] Check gas prices
- [ ] Verify wallet has sufficient ETH
- [ ] Review OpenZeppelin contract versions
- [ ] Check for security vulnerabilities
- [ ] Perform final code review

## Troubleshooting

### Insufficient Gas

```
Error: insufficient funds for gas * price + value
```

Get more ETH for gas fees.

### RPC Connection Error

```
Error: could not detect network
```

Check RPC URL and API keys in `.env`

### Contract Already Deployed

The deploy script checks if a contract already exists. Delete old deployments.json to redeploy.

## Monitoring

After deployment, monitor your contract:

- View on Etherscan
- Track gas prices
- Monitor token transfers
- Check contract balance

## Resources

- [Hardhat Documentation](https://hardhat.org/)
- [Etherscan API](https://docs.etherscan.io/)
- [OpenZeppelin Contracts](https://docs.openzeppelin.com/contracts/)
- [Ethereum Gas Tracker](https://etherscan.io/gastracker)

# Blockchain Operations Module - CDP SDK

A comprehensive TypeScript module for blockchain interactions using Coinbase Developer Platform (CDP) SDK. This module provides easy-to-use functions for ENS registration, token transfers, approvals, and transaction management on EVM-compatible chains.

## 📋 Table of Contents

- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Quick Start](#quick-start)
- [API Reference](#api-reference)
- [Usage Examples](#usage-examples)
- [Network Support](#network-support)
- [Important Notes](#important-notes)
- [Troubleshooting](#troubleshooting)
- [Best Practices](#best-practices)
- [Contributing](#contributing)
- [License](#license)

---

## ✨ Features

### Core Capabilities

- ✅ **ENS Name Registration** - Register and manage Ethereum Name Service domains
- ✅ **Native Token Transfers** - Send ETH/native tokens across networks
- ✅ **ERC-20 Operations** - Transfer, approve, and check allowances for any ERC-20 token
- ✅ **Generic Transactions** - Send any custom transaction to the blockchain
- ✅ **Contract Reading** - Read data from smart contracts (view/pure functions)
- ✅ **TypeScript Support** - Full type safety with comprehensive type definitions
- ✅ **Multi-Network** - Support for Ethereum, Base, and their testnets

### Additional Features

- 🔄 Automatic gas estimation
- 🔐 Secure transaction signing via CDP API
- 📊 Token balance and allowance checking
- 🎯 Idempotency support for reliable operations
- 🛠️ Utility functions for formatting and parsing amounts

---

## 📦 Prerequisites

Before you begin, ensure you have:

- **Node.js** v18+ installed
- **CDP API credentials** (API Key ID and Secret)
- **TypeScript** v5.0+ (optional but recommended)
- Basic understanding of Ethereum/EVM blockchains

---

## 🔧 Installation

### 1. Clone or Copy Files

```bash
# Create project directory
mkdir blockchain-operations
cd blockchain-operations

# Copy the module files
# - abis.ts
# - blockchain.ts
# - utils.ts (optional)
```

### 2. Install Dependencies

```bash
npm install @coinbase/cdp-sdk viem
```

### 3. Install Dev Dependencies

```bash
npm install --save-dev typescript @types/node tsx
```

### 4. Initialize TypeScript

```bash
npx tsc --init
```

Update `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "lib": ["ES2022"],
    "esModuleInterop": true,
    "skipLibCheck": true,
    "strict": true,
    "resolveJsonModule": true,
    "outDir": "./dist"
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

---

## ⚙️ Configuration

### 1. Get CDP API Credentials

Visit [Coinbase Developer Platform](https://portal.cdp.coinbase.com) and:

1. Create a new project
2. Generate API credentials
3. Save your **API Key ID** and **API Key Secret**

### 2. Set Environment Variables

Create a `.env` file:

```bash
CDP_API_KEY_ID=your_api_key_id_here
CDP_API_KEY_SECRET=your_api_key_secret_here
CDP_WALLET_SECRET=optional_wallet_encryption_secret
```

### 3. Load Environment Variables

```typescript
import dotenv from 'dotenv';
dotenv.config();
```

---

## 🚀 Quick Start

### Basic Setup

```typescript
import { CdpClient } from "@coinbase/cdp-sdk";
import { BlockchainOperations } from "./blockchain.js";
import { parseUnits } from "viem";

// Initialize CDP Client
const cdp = new CdpClient({
  apiKeyId: process.env.CDP_API_KEY_ID!,
  apiKeySecret: process.env.CDP_API_KEY_SECRET!,
});

// Create blockchain operations instance
const blockchain = new BlockchainOperations(cdp.openApiClient);

// Get or create an account
const account = await cdp.evm.createAccount({ name: "MyWallet" });
console.log(`Account address: ${account.address}`);
```

### First Transaction

```typescript
// Transfer 0.001 ETH
const result = await blockchain.transferNative({
  from: account.address,
  to: "0x1234567890123456789012345678901234567890",
  amountInEth: "0.001",
  network: "base-sepolia",
});

console.log(`Transaction hash: ${result.transactionHash}`);
```

---

## 📚 API Reference

### BlockchainOperations Class

#### Constructor

```typescript
new BlockchainOperations(client: CdpOpenApiClientType)
```

Creates a new instance of blockchain operations.

**Parameters:**
- `client` - CDP OpenAPI client instance

---

### 1. ENS Operations

#### `registerENSName(options: RegisterENSOptions): Promise<TransactionResult>`

Register a new ENS domain name.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `owner` | `Address` | ✅ | Owner's wallet address |
| `name` | `string` | ✅ | ENS name (without .eth) |
| `durationInYears` | `number` | ✅ | Registration duration |
| `network` | `"ethereum" \| "ethereum-sepolia"` | ✅ | Network to use |
| `idempotencyKey` | `string` | ❌ | Optional idempotency key |

**Returns:** `Promise<TransactionResult>`

**Example:**

```typescript
const result = await blockchain.registerENSName({
  owner: "0x742d35Cc6634C0532925a3b844Bc454e4438f44e",
  name: "myname",
  durationInYears: 1,
  network: "ethereum-sepolia"
});
```

#### `checkENSAvailability(name: string, network: Network): Promise<boolean>`

Check if an ENS name is available for registration.

**Example:**

```typescript
const available = await blockchain.checkENSAvailability(
  "myname",
  "ethereum-sepolia"
);
console.log(`Available: ${available}`);
```

---

### 2. Native Token Operations

#### `transferNative(options: TransferNativeOptions): Promise<TransactionResult>`

Transfer native tokens (ETH) to another address.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `from` | `Address` | ✅ | Sender's address |
| `to` | `Address` | ✅ | Recipient's address |
| `amountInEth` | `string` | ✅ | Amount in ETH (e.g., "0.1") |
| `network` | `Network` | ✅ | Network to use |
| `idempotencyKey` | `string` | ❌ | Optional idempotency key |

**Returns:** `Promise<TransactionResult>`

**Example:**

```typescript
const result = await blockchain.transferNative({
  from: "0x742d35Cc6634C0532925a3b844Bc454e4438f44e",
  to: "0x1234567890123456789012345678901234567890",
  amountInEth: "0.1",
  network: "base-sepolia"
});
```

---

### 3. ERC-20 Token Operations

#### `transferERC20(options: TransferERC20Options): Promise<TransactionResult>`

Transfer ERC-20 tokens to another address.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `from` | `Address` | ✅ | Sender's address |
| `to` | `Address` | ✅ | Recipient's address |
| `tokenAddress` | `Address` | ✅ | Token contract address |
| `amount` | `bigint` | ✅ | Amount in smallest unit |
| `network` | `Network` | ✅ | Network to use |
| `idempotencyKey` | `string` | ❌ | Optional idempotency key |

**Returns:** `Promise<TransactionResult>`

**Example:**

```typescript
import { parseUnits } from "viem";

// Transfer 100 USDC (6 decimals)
const result = await blockchain.transferERC20({
  from: "0x742d35Cc6634C0532925a3b844Bc454e4438f44e",
  to: "0x1234567890123456789012345678901234567890",
  tokenAddress: "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
  amount: parseUnits("100", 6),
  network: "base"
});
```

---

### 4. Approval Operations

#### `approveERC20(options: ApproveERC20Options): Promise<TransactionResult>`

Approve a spender to use your ERC-20 tokens.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `from` | `Address` | ✅ | Owner's address |
| `spender` | `Address` | ✅ | Spender's address |
| `tokenAddress` | `Address` | ✅ | Token contract address |
| `amount` | `bigint` | ✅ | Amount to approve |
| `network` | `Network` | ✅ | Network to use |
| `idempotencyKey` | `string` | ❌ | Optional idempotency key |

**Returns:** `Promise<TransactionResult>`

**Example:**

```typescript
// Approve Uniswap to spend 1000 USDC
const result = await blockchain.approveERC20({
  from: "0x742d35Cc6634C0532925a3b844Bc454e4438f44e",
  spender: "0xUniswapRouterAddress",
  tokenAddress: "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
  amount: parseUnits("1000", 6),
  network: "base"
});
```

#### `approveERC20Unlimited(options): Promise<TransactionResult>`

Approve unlimited amount (max uint256).

⚠️ **Warning:** Use with caution as this gives unlimited approval.

**Example:**

```typescript
const result = await blockchain.approveERC20Unlimited({
  from: "0x742d35Cc6634C0532925a3b844Bc454e4438f44e",
  spender: "0xUniswapRouterAddress",
  tokenAddress: "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
  network: "base"
});
```

---

### 5. Allowance Operations

#### `checkAllowance(options: CheckAllowanceOptions): Promise<bigint>`

Check how much a spender is allowed to spend.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `owner` | `Address` | ✅ | Token owner's address |
| `spender` | `Address` | ✅ | Spender's address |
| `tokenAddress` | `Address` | ✅ | Token contract address |
| `network` | `Network` | ✅ | Network to use |

**Returns:** `Promise<bigint>` - Current allowance amount

**Example:**

```typescript
const allowance = await blockchain.checkAllowance({
  owner: "0x742d35Cc6634C0532925a3b844Bc454e4438f44e",
  spender: "0xUniswapRouterAddress",
  tokenAddress: "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
  network: "base"
});

console.log(`Allowance: ${formatUnits(allowance, 6)} USDC`);
```

#### `hasEnoughAllowance(options): Promise<boolean>`

Check if allowance is sufficient for a transfer.

**Example:**

```typescript
const hasEnough = await blockchain.hasEnoughAllowance({
  owner: "0x742d35Cc6634C0532925a3b844Bc454e4438f44e",
  spender: "0xUniswapRouterAddress",
  tokenAddress: "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
  requiredAmount: parseUnits("500", 6),
  network: "base"
});

console.log(`Has enough allowance: ${hasEnough}`);
```

---

### 6. Generic Transaction Operations

#### `sendTransaction(options: SendTransactionOptions): Promise<TransactionResult>`

Send a generic EVM transaction.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `from` | `Address` | ✅ | Sender's address |
| `transaction` | `TransactionRequestEIP1559` | ✅ | Transaction request |
| `network` | `Network` | ✅ | Network to use |
| `idempotencyKey` | `string` | ❌ | Optional idempotency key |

**Returns:** `Promise<TransactionResult>`

**Example:**

```typescript
import { parseEther } from "viem";

const result = await blockchain.sendTransaction({
  from: "0x742d35Cc6634C0532925a3b844Bc454e4438f44e",
  transaction: {
    to: "0x1234567890123456789012345678901234567890",
    value: parseEther("0.1"),
    data: "0x"
  },
  network: "base-sepolia"
});
```

---

### 7. Read Contract Operations

#### `readContract(options: ReadContractOptions): Promise<unknown>`

Read data from a smart contract (view/pure functions).

⚠️ **Note:** CDP SDK doesn't support direct reads. Use viem or ethers.js for this.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contractAddress` | `Address` | ✅ | Contract address |
| `abi` | `readonly unknown[]` | ✅ | Contract ABI |
| `functionName` | `string` | ✅ | Function to call |
| `args` | `unknown[]` | ❌ | Function arguments |
| `network` | `Network` | ✅ | Network to use |

**Example (with external RPC):**

```typescript
import { createPublicClient, http } from "viem";
import { base } from "viem/chains";

// Use viem for read operations
const publicClient = createPublicClient({
  chain: base,
  transport: http()
});

const balance = await publicClient.readContract({
  address: "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
  abi: ERC20_ABI,
  functionName: "balanceOf",
  args: ["0x742d35Cc6634C0532925a3b844Bc454e4438f44e"]
});
```

---

### Utility Methods

#### `getTokenInfo(tokenAddress: Address, network: Network): Promise<TokenInfo>`

Get comprehensive token information.

**Example:**

```typescript
const info = await blockchain.getTokenInfo(
  "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
  "base"
);

console.log(`Token: ${info.name} (${info.symbol})`);
console.log(`Decimals: ${info.decimals}`);
console.log(`Total Supply: ${info.totalSupply}`);
```

#### `getERC20Balance(tokenAddress, accountAddress, network): Promise<bigint>`

Get token balance for an account.

**Example:**

```typescript
const balance = await blockchain.getERC20Balance(
  "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
  "0x742d35Cc6634C0532925a3b844Bc454e4438f44e",
  "base"
);
```

#### `formatTokenAmount(amount: bigint, decimals: number): string`

Format token amount with proper decimals.

```typescript
const formatted = blockchain.formatTokenAmount(
  BigInt(1000000),
  6
);
console.log(formatted); // "1.0"
```

#### `parseTokenAmount(amount: string, decimals: number): bigint`

Parse amount from human-readable string.

```typescript
const parsed = blockchain.parseTokenAmount("1.5", 6);
console.log(parsed); // 1500000n
```

---

## 💡 Usage Examples

### Complete Workflow Example

```typescript
import { CdpClient } from "@coinbase/cdp-sdk";
import { BlockchainOperations } from "./blockchain.js";
import { parseUnits, formatUnits } from "viem";

async function completeWorkflow() {
  // 1. Initialize
  const cdp = new CdpClient({
    apiKeyId: process.env.CDP_API_KEY_ID!,
    apiKeySecret: process.env.CDP_API_KEY_SECRET!,
  });

  const blockchain = new BlockchainOperations(cdp.openApiClient);
  const account = await cdp.evm.createAccount({ name: "Demo" });

  console.log(`Account: ${account.address}`);

  // 2. Get testnet funds
  await account.requestFaucet({
    network: "base-sepolia",
    token: "eth"
  });

  // 3. Transfer ETH
  const ethTransfer = await blockchain.transferNative({
    from: account.address,
    to: "0x1234567890123456789012345678901234567890",
    amountInEth: "0.001",
    network: "base-sepolia"
  });
  console.log(`ETH Transfer: ${ethTransfer.transactionHash}`);

  // 4. Transfer ERC-20
  const usdcAddress = "0x036CbD53842c5426634e7929541eC2318f3dCF7e";
  const erc20Transfer = await blockchain.transferERC20({
    from: account.address,
    to: "0x1234567890123456789012345678901234567890",
    tokenAddress: usdcAddress,
    amount: parseUnits("10", 6),
    network: "base-sepolia"
  });
  console.log(`USDC Transfer: ${erc20Transfer.transactionHash}`);

  // 5. Approve token spending
  const approval = await blockchain.approveERC20({
    from: account.address,
    spender: "0x9876543210987654321098765432109876543210",
    tokenAddress: usdcAddress,
    amount: parseUnits("100", 6),
    network: "base-sepolia"
  });
  console.log(`Approval: ${approval.transactionHash}`);

  // 6. Check allowance
  const allowance = await blockchain.checkAllowance({
    owner: account.address,
    spender: "0x9876543210987654321098765432109876543210",
    tokenAddress: usdcAddress,
    network: "base-sepolia"
  });
  console.log(`Allowance: ${formatUnits(allowance, 6)} USDC`);
}

completeWorkflow().catch(console.error);
```

### DeFi Integration Example

```typescript
async function swapWithUniswap() {
  const UNISWAP_ROUTER = "0x4752ba5dbc23f44d87826276bf6fd6b1c372ad24";
  const USDC = "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913";

  // 1. Check current allowance
  const currentAllowance = await blockchain.checkAllowance({
    owner: account.address,
    spender: UNISWAP_ROUTER,
    tokenAddress: USDC,
    network: "base"
  });

  const requiredAmount = parseUnits("1000", 6);

  // 2. Approve if needed
  if (currentAllowance < requiredAmount) {
    console.log("Approving USDC for Uniswap...");
    await blockchain.approveERC20({
      from: account.address,
      spender: UNISWAP_ROUTER,
      tokenAddress: USDC,
      amount: requiredAmount,
      network: "base"
    });
  }

  // 3. Execute swap
  // (You would encode the swap call data here)
  console.log("Ready to swap!");
}
```

### Multi-Network Example

```typescript
async function multiNetworkOperations() {
  const networks = ["base", "base-sepolia", "ethereum-sepolia"] as const;

  for (const network of networks) {
    console.log(`\n=== Operating on ${network} ===`);

    // Transfer on each network
    const result = await blockchain.transferNative({
      from: account.address,
      to: "0x1234567890123456789012345678901234567890",
      amountInEth: "0.001",
      network
    });

    console.log(`✅ Transaction: ${result.transactionHash}`);
  }
}
```

---

## 🌐 Network Support

### Supported Networks

| Network | Chain ID | Network Identifier | Native Token |
|---------|----------|-------------------|--------------|
| Ethereum Mainnet | 1 | `ethereum` | ETH |
| Ethereum Sepolia | 11155111 | `ethereum-sepolia` | ETH |
| Base Mainnet | 8453 | `base` | ETH |
| Base Sepolia | 84532 | `base-sepolia` | ETH |

### Token Addresses

Common token addresses are available in `utils.ts`:

```typescript
import { TOKEN_ADDRESSES } from "./utils.js";

const usdcOnBase = TOKEN_ADDRESSES.base.USDC;
const wethOnEthereum = TOKEN_ADDRESSES.ethereum.WETH;
```

---

## ⚠️ Important Notes

### 1. CDP SDK is Server-Side Only

This module uses **custodial wallets** where Coinbase manages the private keys:

- ✅ Perfect for: Backend services, automation, embedded wallets
- ❌ Not for: Traditional Web3 DApp frontends (use MetaMask/WalletConnect)

### 2. Read Operations Require External RPC

CDP SDK doesn't support direct contract reads. For read operations:

```typescript
import { createPublicClient, http } from "viem";
import { base } from "viem/chains";

const publicClient = createPublicClient({
  chain: base,
  transport: http()
});

// Use publicClient for all read operations
const balance = await publicClient.readContract({...});
```

### 3. ENS Registration Requires Two Steps

Real ENS registration requires:

1. **Commit** - Lock in your registration intent
2. **Wait 60 seconds** - Anti-frontrunning protection
3. **Register** - Complete the registration

The provided implementation is simplified for demonstration.

### 4. Gas Fees

- Transactions require native tokens for gas
- CDP API handles gas estimation automatically
- Test on testnets first to avoid wasting real ETH

### 5. Idempotency

Use idempotency keys for critical operations:

```typescript
const result = await blockchain.transferNative({
  // ... other params
  idempotencyKey: "unique-operation-id-123"
});
```

This prevents duplicate transactions if the request is retried.

---

## 🔧 Troubleshooting

### Common Issues

#### 1. "Insufficient funds" Error

**Problem:** Account doesn't have enough ETH for gas.

**Solution:**
```typescript
// For testnets, use faucet
await account.requestFaucet({
  network: "base-sepolia",
  token: "eth"
});
```

#### 2. "Insufficient allowance" Error

**Problem:** Token approval is missing or insufficient.

**Solution:**
```typescript
// Check current allowance first
const allowance = await blockchain.checkAllowance({...});

// Approve if needed
if (allowance < requiredAmount) {
  await blockchain.approveERC20({...});
}
```

#### 3. "Transaction reverted" Error

**Problem:** Smart contract rejected the transaction.

**Common causes:**
- Insufficient token balance
- Invalid recipient address
- Contract-specific requirements not met

**Solution:** Check balances and contract requirements before sending.

#### 4. "Rate limit exceeded" Error

**Problem:** Too many API requests.

**Solution:** Implement retry logic with backoff:

```typescript
import { retryWithBackoff } from "./utils.js";

const result = await retryWithBackoff(
  () => blockchain.transferNative({...}),
  3, // max retries
  1000 // initial delay
);
```

#### 5. ENS Registration Fails

**Problem:** Name already taken or insufficient funds.

**Solution:**
```typescript
// Check availability first
const available = await blockchain.checkENSAvailability("myname", "ethereum-sepolia");

if (!available) {
  console.log("Name already taken!");
}
```

---

## 🎯 Best Practices

### 1. Always Check Balances

```typescript
// Before transferring tokens
const balance = await blockchain.getERC20Balance(
  tokenAddress,
  account.address,
  network
);

if (balance < amount) {
  throw new Error("Insufficient balance");
}
```

### 2. Use Allowance Checks

```typescript
// Before executing swaps or transfers on behalf
const hasEnough = await blockchain.hasEnoughAllowance({
  owner: account.address,
  spender: spenderAddress,
  tokenAddress,
  requiredAmount,
  network
});

if (!hasEnough) {
  await blockchain.approveERC20({...});
}
```

### 3. Handle Errors Gracefully

```typescript
try {
  const result = await blockchain.transferNative({...});
  console.log(`Success: ${result.transactionHash}`);
} catch (error) {
  console.error("Transaction failed:", error.message);
  // Implement retry logic or notify user
}
```

### 4. Use Proper Amount Formatting

```typescript
import { parseUnits, formatUnits } from "viem";

// ✅ GOOD: Use parseUnits for human-readable amounts
const amount = parseUnits("100.5", 6); // 100500000n

// ❌ BAD: Manual calculation prone to errors
const amount = BigInt(100.5 * 1000000);
```

### 5. Test on Testnets First

```typescript
// Always test new operations on testnet
const TESTNET = "base-sepolia";
const MAINNET = "base";

const network = process.env.NODE_ENV === "production"
  ? MAINNET
  : TESTNET;
```

### 6. Use Idempotency Keys

```typescript
// For critical operations, use unique IDs
const operationId = `transfer-${Date.now()}-${userId}`;

await blockchain.transferNative({
  // ... params
  idempotencyKey: operationId
});
```

### 7. Monitor Transaction Status

```typescript
const result = await blockchain.transferNative({...});

console.log(`Transaction submitted: ${result.transactionHash}`);
console.log(`View on explorer: https://basescan.org/tx/${result.transactionHash}`);

// In production, implement a monitoring service
```

---

## 📖 Additional Resources

### Documentation

- [CDP SDK Documentation](https://docs.cdp.coinbase.com/)
- [Viem Documentation](https://viem.sh/)
- [ENS Documentation](https://docs.ens.domains/)
- [ERC-20 Standard](https://eips.ethereum.org/EIPS/eip-20)

### Explorers

- **Ethereum Mainnet**: https://etherscan.io/
- **Ethereum Sepolia**: https://sepolia.etherscan.io/
- **Base Mainnet**: https://basescan.org/
- **Base Sepolia**: https://sepolia.basescan.org/

### Faucets

- **Base Sepolia**: https://www.coinbase.com/faucets/base-sepolia-faucet
- **Ethereum Sepolia**: https://sepoliafaucet.com/

---

## 🤝 Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Development Setup

```bash
# Install dependencies
npm install

# Run type checking
npm run type-check

# Run tests (if available)
npm test
```

---

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

---

## 🆘 Support

Need help? Here are some options:

- 📧 Email: support@yourproject.com
- 💬 Discord: [Join our community](#)
- 🐛 Issues: [GitHub Issues](https://github.com/yourrepo/issues)
- 📚 Docs: [Full Documentation](#)

---

## ⚡ Performance Tips

### 1. Batch Operations

```typescript
// Instead of multiple sequential approvals
await Promise.all([
  blockchain.approveERC20({...}),
  blockchain.approveERC20({...}),
  blockchain.approveERC20({...})
]);
```

### 2. Cache Token Info

```typescript
const tokenInfoCache = new Map();

async function getCachedTokenInfo(address, network) {
  const key = `${address}-${network}`;
  
  if (!tokenInfoCache.has(key)) {
    const info = await blockchain.getTokenInfo(address, network);
    tokenInfoCache.set(key, info);
  }
  
  return tokenInfoCache.get(key);
}
```

### 3. Use Allowance Wisely

```typescript
// Check before approving again
const currentAllowance = await blockchain.checkAllowance({...});

if (currentAllowance >= requiredAmount) {
  console.log("Approval not needed!");
  return;
}
```

---

## 🔐 Security Considerations

### 1. Never Commit Credentials

```bash
# Add to .gitignore
.env
*.key
secrets.json
```

### 2. Use Environment Variables

```typescript
// ✅ GOOD
const apiKey = process.env.CDP_API_KEY_ID;

// ❌ BAD
const apiKey = "cdp_api_key_1234567890";
```

### 3. Validate Addresses

```typescript
import { isAddress } from "viem";

function validateAddress(address: string): void {
  if (!isAddress(address)) {
    throw new Error(`Invalid address: ${address}`);
  }
}
```

### 4. Limit Approvals

```typescript
// Instead of unlimited approval
await blockchain.approveERC20Unlimited({...});

// Use specific amounts
await blockchain.approveERC20({
  amount: parseUnits("1000", 6), // Only approve what's needed
  ...
});
```

---

## 📊 Example Project Structure

```
my-blockchain-project/
├── src/
│   ├── abis.ts                 # Contract ABIs
│   ├── blockchain.ts           # Main operations
│   ├── utils.ts                # Helper functions
│   ├── config.ts               # Configuration
│   └── examples/
│       ├── basic-transfer.ts
│       ├── token-approval.ts
│       └── ens-registration.ts
├── tests/
│   └── blockchain.test.ts
├── .env
├── .gitignore
├── package.json
├── tsconfig.json
└── README.md
```

---

## 🎓 Learning Resources

### Beginner

- [Ethereum Basics](https://ethereum.org/en/developers/docs/)
- [What is an ABI?](https://docs.soliditylang.org/en/latest/abi-spec.html)
- [Understanding Gas](https://ethereum.org/en/developers/docs/gas/)

### Intermediate

- [ERC-20 Deep Dive](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/)
- [Smart Contract Security](https://consensys.github.io/smart-contract-best-practices/)
- [ENS Architecture](https://docs.ens.domains/contract-api-reference/.eth-permanent-registrar)

### Advanced

- [Account Abstraction (ERC-4337)](https://eips.ethereum.org/EIPS/eip-4337)
- [MEV Protection](https://docs.flashbots.net/)
- [Layer 2 Scaling](https://ethereum.org/en/developers/docs/scaling/)

---

**Made with ❤️ by the CDP Community**

**Version:** 1.0.0  
**Last Updated:** 2024

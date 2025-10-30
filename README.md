# KipuBank V2 Smart Contract

## 📋 Description

**KipuBank V2** is an advanced decentralized banking smart contract built in Solidity that supports multi-token deposits and withdrawals (ETH and ERC20 tokens). The contract uses Chainlink price feeds for real-time USD valuation, implements role-based access control, and features sophisticated decimal normalization for cross-token compatibility.

### 🔥 Key Features

- **Multi-Token Support**: Deposit and withdraw ETH and any supported ERC20 tokens
- **Chainlink Integration**: Real-time USD price feeds for accurate valuation
- **USD-Based Limits**: Bank capacity and withdrawal limits denominated in USD
- **Role-Based Access Control**: Admin and Emergency roles for governance
- **Decimal Normalization**: Automatic handling of different token decimals (normalized to 6 decimals)
- **Advanced Security**: CEI pattern, reentrancy protection, and pause mechanism
- **Fallback Functions**: Automatic ETH deposit when sent directly to contract
- **Minimum Deposit**: $1 USD minimum deposit requirement
- **Detailed Events**: Complete tracking of all operations with USD values
- **Complete Metrics**: Global TVL and per-token statistics

### 🛡️ Security Features

- ✅ OpenZeppelin AccessControl for role management
- ✅ Custom errors for gas efficiency
- ✅ Modifiers for input validation and pause mechanism
- ✅ Reentrancy protection via CEI pattern
- ✅ Secure transfers with `call()` for ETH and `transfer()` for ERC20
- ✅ Chainlink oracle integration for price validation
- ✅ Fallback and receive functions for direct ETH transfers
- ✅ Emergency pause functionality
- ✅ Per-token decimal normalization

## 🚀 Deployment with Remix IDE

### Prerequisites

- MetaMask or compatible wallet
- ETH for deployment (testnet recommended for testing)
- Access to [Remix IDE](https://remix.ethereum.org/)
- OpenZeppelin and Chainlink dependencies
- Chainlink ETH/USD price feed address for your network

### Step-by-Step Deployment

1. **Open Remix IDE**
   - Go to [https://remix.ethereum.org/](https://remix.ethereum.org/)
   - Create a new file named `KipuBankV2.sol`
   - Copy and paste the contract code

2. **Install Dependencies**
   - The contract imports OpenZeppelin and Chainlink contracts
   - Remix will auto-import from GitHub when you compile
   - Required imports:
     - `@openzeppelin/contracts/access/AccessControl.sol`
     - `@openzeppelin/contracts/token/ERC20/IERC20.sol`
     - `@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol`
     - `@chainlink/contracts/src/v0.8/shared/interfaces/AggregatorV3Interface.sol`

3. **Compile the Contract**
   - Go to the "Solidity Compiler" tab
   - Select compiler version `0.8.20` or higher
   - Click "Compile KipuBankV2.sol"
   - Ensure compilation is successful (green checkmark)

4. **Get Chainlink Price Feed Address**
   - **Sepolia Testnet**: `0x694AA1769357215DE4FAC081bf1f309aDC325306`
   - **Ethereum Mainnet**: `0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419`
   - Find more at: [Chainlink Price Feeds](https://docs.chain.link/data-feeds/price-feeds/addresses)

5. **Deploy the Contract**
   - Go to the "Deploy & Run Transactions" tab
   - Select your environment (Injected Provider - MetaMask)
   - Connect your MetaMask wallet
   - Select "KipuBank" from the contract dropdown

6. **Set Constructor Parameters**
   The contract requires three parameters (all with 6 decimals):
   - `_bankCapUsd`: Maximum total bank capacity in USD (6 decimals)
   - `_withdrawalLimitUsd`: Maximum withdrawal per transaction in USD (6 decimals)
   - `_ethUsdPriceFeed`: Chainlink ETH/USD price feed address
   
   **Example values:**
   - For $100,000 bank cap: `100000000000` (100000 × 10^6)
   - For $10,000 withdrawal limit: `10000000000` (10000 × 10^6)
   - ETH/USD Price Feed (Sepolia): `0x694AA1769357215DE4FAC081bf1f309aDC325306`
   
   **Note**: All USD values use 6 decimals precision

7. **Deploy**
   - Enter the constructor parameters
   - Click "Deploy"
   - Confirm the transaction in MetaMask
   - Wait for deployment confirmation

## 🔧 Contract Interaction with Remix IDE

### 💰 Making Deposits

#### Method 1: Depositing Native ETH (via receive/fallback)
1. **Locate the Contract**
   - After deployment, find your contract in the "Deployed Contracts" section
   - Expand the contract to see available functions

2. **Set Deposit Amount**
   - In the **VALUE** field at the top, enter the amount you want to deposit
   - Change the unit dropdown from "Wei" to **"Ether"**
   - Example: Enter `0.5` and select "Ether" to deposit 0.5 ETH

3. **Execute Deposit**
   - Click on the contract address or use the low-level interactions
   - The receive() function will automatically process the deposit
   - Confirm the transaction in MetaMask
   - Wait for transaction confirmation

#### Method 2: Direct ETH Transfer
- Simply send ETH directly to the contract address from your wallet
- The receive/fallback functions will automatically process it as a deposit
- Must meet minimum $1 USD requirement

#### Method 3: Depositing ERC20 Tokens
1. **First, approve the contract** (on the token contract)
   - Go to your ERC20 token contract in Remix
   - Call `approve(kipuBankAddress, amount)`
   - Confirm the transaction

2. **Deposit the tokens**
   - Return to KipuBank contract
   - Find the **"depositToken"** function
   - Enter token address and amount (in token's native decimals)
   - Click to execute
   - Confirm transaction

### 💸 Making Withdrawals

1. **Withdraw Native ETH or ERC20**
   - Find the **"withdraw"** function in your deployed contract
   - Enter two parameters:
     - `token`: Address of token (use `0x0000000000000000000000000000000000000000` for ETH)
     - `amount`: Amount in token's native decimals
   
   **Examples:**
   - Withdraw 0.5 ETH: token = `0x0000000000000000000000000000000000000000`, amount = `500000000000000000`
   - Withdraw 100 USDC: token = `USDC_ADDRESS`, amount = `100000000` (USDC has 6 decimals)

2. **Execute Withdrawal**
   - Click the orange **"withdraw"** button
   - Must not exceed withdrawal limit in USD
   - Confirm the transaction in MetaMask
   - Tokens will be sent to your wallet

### 📊 Querying Contract State

#### Check Your Balance
1. **getUserBalance**
   - Parameters: `user` (address), `token` (address)
   - Returns normalized balance (6 decimals) and timestamp
   - Example: `getUserBalance(YOUR_ADDRESS, 0x0000000000000000000000000000000000000000)`

2. **getUserBalanceUsd**
   - Parameters: `user` (address), `token` (address)
   - Returns balance in USD (6 decimals)

#### Check Remaining Bank Capacity
1. Find **"getRemainingCapacityUsd"**
2. Click to see remaining capacity in USD (6 decimals)
3. Shows how much more value can be deposited

#### Check Contract Statistics
- **"totalValueLockedUsd"**: Total value locked in USD (6 decimals)
- **"bankCapUsd"**: Maximum bank capacity in USD (6 decimals)
- **"WITHDRAWAL_LIMIT_USD"**: Withdrawal limit per transaction in USD (6 decimals)
- **"paused"**: Check if contract is paused
- **"getEthPrice"**: Current ETH price from Chainlink oracle

#### Check Token Information
- **"tokens"**: Enter token address to see:
  - `isSupported`: Whether token is supported
  - `totalDeposits`: Total deposits for that token (normalized to 6 decimals)
  - `decimals`: Token's native decimal count

#### Converting Values
- **6-decimal USD to dollar**: Divide by 1,000,000
- **Wei to ETH**: Divide by 1,000,000,000,000,000,000 (18 zeros)
- **Use convertToUsd**: Get real-time USD value of any token amount

## 📋 Available Functions

### User Functions

| Function | Type | Description |
|----------|------|-------------|
| `receive()` | payable | Deposits ETH automatically when sent to contract |
| `fallback()` | payable | Handles direct ETH transfers |
| `depositToken(address, uint256)` | external | Deposits ERC20 tokens |
| `withdraw(address, uint256)` | external | Withdraws tokens (ETH or ERC20) |

### View Functions

| Function | Returns | Description |
|----------|---------|-------------|
| `getUserBalance(address, address)` | (uint256, uint256) | Returns user's balance and timestamp |
| `getUserBalanceUsd(address, address)` | uint256 | Returns user's balance in USD |
| `convertToUsd(address, uint256)` | uint256 | Converts token amount to USD |
| `getRemainingCapacityUsd()` | uint256 | Returns remaining capacity in USD |
| `getEthPrice()` | uint256 | Returns current ETH/USD price |

### Admin Functions (ADMIN_ROLE)

| Function | Description |
|----------|-------------|
| `addToken(address, address)` | Adds new ERC20 token with price feed |
| `removeToken(address)` | Removes token support |
| `updateBankCap(uint256)` | Updates bank capacity in USD |

### Emergency Functions (EMERGENCY_ROLE)

| Function | Description |
|----------|-------------|
| `setPaused(bool)` | Pauses/unpauses all operations |

### Public Variables & Constants

| Variable | Type | Description |
|----------|------|-------------|
| `TARGET_DECIMALS` | constant (uint8) | Normalized decimal precision (6) |
| `NATIVE_ETH` | constant (address) | Address for native ETH (0x0) |
| `MIN_DEPOSIT_USD` | constant (uint256) | Minimum deposit in USD (1e6 = $1) |
| `WITHDRAWAL_LIMIT_USD` | immutable | Maximum withdrawal per transaction (USD) |
| `ethUsdPriceFeed` | immutable | Chainlink ETH/USD price feed |
| `bankCapUsd` | uint256 | Maximum total bank capacity (USD) |
| `totalValueLockedUsd` | uint256 | Total value locked (USD) |
| `paused` | bool | Contract pause status |

### Events

| Event | Parameters | Description |
|-------|------------|-------------|
| `Deposit` | `user`, `token`, `amount`, `amountUsd` | Emitted on deposits |
| `Withdrawal` | `user`, `token`, `amount`, `amountUsd` | Emitted on withdrawals |
| `TokenAdded` | `token`, `priceFeed` | Emitted when new token is added |

### Custom Errors

| Error | Description |
|-------|-------------|
| `ZeroAmount()` | Amount is zero |
| `TokenNotSupported(address)` | Token is not supported |
| `ExceedsBankCap(uint256, uint256)` | Deposit exceeds capacity |
| `ExceedsWithdrawalLimit(uint256, uint256)` | Withdrawal exceeds limit |
| `InsufficientBalance(uint256, uint256)` | Insufficient balance |
| `TransferFailed()` | Token transfer failed |
| `InvalidPriceFeed()` | Price feed returned invalid data |
| `BelowMinimumDeposit(uint256, uint256)` | Deposit below $1 minimum |
| `ContractPaused()` | Contract is paused |

## 🔒 Rules and Limitations

### Deposits
- ✅ Minimum $1 USD value required
- ✅ Limited by remaining bank capacity (USD)
- ✅ Must send ETH with transaction (for native ETH)
- ✅ Must approve contract first (for ERC20 tokens)
- ✅ Token must be supported by the contract
- ✅ Contract must not be paused

### Withdrawals
- ✅ Value must be > 0
- ✅ Cannot exceed user balance
- ✅ Cannot exceed per-transaction limit (USD)
- ✅ Must have sufficient balance
- ✅ Token must be supported
- ✅ Contract must not be paused

### Token Support
- ✅ Native ETH supported by default
- ✅ ERC20 tokens must be added by admin
- ✅ Each token needs a Chainlink price feed
- ✅ Tokens can be removed by admin (except ETH)

## 🧪 Testing in Remix IDE

### Manual Testing Steps

1. **Deploy with Test Parameters**
   - Bank Cap: `50000000000` ($50,000 USD with 6 decimals)
   - Withdrawal Limit: `10000000000` ($10,000 USD with 6 decimals)
   - ETH/USD Price Feed (Sepolia): `0x694AA1769357215DE4FAC081bf1f309aDC325306`

2. **Test ETH Deposits**
   - Set VALUE to `0.01` Ether (should be ~$30-40 depending on ETH price)
   - Send ETH to contract (receive function will process it)
   - Check balance: `getUserBalance(YOUR_ADDRESS, 0x0000000000000000000000000000000000000000)`
   - Check USD value: `getUserBalanceUsd(YOUR_ADDRESS, 0x0000000000000000000000000000000000000000)`
   - Verify `totalValueLockedUsd` increased
   - Check ETH price: `getEthPrice()`

3. **Test ERC20 Deposits** (if you have test tokens)
   - First add token: `addToken(TOKEN_ADDRESS, PRICE_FEED_ADDRESS)` (requires ADMIN_ROLE)
   - Approve contract: On token contract, call `approve(KIPUBANK_ADDRESS, AMOUNT)`
   - Deposit tokens: `depositToken(TOKEN_ADDRESS, AMOUNT)`
   - Check balance: `getUserBalance(YOUR_ADDRESS, TOKEN_ADDRESS)`

4. **Test Withdrawals**
   - Withdraw ETH: `withdraw(0x0000000000000000000000000000000000000000, AMOUNT_IN_WEI)`
   - Check balance decreased: `getUserBalance(YOUR_ADDRESS, 0x0000000000000000000000000000000000000000)`
   - Verify `totalValueLockedUsd` decreased
   - Check remaining capacity: `getRemainingCapacityUsd()`

5. **Test Limits**
   - Try withdrawing more than USD limit
   - Should fail with `ExceedsWithdrawalLimit` error
   - Try depositing more than `getRemainingCapacityUsd()` shows
   - Should fail with `ExceedsBankCap` error
   - Try depositing less than $1 (e.g., 0.0001 ETH)
   - Should fail with `BelowMinimumDeposit` error

6. **Test Admin Functions**
   - Add a new token: `addToken(TOKEN_ADDRESS, PRICE_FEED_ADDRESS)`
   - Update bank cap: `updateBankCap(NEW_CAP_USD)`
   - Remove token: `removeToken(TOKEN_ADDRESS)`

7. **Test Emergency Functions**
   - Pause contract: `setPaused(true)`
   - Try depositing (should fail with `ContractPaused`)
   - Unpause: `setPaused(false)`

8. **Test Edge Cases**
   - Try depositing 0 ETH (should fail with `ZeroAmount`)
   - Try withdrawing more than your balance
   - Try withdrawing 0 (should fail with `ZeroAmount`)
   - Try unsupported token (should fail with `TokenNotSupported`)

### Recommended Test Network
- Use **Sepolia** testnet for testing (has active Chainlink price feeds)
- Get test ETH from faucets:
  - [Sepolia Faucet](https://sepoliafaucet.com/)
  - [Chainlink Faucet](https://faucets.chain.link/sepolia)

### Getting Test ERC20 Tokens
- Deploy a simple ERC20 for testing or use existing test tokens on Sepolia
- Find Chainlink price feeds: [Price Feeds Directory](https://docs.chain.link/data-feeds/price-feeds/addresses?network=ethereum&page=1)

## 📄 License

This project is licensed under the MIT License.

## ⚠️ Legal Disclaimer

This contract is provided "as is" for educational and demonstration purposes. Always conduct security audits before using in production with real funds.

## 🤝 Contributions

Contributions are welcome! Please open an issue or pull request for improvement suggestions.

## 🏗️ Architecture Overview

### Key Components

1. **Multi-Token Support**
   - Native ETH and ERC20 tokens
   - Each token has dedicated price feed
   - Decimal normalization to 6 decimals

2. **Chainlink Integration**
   - Real-time price feeds for accurate USD valuation
   - Price validation to prevent stale data
   - Used for all limit calculations

3. **Role-Based Access Control**
   - `DEFAULT_ADMIN_ROLE`: OpenZeppelin default admin
   - `ADMIN_ROLE`: Manage tokens and parameters
   - `EMERGENCY_ROLE`: Emergency pause/unpause

4. **Decimal Normalization**
   - All balances stored as 6-decimal normalized values
   - Automatic conversion for different token decimals
   - Consistent USD calculations across all tokens

### Data Structures

```solidity
struct TokenInfo {
    bool isSupported;
    uint256 totalDeposits;  // Normalized to 6 decimals
    uint8 decimals;
}

struct UserDeposit {
    uint256 amount;         // Normalized to 6 decimals
    uint256 timestamp;
}
```

### Important Mappings

- `mapping(address => TokenInfo) public tokens` - Token configuration
- `mapping(address => mapping(address => UserDeposit)) public userDeposits` - User balances per token
- `mapping(address => AggregatorV3Interface) public priceFeeds` - Chainlink price feeds per token

---

**Built with ❤️ by Ernesto de Oliveira**
# KipuBankV2

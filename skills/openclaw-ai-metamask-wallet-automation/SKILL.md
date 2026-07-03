---
name: openclaw-ai-metamask-wallet-automation
description: EVM-compatible wallet automation framework with secure key derivation, multi-threaded RPC routing, and transaction queuing for blockchain applications
triggers:
  - automate metamask wallet transactions
  - integrate openclaw wallet framework
  - set up EVM transaction queuing
  - use openclaw for blockchain automation
  - configure multi-chain wallet operations
  - implement secure key derivation for wallets
  - optimize gas fees with openclaw
  - build wallet automation with openclaw-ai
---

# Openclaw AI Metamask Wallet Automation

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill enables AI coding agents to work with the Openclaw-AI-Metamask-26 framework, an advanced EVM-compatible wallet automation and transaction queuing system built in C++. The framework provides secure local key derivation, multi-threaded RPC routing, and non-blocking gas optimization for blockchain applications.

## What This Project Does

Openclaw-AI-Metamask-26 is a C++ framework that provides:

- **Wallet Automation**: Programmatic control over EVM-compatible wallet operations
- **Transaction Queuing**: Efficient batching and scheduling of blockchain transactions
- **Secure Key Derivation**: Local key management using hierarchical deterministic (HD) wallets
- **Multi-threaded RPC Routing**: Parallel RPC calls to multiple blockchain nodes
- **Gas Optimization**: Non-blocking analysis and optimization of transaction gas fees
- **Multi-chain Support**: Compatible with Ethereum, BSC, Polygon, and other EVM chains

## Installation

### Prerequisites

- C++17 or higher compiler (GCC 9+, Clang 10+, or MSVC 2019+)
- CMake 3.15 or higher
- OpenSSL development libraries
- libcurl for RPC communication
- Boost libraries (optional, for threading optimization)

### Build from Source

```bash
# Clone the repository
git clone https://github.com/olot-1477/Openclaw-AI-Metamask-26.git
cd Openclaw-AI-Metamask-26

# Create build directory
mkdir build && cd build

# Configure with CMake
cmake .. -DCMAKE_BUILD_TYPE=Release

# Build the project
make -j$(nproc)

# Install (optional)
sudo make install
```

### Linking in Your Project

**CMakeLists.txt:**
```cmake
find_package(OpenclawAI REQUIRED)

add_executable(your_app main.cpp)
target_link_libraries(your_app OpenclawAI::Core OpenclawAI::Wallet)
```

## Core API Components

### 1. Wallet Initialization

```cpp
#include <openclaw/wallet.h>
#include <openclaw/key_derivation.h>

// Initialize wallet with mnemonic from environment
const char* mnemonic = std::getenv("WALLET_MNEMONIC");
openclaw::KeyDerivationMatrix kdm(mnemonic);

// Derive account at path m/44'/60'/0'/0/0 (Ethereum standard)
openclaw::Wallet wallet = kdm.derive_wallet("m/44'/60'/0'/0/0");

std::cout << "Wallet Address: " << wallet.get_address() << std::endl;
```

### 2. RPC Configuration

```cpp
#include <openclaw/rpc_router.h>

// Configure multi-threaded RPC routing
openclaw::RPCConfig config;
config.add_endpoint("https://eth-mainnet.g.alchemy.com/v2/" + 
                    std::string(std::getenv("ALCHEMY_API_KEY")));
config.add_endpoint("https://rpc.ankr.com/eth");
config.set_thread_pool_size(4);
config.set_timeout_ms(5000);

openclaw::RPCRouter router(config);
```

### 3. Transaction Creation and Queuing

```cpp
#include <openclaw/transaction.h>
#include <openclaw/queue.h>

// Create transaction
openclaw::Transaction tx;
tx.set_to("0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb");
tx.set_value("0.1"); // ETH amount
tx.set_gas_limit(21000);
tx.set_chain_id(1); // Ethereum mainnet

// Add to queue with gas optimization
openclaw::TransactionQueue queue(router);
queue.set_gas_strategy(openclaw::GasStrategy::FAST);
queue.add_transaction(tx, wallet);

// Process queue (non-blocking)
queue.process_async([](const openclaw::TransactionResult& result) {
    if (result.is_success()) {
        std::cout << "TX Hash: " << result.get_hash() << std::endl;
    } else {
        std::cerr << "Error: " << result.get_error() << std::endl;
    }
});
```

### 4. Gas Optimization

```cpp
#include <openclaw/gas_optimizer.h>

openclaw::GasOptimizer optimizer(router);

// Get optimized gas parameters
auto gas_params = optimizer.estimate_optimal_gas(tx);

tx.set_gas_price(gas_params.gas_price);
tx.set_gas_limit(gas_params.gas_limit);
tx.set_max_priority_fee(gas_params.priority_fee); // EIP-1559

std::cout << "Estimated Gas: " << gas_params.total_cost_eth() << " ETH" << std::endl;
```

## Common Patterns

### Pattern 1: Batch Transaction Processing

```cpp
#include <openclaw/batch.h>

openclaw::BatchProcessor batch(router, wallet);
batch.set_batch_size(10);
batch.set_delay_between_batches_ms(1000);

// Add multiple transactions
std::vector<std::string> recipients = {
    "0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb",
    "0x1234567890123456789012345678901234567890",
    // ... more addresses
};

for (const auto& recipient : recipients) {
    openclaw::Transaction tx;
    tx.set_to(recipient);
    tx.set_value("0.01");
    batch.add(tx);
}

// Execute batch with automatic nonce management
batch.execute_sequential();
```

### Pattern 2: Multi-Chain Operations

```cpp
#include <openclaw/multichain.h>

// Configure multiple chains
openclaw::MultichainRouter mc_router;
mc_router.add_chain(1, "https://eth-mainnet.alchemyapi.io/v2/" + 
                    std::string(std::getenv("ALCHEMY_API_KEY")));
mc_router.add_chain(56, "https://bsc-dataseed.binance.org/");
mc_router.add_chain(137, "https://polygon-rpc.com/");

// Execute transaction on specific chain
auto eth_tx = mc_router.create_transaction(1); // Ethereum
eth_tx.set_to("0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb");
eth_tx.set_value("0.1");

auto bsc_tx = mc_router.create_transaction(56); // BSC
bsc_tx.set_to("0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb");
bsc_tx.set_value("0.01");

// Process on respective chains
mc_router.submit(eth_tx, wallet);
mc_router.submit(bsc_tx, wallet);
```

### Pattern 3: Smart Contract Interaction

```cpp
#include <openclaw/contract.h>

// Load contract ABI
openclaw::Contract erc20("0xdAC17F958D2ee523a2206206994597C13D831ec7"); // USDT
erc20.load_abi_from_file("usdt_abi.json");

// Prepare transfer call
auto transfer_data = erc20.encode_function_call(
    "transfer",
    {
        {"address", "0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb"},
        {"uint256", "1000000"} // 1 USDT (6 decimals)
    }
);

// Create transaction with contract call
openclaw::Transaction tx;
tx.set_to(erc20.address());
tx.set_data(transfer_data);
tx.set_value("0"); // No ETH transfer

// Execute
queue.add_transaction(tx, wallet);
```

### Pattern 4: Event Monitoring

```cpp
#include <openclaw/events.h>

openclaw::EventMonitor monitor(router);

// Monitor Transfer events
monitor.subscribe(
    "0xdAC17F958D2ee523a2206206994597C13D831ec7", // Contract
    "Transfer(address,address,uint256)",
    [](const openclaw::Event& event) {
        std::cout << "Transfer detected!" << std::endl;
        std::cout << "From: " << event.get_param("from") << std::endl;
        std::cout << "To: " << event.get_param("to") << std::endl;
        std::cout << "Amount: " << event.get_param("value") << std::endl;
    }
);

// Start monitoring
monitor.start();
```

## Configuration

### Environment Variables

```bash
# Required
export WALLET_MNEMONIC="your twelve word mnemonic phrase here"
export ALCHEMY_API_KEY="your_alchemy_api_key"

# Optional
export OPENCLAW_RPC_TIMEOUT=5000
export OPENCLAW_THREAD_POOL_SIZE=4
export OPENCLAW_GAS_STRATEGY=FAST  # Options: SLOW, MEDIUM, FAST
export OPENCLAW_LOG_LEVEL=INFO     # Options: DEBUG, INFO, WARN, ERROR
```

### Configuration File (openclaw.conf)

```ini
[network]
chain_id = 1
rpc_endpoints = https://eth-mainnet.alchemyapi.io/v2/${ALCHEMY_API_KEY},https://rpc.ankr.com/eth
timeout_ms = 5000

[gas]
strategy = FAST
max_gas_price_gwei = 500
priority_fee_percentile = 75

[threading]
rpc_thread_pool_size = 4
transaction_processor_threads = 2

[security]
key_derivation_path = m/44'/60'/0'/0
use_hardware_wallet = false
```

### Loading Configuration in Code

```cpp
#include <openclaw/config.h>

openclaw::Config config;
config.load_from_file("openclaw.conf");
config.load_from_env(); // Override with environment variables

openclaw::RPCRouter router(config.get_rpc_config());
openclaw::GasOptimizer optimizer(config.get_gas_config());
```

## Troubleshooting

### Issue: RPC Connection Timeouts

```cpp
// Increase timeout and add fallback endpoints
config.set_timeout_ms(10000);
config.add_endpoint("https://cloudflare-eth.com");
config.enable_automatic_failover(true);
```

### Issue: Nonce Conflicts in Concurrent Transactions

```cpp
#include <openclaw/nonce_manager.h>

// Use centralized nonce manager
openclaw::NonceManager nonce_mgr(router, wallet.get_address());

for (auto& tx : transactions) {
    tx.set_nonce(nonce_mgr.get_next_nonce());
    queue.add_transaction(tx, wallet);
}
```

### Issue: Gas Price Too High

```cpp
// Set maximum acceptable gas price
optimizer.set_max_gas_price_gwei(300);

try {
    auto gas_params = optimizer.estimate_optimal_gas(tx);
    tx.set_gas_price(gas_params.gas_price);
} catch (const openclaw::GasPriceTooHighException& e) {
    std::cerr << "Gas price exceeds limit: " << e.what() << std::endl;
    // Retry with lower priority or wait
}
```

### Issue: Memory Leaks in Long-Running Applications

```cpp
// Properly cleanup resources
{
    openclaw::TransactionQueue queue(router);
    // ... use queue
} // Queue destructor called, resources freed

// Or explicitly clear
queue.clear_completed_transactions();
router.close_idle_connections();
```

### Debug Logging

```cpp
#include <openclaw/logger.h>

openclaw::Logger::set_level(openclaw::LogLevel::DEBUG);
openclaw::Logger::enable_file_output("openclaw_debug.log");

// Logs will show detailed RPC calls, transaction construction, etc.
```

## Advanced Usage

### Custom Gas Strategy Implementation

```cpp
#include <openclaw/gas_strategy.h>

class CustomGasStrategy : public openclaw::IGasStrategy {
public:
    openclaw::GasParams calculate(const openclaw::NetworkState& state) override {
        openclaw::GasParams params;
        // Implement custom logic
        params.gas_price = state.base_fee * 1.2;
        params.priority_fee = state.median_priority_fee * 0.8;
        return params;
    }
};

optimizer.set_custom_strategy(std::make_unique<CustomGasStrategy>());
```

### Transaction Simulation Before Submission

```cpp
#include <openclaw/simulator.h>

openclaw::Simulator sim(router);
auto result = sim.simulate_transaction(tx, wallet.get_address());

if (result.will_revert()) {
    std::cerr << "Transaction will revert: " << result.get_revert_reason() << std::endl;
} else {
    std::cout << "Estimated gas used: " << result.gas_used() << std::endl;
    queue.add_transaction(tx, wallet);
}
```

This skill provides comprehensive coverage of the Openclaw-AI-Metamask-26 framework for building robust EVM wallet automation systems.

# IBC Implementation with Hermes Relayer

This repository demonstrates a simple Inter-Blockchain Communication (IBC) setup between two Cosmos SDK-based chains, `chainA` and `chainB`, using Hermes Relayer.

---

## Prerequisites

1. **Go (>=1.20)**: [Install Go](https://go.dev/dl/)
2. **Rust and Cargo**: [Install Rust](https://rustup.rs/)
3. **Hermes Relayer**: [Install Hermes](https://hermes.informal.systems/)
4. **SimApp**: A Cosmos SDK-based binary (`simd`) to run the chains.
5. **gRPC and curl**: Tools to interact with chain endpoints.

---

## Setup Instructions

### **1. Clone the Repository**

```bash
git clone https://github.com/cosmos/ibc-go.git
cd ibc-go
git checkout v7.2.0
```

## Initialize Chains

Chain A

```bash
simd init chainA --chain-id chainA --home ~/.simapp/chainA
simd keys add alice --keyring-backend test --home ~/.simapp/chainA
simd add-genesis-account alice 100000000000stake --keyring-backend test --home ~/.simapp/chainA
simd gentx alice 100000000stake --chain-id chainA --keyring-backend test --home ~/.simapp/chainA
simd collect-gentxs --home ~/.simapp/chainA
```

Chain B

```bash
simd init chainB --chain-id chainB --home ~/.simapp/chainB
simd keys add bob --keyring-backend test --home ~/.simapp/chainB
simd add-genesis-account bob 100000000000stake --keyring-backend test --home ~/.simapp/chainB
simd gentx bob 100000000stake --chain-id chainB --keyring-backend test --home ~/.simapp/chainB
simd collect-gentxs --home ~/.simapp/chainB
```

### Configure the chainA and chainB config

* Chain A (~/.simapp/chainA/config/config.toml):

```bash

[rpc]
laddr = "tcp://127.0.0.1:26657"

[p2p]
laddr = "tcp://0.0.0.0:26656"

[grpc]
address = "0.0.0.0:9090"
enable = true
```

* Chain B (~/.simapp/chainB/config/config.toml):

```bash
[rpc]
laddr = "tcp://127.0.0.1:36657"

[p2p]
laddr = "tcp://0.0.0.0:36656"

[grpc]
address = "0.0.0.0:9190"
enable = true
```

### Start Chain

```bash
simd start --home ~/.simapp/chainA
simd start --home ~/.simapp/chainB
```

## Confgiure Hermes

```bash
mkdir -p ~/.hermes
nano ~/.hermes/config.toml
```

# Convert and Add Mnemonics to Hermes

### Export Mnemonic ChainA and ChainB

```bash
simd keys mnemonic alice --keyring-backend test --home ~/.simapp/chainA > ~/.simapp/chainA/alice-mnemonic.txt

simd keys mnemonic alice --keyring-backend test --home ~/.simapp/chainA > ~/.simapp/chainA/alice-mnemonic.txt
```

### Add Mnemonics to Hermes

```bash
hermes keys add --chain chainA --mnemonic-file ~/.simapp/chainA/alice-mnemonic.txt
hermes keys add --chain chainB --mnemonic-file ~/.simapp/chainB/bob-mnemonic.txt
```

## Running Hermes Relayer

### Start Hermes

```bash
hermes start
```

## Establish IBC Connections

```bash
hermes create client chainA --host-chain chainB
hermes create client chainB --host-chain chainA
```

### Create a Connection

```bash
hermes create connection chainA --to chainB
```

### Create a Channel

```bash
hermes create channel chainA --to chainB --port transfer --channel-version ics20-1
```

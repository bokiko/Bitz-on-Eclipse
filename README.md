# Mining $BITZ on Eclipse via CLI
## Complete Guide to Mining $BITZ Tokens via ePOW

This repository contains a step-by-step guide for setting up and mining BITZ tokens using the Eclipse Proof of Work (ePOW) system.

## What is $BITZ?

- The first ePOW commodity token that anyone can mine on Eclipse
- CPU mining network on Eclipse (testnet)
- 5 million max supply
- Not related to $ES
- NOT pre-mined + ZERO team/insider allocations
- You can mine it using Solana CLI or wait for the web app miner UI

### Token Information

- **Token Address**: [$BITZ (64mggk2nXg6vHC1qCdsZdEFzd5QGN4id54Vbho4PswCF)](https://eclipsescan.xyz/token/64mggk2nXg6vHC1qCdsZdEFzd5QGN4id54Vbho4PswCF)
- **Price Chart**: [GeckoTerminal](https://www.geckoterminal.com/eclipse/pools/CUzwQT8ZThYuKCerH3SpNu12eyxB4tAU3d19snjhELWU)
- **Trade on**: [Orca](https://www.orca.so/?chainId=eclipse&tokenIn=64mggk2nXg6vHC1qCdsZdEFzd5QGN4id54Vbho4PswCF&tokenOut=So11111111111111111111111111111111111111112)

⚠️ **IMPORTANT**: Everything is experimental and subject to change. Always verify token addresses and websites before interacting with them. Stay vigilant and practice proper security measures when trading or mining.

## Table of Contents
- [What is $BITZ?](#what-is-bitz)
  - [Token Information](#token-information)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Wallet Setup](#wallet-setup)
  - [Option 1: Create a New Solana Wallet](#option-1-create-a-new-solana-wallet)
  - [Option 2: Use an Existing Solana Wallet](#option-2-use-an-existing-solana-wallet)
- [Configuring Solana for Eclipse](#configuring-solana-for-eclipse)
- [Backpack Wallet Integration](#backpack-wallet-integration)
- [Mining Configuration](#mining-configuration)
- [Starting the Miner](#starting-the-miner)
  - [Using tmux](#option-1-using-tmux-recommended)
  - [Using screen](#option-2-using-screen)
- [Managing Your BITZ](#managing-your-bitz)
- [Troubleshooting](#troubleshooting)
- [Optimization Tips](#optimization-tips)
- [Safety Disclaimer](#safety-disclaimer)

## Prerequisites
- Ubuntu Linux system (20.04 LTS or newer)
- Root access or sudo privileges
- Internet connection
- A minimum of 0.005 ETH on Eclipse network

## Installation

### Step 1: Update System and Install Required Packages

First, let's update your system and install all the necessary applications:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential pkg-config libssl-dev clang nano tmux screen htop net-tools git curl wget python3-pip unzip
```

This installs all essential tools including:
- nano and vim (text editors)
- tmux and screen (terminal multiplexers)
- htop (system monitoring)
- net-tools (network utilities)
- git, curl, wget (download utilities)
- python3-pip (Python package manager)
- unzip (file extraction)

If you encounter Python apt_pkg errors during the update:
```bash
sudo apt-get update --fix-missing
sudo apt-get install --reinstall python3-apt
```

### Step 2: Install Rust
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
Follow the prompts (select option 1 for default installation). Once installed, load Rust into your current shell:
```bash
source $HOME/.cargo/env
```

### Step 3: Install Solana CLI
```bash
sh -c "$(curl -sSfL https://release.solana.com/v1.18.2/install)"
```
After installation, update your PATH:
```bash
export PATH="$HOME/.local/share/solana/install/active_release/bin:$PATH"
echo 'export PATH="$HOME/.local/share/solana/install/active_release/bin:$PATH"' >> ~/.bashrc
```
Verify installation:
```bash
solana --version
```

### Step 4: Install BITZ CLI
```bash
cargo install bitz
```

## Wallet Setup

You have two options for setting up your wallet:

### Option 1: Create a New Solana Wallet

```bash
solana-keygen new --no-passphrase
```
This creates a keypair at `~/.config/solana/id.json` and displays your public key and seed phrase.

⚠️ **IMPORTANT:** Save your public key and seed phrase securely!

View your private key (for importing to wallets):
```bash
cat ~/.config/solana/id.json
```
This will display an array of numbers - that's your private key. Example output:
```
[12,......,144]
```

### Option 2: Use an Existing Solana Wallet

Instead of creating a new wallet, you can use an existing Solana wallet:

```bash
mkdir -p ~/.config/solana
nano ~/.config/solana/id.json
```

Paste your existing keypair (array of numbers) into the file, save and exit by pressing `Ctrl+X`, then `Y`, then `Enter`.

⚠️ **IMPORTANT:** Make sure the file contains valid JSON format (e.g., starts and ends with `[` and `]`, with numbers separated by commas).

Confirm your wallet address:
```bash
solana address
```
This will show the public key of the wallet you just loaded.

## Configuring Solana for Eclipse

### Step 5: Configure Solana for Eclipse

You have two options for the Eclipse RPC endpoint:

#### Option 1: Primary Eclipse RPC
```bash
solana config set --url https://mainnetbeta-rpc.eclipse.xyz/
```

#### Option 2: Alternative Eclipse RPC
```bash
solana config set --url https://bitz-000.eclipserpc.xyz/
```

Choose one of these endpoints. If you experience connection issues with one, you can try the other.

### Step 6: Fund Your Eclipse Wallet

Before mining, you need at least 0.005 ETH on Eclipse network.

Get your public key:
```bash
solana address
```

Transfer at least 0.005 ETH to this address on the Eclipse network. You can use a bridge to move ETH from Ethereum mainnet or Layer 2 networks to Eclipse via https://app.eclipse.xyz/bridge or https://www.relay.link/bridge/eclipse.

## Backpack Wallet Integration

### Method 1: Import Wallet Using Private Key Array

1. Copy your private key array from:
   ```bash
   cat ~/.config/solana/id.json
   ```

2. In Backpack wallet:
   - Go to Settings > Wallets
   - Select "Import wallet"
   - Choose "Import private key"
   - Paste the array of numbers from your Solana keypair

### Method 2: Import Wallet Using Seed Phrase

If you created a new wallet with `solana-keygen new`, you received a seed phrase. Use this to import your wallet to Backpack:

1. In Backpack wallet:
   - Go to Settings > Wallets
   - Select "Import wallet"
   - Choose "Import seed phrase"
   - Enter the seed phrase you saved during wallet creation

FYI, Backpack wallet supports Eclipse network natively.

## Mining Configuration

Before starting the mining process, you can configure how many CPU cores to use:

```bash
# Set the number of cores based on your system (example uses 8 cores)
bitz collect --cores 8
```

For optimal performance, leave 1-2 cores free for system operations.

## Starting the Miner

### Step 7: Start Mining Using a Terminal Multiplexer

You can use either tmux or screen to keep your mining process running even after disconnecting from your terminal.

#### Option 1: Using tmux (Recommended)

Start a new tmux session:
```bash
tmux new -s eclipse
```

Begin mining BITZ:
```bash
bitz collect
```

Managing your tmux session:
- To detach from the tmux session (keep mining in background):
  - Press `Ctrl+B` then `D`
- To reattach to the session later:
  ```bash
  tmux attach -t eclipse
  ```
- To list all active tmux sessions:
  ```bash
  tmux ls
  ```
- To terminate the mining session:
  - Reattach to the tmux session, then press `Ctrl+C`
- Basic tmux commands:
  - `Ctrl+B` then `%` - Split window vertically
  - `Ctrl+B` then `"` - Split window horizontally
  - `Ctrl+B` then arrow keys - Navigate between panes
  - `Ctrl+B` then `c` - Create a new window
  - `Ctrl+B` then `n` - Go to next window
  - `Ctrl+B` then `p` - Go to previous window

#### Option 2: Using screen

Start a new screen session:
```bash
screen -S eclipse
```

Begin mining BITZ:
```bash
bitz collect
```

Managing your screen session:
- To detach from the screen session (keep mining in background):
  - Press `Ctrl+A` then `D`
- To reattach to the session later:
  ```bash
  screen -r eclipse
  ```
- To list all active screen sessions:
  ```bash
  screen -ls
  ```
- To terminate the mining session:
  - Reattach to the screen session, then press `Ctrl+C`

### Setting Up Automatic Mining on Restart

To ensure mining starts automatically when your VM restarts:

1. Create a startup script:
   ```bash
   nano ~/start_eclipse_mining.sh
   ```

2. Add the following content:
   ```bash
   #!/bin/bash
   export PATH="$HOME/.local/share/solana/install/active_release/bin:$PATH"
   tmux new-session -d -s eclipse 'bitz collect --cores 8'
   ```

3. Make the script executable:
   ```bash
   chmod +x ~/start_eclipse_mining.sh
   ```

4. Add it to crontab:
   ```bash
   crontab -e
   ```
   
5. Add this line (choose nano as the editor when prompted):
   ```
   @reboot ~/start_eclipse_mining.sh
   ```
   Save and exit by pressing `Ctrl+X`, then `Y`, then `Enter`.

## Managing Your BITZ

Once mining is active, you can manage your BITZ tokens with these commands:

### Check Your Balance
```bash
bitz account
```

### Claim Your Mined Tokens
```bash
bitz claim
```

### View All Available Commands
```bash
bitz -h
```

## Troubleshooting

### Common Issues and Solutions

| Issue | Solution |
|-------|----------|
| Command Not Found Errors | Reinstall Python packages: `sudo apt-get install --reinstall python3-apt` |
| Mining Not Starting | Check ETH balance (min 0.005 ETH required) |
| solana-keygen Not Found | Verify PATH settings: `echo $PATH` and ensure Solana binaries are included |
| Connection Issues | Verify Eclipse RPC endpoint: `solana config get` |
| Low Mining Rate | Check CPU usage, network connection, and consider adjusting cores |

### Getting Your Node IP
To find your node's IP address:
```bash
hostname -I | awk '{print $1}'
```

### Verifying Eclipse RPC Connection
```bash
solana config get
```
The URL should be set to either `https://mainnetbeta-rpc.eclipse.xyz/` or `https://bitz-000.eclipserpc.xyz/` depending on which RPC endpoint you chose

## Optimization Tips

- **System Resources**: Allocate appropriate CPU cores with the `--cores` flag
- **Uptime**: Use tmux to maintain mining sessions during disconnections
- **Monitoring**: Set up basic system monitoring to track performance
- **Thermal Management**: Ensure proper cooling for extended mining sessions
- **Automation**: Consider setting up auto-claim scripts for collected BITZ
## Safety Disclaimer

⚠️ **IMPORTANT**: 

This guide is provided for informational purposes only. The Eclipse ecosystem and BITZ token are experimental projects that may change or be subject to various risks:

- Always verify token addresses before interacting with them
- Double-check website URLs to avoid phishing attempts
- Use only trusted bridges when moving funds across chains
- Never share your private keys or seed phrases with anyone
- Start with small amounts when testing new platforms
- Be aware that experimental projects carry higher risks
- Mining rewards and token values can fluctuate significantly

The developers of this guide or BITZ token cannot be held responsible for any losses that may occur. Participate at your own risk and only invest what you can afford to lose.

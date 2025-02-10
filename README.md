# Merkle Airdrop & Bagel Token

This repository contains a suite of Solidity contracts and Foundry scripts that together implement a Merkle tree–based airdrop system. The system uses a combination of Merkle proofs and EIP-712 signatures to securely distribute ERC20 tokens to eligible addresses.

---

## Table of Contents

- [Overview](#overview)
- [Contracts](#contracts)
- [Scripts](#scripts)
- [Testing](#testing)
- [Installation & Setup](#installation--setup)
- [Usage](#usage)
  - [Deploying Contracts](#deploying-contracts)
  - [Generating Merkle Input Data](#generating-merkle-input-data)
  - [Building the Merkle Tree & Proofs](#building-the-merkle-tree--proofs)
  - [Claiming the Airdrop](#claiming-the-airdrop)
- [File Structure](#file-structure)
- [License](#license)

---

## Overview

The project implements an airdrop mechanism where a fixed amount of tokens is distributed to a list of whitelisted users. Eligible users must provide both a valid Merkle proof (to verify inclusion in the whitelist) and a valid EIP-712 signature (to prove ownership of the claiming address) to claim their tokens. The repository leverages Foundry for deployment, scripting, and testing, as well as OpenZeppelin’s audited contracts for ERC20 and cryptographic functions.

---

## Contracts

### BagelToken.sol

- **Purpose:** An ERC20 token contract named "Bagel" (symbol: BAGEL) with minting capabilities.
- **Key Features:**
  - Inherits from OpenZeppelin’s `ERC20` and `Ownable` contracts.
  - The owner can mint new tokens.

### MerkleAirdrop.sol

- **Purpose:** Implements the airdrop logic using a Merkle tree to verify eligible claimants.
- **Key Features:**
  - Uses Merkle proofs (via OpenZeppelin’s `MerkleProof`) to confirm whitelist membership.
  - Verifies EIP-712 signatures to authenticate claims.
  - Prevents multiple claims from the same address.
  - Emits events for successful claims and updates.

---

## Scripts

### DeployMerkleAirdrop.s.sol

- **Purpose:** Deploys the `BagelToken` and `MerkleAirdrop` contracts.
- **Workflow:**
  - Deploys a new `BagelToken` contract.
  - Deploys `MerkleAirdrop` with a predefined Merkle root.
  - Mints tokens and transfers them to the airdrop contract for distribution.

### GenerateInput.s.sol

- **Purpose:** Generates a JSON file (`input.json`) containing whitelist data.
- **Workflow:**
  - Defines an array of whitelisted addresses.
  - Sets a fixed token amount (25 tokens per user).
  - Writes the output JSON to `/script/target/input.json`.

### MakeMerkle.s.sol

- **Purpose:** Reads the generated input JSON file, constructs the Merkle tree, and generates proofs.
- **Workflow:**
  - Reads the input file for whitelist entries.
  - Computes Merkle tree leaves and the corresponding proofs using the [Murky](https://github.com/dmfxyz/murky) library.
  - Outputs the Merkle root, proofs, and leaf nodes in `/script/target/output.json`.

### Interact.s.sol

- **Purpose:** Simulates a user claiming their airdrop.
- **Workflow:**
  - Retrieves the most recent `MerkleAirdrop` deployment.
  - Generates a valid EIP-712 signature for the claiming address.
  - Calls the `claim` function on the `MerkleAirdrop` contract with the necessary parameters (address, amount, proof, and signature).

---

## Testing

### MerkleAirdrop.t.sol

- **Purpose:** Contains Foundry tests to validate the airdrop functionality.
- **Key Features:**
  - Sets up the testing environment with both local and zkSync chain considerations.
  - Verifies that an eligible user can claim tokens successfully.
  - Checks that token balances are correctly updated after a claim.
  - Ensures that claims cannot be executed more than once per address.

---

## Installation & Setup

1. **Clone the Repository:**

   ```bash
   git clone <repository-url>
   cd <repository-folder>
   ```

2. **Install Foundry:**

   If you haven't already installed Foundry, run:

   ```bash
   curl -L https://foundry.paradigm.xyz | bash
   foundryup
   ```

3. **Install Dependencies:**

   This project depends on several libraries including OpenZeppelin contracts, forge-std, murky, and foundry-devops. You can install them using Foundry’s package manager:

   ```bash
   forge install OpenZeppelin/openzeppelin-contracts
   forge install foundry-rs/forge-std
   forge install dmfxyz/murky
   forge install foundry-rs/foundry-devops
   ```

4. **Compile the Contracts:**

   ```bash
   forge build
   ```

---

## Usage

### Deploying Contracts

To deploy the `BagelToken` and `MerkleAirdrop` contracts, run:

```bash
forge script script/DeployMerkleAirdrop.s.sol --broadcast --verify
```

This script will:
- Deploy the `BagelToken`.
- Deploy the `MerkleAirdrop` with a preset Merkle root.
- Mint tokens and transfer them to the airdrop contract.

### Generating Merkle Input Data

Generate the input JSON file containing whitelist information:

```bash
forge script script/GenerateInput.s.sol --broadcast
```

The generated file will be located at `/script/target/input.json`.

### Building the Merkle Tree & Proofs

Generate the Merkle tree and corresponding proofs by running:

```bash
forge script script/MakeMerkle.s.sol --broadcast
```

The output will be saved in `/script/target/output.json`.

### Claiming the Airdrop

To simulate a user claiming their airdrop tokens, run:

```bash
forge script script/Interact.s.sol --broadcast
```

This script will:
- Retrieve the latest deployed `MerkleAirdrop` contract.
- Generate a valid signature for the user.
- Execute the claim function with the correct Merkle proof and signature.

---

## File Structure

```
├── src
│   ├── BagelToken.sol            # ERC20 token contract with minting functionality
│   └── MerkleAirdrop.sol          # Airdrop contract using Merkle proofs and EIP-712 signatures
├── script
│   ├── DeployMerkleAirdrop.s.sol  # Deployment script for contracts
│   ├── GenerateInput.s.sol        # Script to generate JSON input for the Merkle tree
│   ├── MakeMerkle.s.sol           # Script to build the Merkle tree and generate proofs
│   └── Interact.s.sol             # Script to simulate claiming an airdrop
├── test
│   └── MerkleAirdrop.t.sol        # Foundry tests for airdrop functionality
└── README.md                      # This README file
```

---

## License

This project is licensed under the [MIT License](LICENSE).

---

Happy coding and airdropping! If you have any questions or suggestions, feel free to open an issue or reach out.

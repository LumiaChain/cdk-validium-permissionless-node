# Lumia Permissionless Node

This repository contains a reference docker-compose to run a Lumia permissionless RPC node.

This work is based on docker-compose provided in [cdk-erigon repository](https://github.com/0xPolygonHermez/cdk-erigon).

## Recommended hardware

- Linux OS
- Any modern multicore CPU
- At least 32GiB RAM
- 1TB NVMe SSD

## How to use

- Copy `env.example` to `.env` and update with valid L1 RPC URL (recommended to use a premium provider)

  ```bash
  cp env.example .env
  ```

- Configure the `.env` file for your desired network:

  - For **Lumia Beam Testnet** (default):

    ```.env
    NETWORK_ENV=testnet
    NETWORK_PREFIX=beam
    CDK_ERIGON_IMAGE=ghcr.io/0xpolygon/cdk-erigon:v2.64.0-RC10
    CDK_ERIGON_L1_RPC_URL=<your-sepolia-rpc-url>
    CDK_ERIGON_L2_DATASTREAMER_URL=16.170.119.192:6900
    CDK_ERIGON_ZKEVM_L2_SEQUENCER_RPC_URL=https://beam-rpc.lumia.org
    ```

  - For **Lumia Prism Mainnet**:

    ```.env
    NETWORK_ENV=mainnet
    NETWORK_PREFIX=prism
    CDK_ERIGON_IMAGE=hermeznetwork/cdk-erigon:v2.61.24
    CDK_ERIGON_L1_RPC_URL=<your-ethereum-mainnet-rpc-url>
    CDK_ERIGON_L2_DATASTREAMER_URL=16.171.93.208:6900
    CDK_ERIGON_ZKEVM_L2_SEQUENCER_RPC_URL=https://prism-rpc.lumia.org
    ```

  > **Note:** The testnet image (`v2.64.0-RC10`) is not compatible with old data directories—either resync the RPC from scratch or download a fresh snapshot.

- The configuration includes:

  - `NETWORK_ENV`: Set to `testnet` or `mainnet`
  - `NETWORK_PREFIX`: Set to `beam` (testnet) or `prism` (mainnet)
  - `CDK_ERIGON_IMAGE`: Docker image for the network
  - Network-specific RPC endpoints for L1 and L2

- (Optional) Download chaindata snapshot for faster sync:

  **Lumia Beam Testnet:**

  ```bash
  # Download the snapshot
  wget https://cdn.lumia.org/chaindata/lumia-beam-testnet-rpc.tar.gz

  # Verify checksum (recommended)
  echo "0029185abb4d7f7c1ff53341bd4e88330f7560558d6373845f77b24d4fa975e7  lumia-beam-testnet-rpc.tar.gz" | sha256sum -c

  # Extract to data directory
  mkdir -p data/testnet
  tar -xzf lumia-beam-testnet-rpc.tar.gz -C data/testnet
  ```

  | File | Checksum |
  |------|----------|
  | `lumia-beam-testnet-rpc.tar.gz` | **SHA256:** `0029185abb4d7f7c1ff53341bd4e88330f7560558d6373845f77b24d4fa975e7` |
  | | **MD5:** `145255060ce4638aeff88cdd970389c2` |

  **Lumia Prism Mainnet:** Snapshot will be made available soon.

- Run `docker compose up -d`

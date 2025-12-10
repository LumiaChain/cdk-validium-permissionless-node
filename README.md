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

  - For **testnet** (default): Update `CDK_ERIGON_L1_RPC_URL`, `CDK_ERIGON_L2_DATASTREAMER_URL` and `CDK_ERIGON_ZKEVM_L2_SEQUENCER_RPC_URL` with a Sepolia endpoint

  - For **mainnet**: Replace at the top of the `.env` file `testnet` with `mainnet` and update `CDK_ERIGON_L1_RPC_URL`, `CDK_ERIGON_L2_DATASTREAMER_URL` and `CDK_ERIGON_ZKEVM_L2_SEQUENCER_RPC_URL` with a Mainnet endpoint

  - Add `CDK_ERIGON_IMAGE` to the `.env` file according to the network:

    ```.env
    # For testnet
    CDK_ERIGON_IMAGE=ghcr.io/0xpolygon/cdk-erigon:v2.64.0-RC10
    # For mainnet, use the following line instead
    # CDK_ERIGON_IMAGE=hermeznetwork/cdk-erigon:v2.61.24
    ```
    Testnet note: this newer image is not compatible with old data directories—either resync the RPC from scratch or download a fresh snapshot (S3 link to be provided).

- The configuration includes:

  - `NETWORK_ENV`: Set to either `testnet` or `mainnet`
  - `CDK_ERIGON_IMAGE`: Set according to your network (see above)
  - Network-specific RPC endpoints for L1 and L2

- Run `docker compose up`

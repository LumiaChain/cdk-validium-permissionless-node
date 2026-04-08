# Lumia Permissionless Node

This repository contains a reference docker-compose to run a Lumia permissionless RPC node.

This work is based on docker-compose provided in [cdk-erigon repository](https://github.com/0xPolygonHermez/cdk-erigon).

## Recommended hardware

- Linux or macOS (Windows via WSL2/Docker Desktop)
- Any modern multicore CPU
- At least 32GiB RAM
- 1TB NVMe SSD (mainnet snapshot + `chaindata` need substantial free space)

## How to use

### 1. Environment file

From the repository root:

```bash
cp .env.example .env
```

If you already have a working `.env`, keep it and confirm it defines **`NETWORK_ENV`**, **`CHAIN_CONFIG_BASENAME`**, **`CDK_ERIGON_*`**, and **`CDK_ERIGON_IMAGE`** as in the examples below.

Edit `.env` for the network you want. Compose loads `config/${NETWORK_ENV}/${CHAIN_CONFIG_BASENAME}-*.{yaml,json}` into the container.

**Lumia Beam Testnet** (default in `.env.example`):

```env
NETWORK_ENV=testnet
CHAIN_CONFIG_BASENAME=dynamic-lumia-beam-testnet
CDK_ERIGON_IMAGE=ghcr.io/0xpolygon/cdk-erigon:v2.65.0-RC2
CDK_ERIGON_L1_RPC_URL=<your-sepolia-rpc-url>
CDK_ERIGON_L2_DATASTREAMER_URL=rpc-ds.lumia-beam-testnet.gateway.fm:6902
CDK_ERIGON_ZKEVM_L2_SEQUENCER_RPC_URL=https://beam-rpc.lumia.org
```

**Lumia Prism Mainnet** (`CHAIN_CONFIG_BASENAME` must stay `dynamic-prism` so it matches `chain:` in `config/mainnet/dynamic-prism-config.yaml` and the published CDN snapshot `chaindata`):

```env
NETWORK_ENV=mainnet
CHAIN_CONFIG_BASENAME=dynamic-prism
CDK_ERIGON_IMAGE=ghcr.io/0xpolygon/cdk-erigon:v2.65.0-RC2
CDK_ERIGON_L1_RPC_URL=<your-ethereum-mainnet-rpc-url>
CDK_ERIGON_L2_DATASTREAMER_URL=rpc-ds.prism.gateway.fm:6903
CDK_ERIGON_ZKEVM_L2_SEQUENCER_RPC_URL=https://mainnet-rpc.lumia.org
```

> **Note:** Image `v2.65.0-RC2` is not compatible with arbitrary old `chaindata` trees—use a matching snapshot or sync from an empty `data/<network>`.

### 2. (Optional) Mainnet snapshot for faster sync

Only for **Prism mainnet**. Skip this if you will sync from an empty `data/mainnet`.

1. Download (~20.4 GiB). Prefer `curl` on macOS; use `wget` on Linux if you prefer.

   ```bash
   curl -fL -o lumia-prism-mainnet-rpc.tar.gz "https://cdn.lumia.org/chaindata/lumia-prism-mainnet-rpc.tar.gz"
   ```

   If the download stops early, resume:

   ```bash
   curl -fL -C - -o lumia-prism-mainnet-rpc.tar.gz "https://cdn.lumia.org/chaindata/lumia-prism-mainnet-rpc.tar.gz"
   ```

2. Verify SHA256:

   ```bash
   echo "7d47466190113708487a077c7242354036e99bfabd76a9598f9963eb94a5864a  lumia-prism-mainnet-rpc.tar.gz" | shasum -a 256 -c
   ```

   Or: `shasum -a 256 lumia-prism-mainnet-rpc.tar.gz` and compare to the value in the table below.

3. Extract. The archive has a top-level `prism-rpc/` directory; strip it so the datadir matches `datadir: /data` → host `data/mainnet`:

   ```bash
   mkdir -p data/mainnet
   tar -xzf lumia-prism-mainnet-rpc.tar.gz --strip-components=1 -C data/mainnet
   ```

   You should see `data/mainnet/chaindata` and related folders. After a good extract you can delete `lumia-prism-mainnet-rpc.tar.gz` to reclaim disk space.

| File | Size (bytes) | SHA256 |
| --- | ---:| --- |
| `lumia-prism-mainnet-rpc.tar.gz` | 21868767456 (~20.4 GiB) | `7d47466190113708487a077c7242354036e99bfabd76a9598f9963eb94a5864a` |

If you see **`MDBX_CORRUPTED`** or metadata “used-bytes beyond filesize”, the extract likely ran out of disk space—free space, remove `data/mainnet`, and extract again (or start empty and sync from the datastream).

### 3. Start the node

From the repository root:

```bash
docker compose up -d
```

Use Docker Compose V2 (`docker compose`). Older installs may use `docker-compose up -d`.

Published ports: **8545** (HTTP RPC), **8546** (WebSocket), **9000** (datastream), **9090** (metrics).

### 4. Check that it is healthy

```bash
docker compose ps
docker compose logs rpc --tail 80
```

Follow logs live (Ctrl+C to stop):

```bash
docker compose logs -f rpc
```

Quick RPC check (Prism mainnet chain id `994873017` = `0x3b4c8eb9`):

```bash
curl -s -X POST -H 'Content-Type: application/json' \
  --data '{"jsonrpc":"2.0","method":"eth_chainId","params":[],"id":1}' \
  http://127.0.0.1:8545
```

Stop and remove the container (data under `data/` is kept):

```bash
docker compose down
```

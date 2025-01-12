# Lumia Permissionless Node

This repository contains a reference docker-compose to run a Lumia permissionless RPC node.

This work is based on docker-compose provided in [cdk-erigon repository](https://github.com/0xPolygonHermez/cdk-erigon).

## Recommended hardware

* Linux OS
* Any modern multicore CPU
* At least 32GiB RAM
* 1TB NVMe SSD

## How to use

* Copy `.env.example` to `.env` and update with valid L1 RPC URL (recommended to use a premium provider)
* Set `NETWORK_ENV` in `.env` file (or in docker-compose.yml) to either `testnet` or `mainnet`
* Run `docker-compose up`

## Upgrading cdk-validium-node version

It is suggested to stop rpc and synchronizer services and make a backup copy of the databases before performing the upgrade.

Check that the versions of the components you plan to apply corresponds to [CDK Version compatibility matrix](https://docs.polygon.technology/cdk/version-matrix/).

To perform the upgrade simply change the docker image version of needed components and start all services.
Please note that reverting back from upgraded to previous version may fail because newer version can apply migrations to the database schema.

# Lumia Permissionless Node

This repository contains a reference docker-compose to run a Lumia  permissionless node.

This work is based on docker-compose provided in [cdk-validium-node repository](https://github.com/0xPolygon/cdk-validium-node).

## Recommended hardware
* Linux OS
* Multicore Intel x64 CPU
* At least 32GiB RAM
* SSD storage for databases depends on the network size but can take up to several terabytes

## How to use
* Update `config/genesis.json` with valid genesis for your rollup
* create `.env` file based on `env.example` content
* Set correct RPC URLs in `.env`
* If running forkId other than 9, set correct docker image tags for `cdk-validium-node` and `zkevm-prover` - Currently Lumia supports forkId 9
* [Optional] Set correct paths for database storage in `.env` file
* Run `docker-compose up`

## Upgrading cdk-validium-node version
It is suggested to stop rpc and synchronizer services and make a backup copy of the databases before performing the upgrade.
Check that the versions of the components you plan to apply corresponds to [CDK Version compatibility matrix](https://docs.polygon.technology/cdk/version-matrix/).
To perform the upgrade simply change the docker image version of needed components and start all services.
Please note that reverting back from upgraded to previous version may fail because newer version can apply migrations to the database schema.
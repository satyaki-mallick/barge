[![banner](https://raw.githubusercontent.com/oceanprotocol/art/master/github/repo-banner%402x.png)](https://oceanprotocol.com)

<h1 align="center">barge</h1>

🐳 Docker Compose files for the full Ocean Protocol stack. It's called "barge" because barges carry containers on the water.

---

**🐲🦑 THERE BE DRAGONS AND SQUIDS. This is in alpha state and you can expect running into problems. If you run into them, please open up [a new issue](https://github.com/oceanprotocol/docker-images/issues). 🦑🐲**

---

## Table of Contents

  - [Prerequisites](#prerequisites)
  - [Get Started](#get-started)
  - [Script Options](#script-options)
  - [Docker Building Blocks](#docker-building-blocks)
    - [Pleuston](#pleuston)
    - [Aquarius](#aquarius)
    - [Brizo](#brizo)
    - [Keeper Node](#keeper-node)
    - [Secret Store](#secret-store)
  - [Spree Network](#spree-network)
  - [Contributing](#contributing)
  - [License](#license)

---

## Prerequisites

You need to have the newest versions of:

* [Docker](https://www.docker.com/get-started)
* [Docker Compose](https://docs.docker.com/compose/)
* Linux or macOS. Currently Windows is not supported.
* If you want to use Azure Storage with Brizo (and you might not), then you must edit the file [`brizo.env`](./brizo.env) to have your Azure credentials. To learn how to get those credentials, see our tutorial to [Set up Azure Storage](https://docs.oceanprotocol.com/tutorials/azure-for-brizo/).

## Get Started

It's overkill, but to be _sure_ that you use exactly the Docker images and volumes you want, you can start by pruning all the Docker things in your system:

```bash
docker system prune --all --volumes
```

(An alternative would be to add the options `--purge` and `--force-pull` in your call to `./start_ocean.sh` below but that's not as sure as the above command.)

If you're new to Barge, it's best to pin components (e.g. Aquarius, Brizo and Keeper Contracts) to a set of specific versions known to work with a specific version of squid-py, squid-js, etc.
For example, if you do:

```bash
git clone git@github.com:oceanprotocol/barge.git
cd barge
./start_ocean.sh --squid-py-v0.4.2-compatible --no-pleuston --local-spree-node
```

then you'll freeze Aquarius, Brizo and Keeper Contracts at versions known to work with squid-py v0.4.2 (and Mantaray 0.4.0). The above command will also not run Pleuston (because squid-py users don't usually need to run Pleuston) and will run a local Spree node (explained more below).

<img width="535" alt="Welcome to Ocean Protocol" src="Welcome_to_Ocean_Protocol.png">

---

If you want to use the latest versions of Aquarius, Brizo, Keeper Contracts and Pleuston (with the Docker image tag `latest`, based on the `develop` branches), then include `--latest` in the call to `start_ocean.sh`. You can pin one or all of the versions (to something other than `latest` by setting some environment variables explicitly (e.g. `KEEPER_VERSION=v0.6.12`). Read the `start_ocean.sh` code for details.

If you don't pin the versions of components (with an option like `--latest` or `--squid-py-v0.4.2-compatible`), then the default is to use the Docker images with Docker image tag `stable` (based on the `master` branches). You can pin one or all of the versions (to something other than `stable` by setting some environment variables explicitly (e.g. `KEEPER_VERSION=v0.6.12`). Read the `start_ocean.sh` code for details.

If you don't specify what kind of local node to run (e.g. using `--local-spree-node`) then the default is to run a local Nile node (i.e. `--local-nile-node`).

## Script Options

The `./start_ocean.sh` script provides the following options:

Option                      | Description
----------------------------| -----------
`--latest`                  | `latest` means to use the latest `develop` branches.
`--squid-py-v0.4.2-compatible` | Use versions of Aquarius, Brizo and Keeper Contracts known to be compatible with squid-py v0.4.2.
`--force-pull`              | Force pulling the latest revision of the used Docker images.
`--no-pleuston`             | Start up Ocean without the `pleuston` Building Block. Helpful for development on `pleuston`.
`--no-aquarius`             | Start up Ocean without the `aquarius` Building Block.
`--no-brizo`                | Start up Ocean without the `brizo` Building Block.
`--no-secret-store`         | Start up Ocean without the `secret-store` Building Block.
`--local-ganache-node`      | Runs a local `ganache` node.
`--local-spree-node`        | Runs a node of the local `spree` network.
`--local-nile-node`         | Runs a node of the `nile` network and connects the node to the `nile` network.
`--local-kovan-node`        | Runs a light node of the `kovan` network and connects the node to the `kovan` network.
`--reuse-ganache-database`  | Configures a running `ganache` node to use a persistent database.
`--acl-contract`            | Configures secret-store `acl_contract` option to enable secret-store authorization.
`--purge`                   | Removes the containers, volumes, artifact folder and networks used by the script.

## Docker Building Blocks

Ocean compose consists of a set of building blocks that can be combined to form a local test environment. By default all building blocks will be started with the `./start_ocean.sh` script.

### Pleuston

By default it will start one container. If Pleuston is running, you can open the **Pleuston Frontend** application in your browser:

[http://localhost:3000](http://localhost:3000)

This Building Block can be disabled by setting the `--no-pleuston` flag.

Hostname   | External Port | Internal Url          | Local Url             | Description
-----------|---------------|-----------------------|-----------------------|--------------
`pleuston` | `3000`        | http://pleuston:3000  | http://localhost:3000 | [Pleuston](https://github.com/oceanprotocol/pleuston)

### Aquarius

By default it will start two containers. This Building Block can be disabled by setting the `--no-aquarius` flag.

Hostname   | External Port | Internal Url         | Local Url             | Description
-----------|---------------|----------------------|-----------------------|--------------
`aquarius` | `5000`        | http://aquarius:5000 | http://localhost:5000 | [Aquarius](https://github.com/oceanprotocol/aquarius)
`mongodb`  |               |                      |                       | MongoDB used by Aquarius

### Brizo

By default it will start one container. This Building Block can be disabled by setting the `--no-brizo` flag.

Hostname   | External Port | Internal Url       | Local Url             | Description
-----------|---------------|--------------------|-----------------------|--------------
`brizo`    | `8030`        | http://brizo:8030  | http://localhost:8030 | [Brizo](https://github.com/oceanprotocol/brizo)

### Keeper Node

Controlled by the `--local-*-node` config switches will start a container `keeper-node` that uses port `8545` to expose an rpc endpoint to the Ethereum Protocol.
You can find a detailed explanation of how to use this in the [script options](#script-options) section of this document.

Hostname      | External Port | Internal Url            | Local Url             | Description
--------------|---------------|-------------------------|-----------------------|--------------
`keeper-node` | `8545`        | http://keeper-node:8545 | http://localhost:8545 | An Ethereum RPC node

This node can be one of the following types (with the default being `nile`):

Node      | Description
----------|-------------
`ganache` | Runs a local [ganache-cli](https://github.com/trufflesuite/ganache-cli) node that is not persistent by default. The contracts from the desired `keeper-contracts` version will be deployed upon launch of this node.
`spree`   | Runs a local node of the Spree Network. See [Spree Network](#spree-network) for details. The contracts from the desired `keeper-contracts` version will be deployed upon launch of this node.
`nile`    | This is the default. Runs a local node of the Nile Network and connects to the [Nile Testnet](https://docs.oceanprotocol.com/concepts/testnets/#nile-testnet).
`kovan`   | Runs a local node of the Kovan Network and connects to the [Kovan Testnet](https://docs.oceanprotocol.com/concepts/testnets/#kovan-testnet).

### Secret Store

By default it will start three containers. This Building Block can be disabled by setting the `--no-secret-store` flag.

Hostname                    | External Ports   | Internal URL                          | Local URL              | Description
----------------------------|------------------|---------------------------------------|------------------------|--------------
`secret-store`              | `12000`, `32771` | http://secret-store:12000             | http://localhost:12000 | An instance of the Ocean Secret Store
`secret-store-cors-proxy`   | `12001`          | http://secret-store-cors-proxy:12001  | http://localhost:12001 | An NGINX proxy to enable CORS on the secret store
`secret-store-signing-node` | `9545`           | http://secret-store-signing-node:9545 | http://localhost:9545  | A Parity Ethereum node to `sign` messages for the secret store and to `decrypt` and `encrypt`

## Spree Network

If you run the `./start_ocean.sh` script with the `--local-spree-node` option (please see [Keeper Node](#keeper-node) section of this document for more details),
you will have available a keeper node in the local and private Spree Network with the following accounts enabled:

Account                                      | Type     | Password/Key                 | Balance
---------------------------------------------|----------|------------------------------|-----------------
`0x00Bd138aBD70e2F00903268F3Db08f2D25677C9e` | key      | node0                        | 1000000000 Ether
`0x068Ed00cF0441e4829D9784fCBe7b9e26D4BD8d0` | key      | secret                       | 1000000000 Ether
`0xA99D43d86A0758d5632313b8fA3972B6088A21BB` | key      | secret                       | 1000000000 Ether
`0xe2DD09d719Da89e5a3D0F2549c7E24566e947260` | mnemonic | [info here](#spree-mnemonic) | 1000000000 Ether
`0xBE5449a6A97aD46c8558A3356267Ee5D2731ab5e` | mnemonic | [info here](#spree-mnemonic) | 1000000000 Ether
`0xA78deb2Fa79463945C247991075E2a0e98Ba7A09` | mnemonic | [info here](#spree-mnemonic) | 1000000000 Ether
`0x02354A1F160A3fd7ac8b02ee91F04104440B28E7` | mnemonic | [info here](#spree-mnemonic) | 1000000000 Ether
`0xe17D2A07EFD5b112F4d675ea2d122ddb145d117B` | mnemonic | [info here](#spree-mnemonic) | 1000000000 Ether
`0xA32C84D2B44C041F3a56afC07a33f8AC5BF1A071` | mnemonic | [info here](#spree-mnemonic) | 1000000000 Ether
`0xFF3fE9eb218EAe9ae1eF9cC6C4db238B770B65CC` | mnemonic | [info here](#spree-mnemonic) | 1000000000 Ether
`0x529043886F21D9bc1AE0feDb751e34265a246e47` | mnemonic | [info here](#spree-mnemonic) | 1000000000 Ether
`0xe08A1dAe983BC701D05E492DB80e0144f8f4b909` | mnemonic | [info here](#spree-mnemonic) | 1000000000 Ether
`0xbcE5A3468386C64507D30136685A99cFD5603135` | mnemonic | [info here](#spree-mnemonic) | 1000000000 Ether

Use one of the above accounts to populate `PARITY_ADDRESS` and `PARITY_PASSWORD` in `brizo.env` file to avoid account `locked` issues from the keeper contracts.

### Spree Mnemonic

The accounts from type mnemonic can be access with this seedphrase:

`taxi music thumb unique chat sand crew more leg another off lamp`

## Contributing

See the page titled "[Ways to Contribute](https://docs.oceanprotocol.com/concepts/contributing/)" in the Ocean Protocol documentation.

## License

```text
Copyright 2018 Ocean Protocol Foundation

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

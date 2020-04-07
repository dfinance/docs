# DNode introduction

Introduction to **dnode** - dfinance node. 

## What's dnode

**Dnode** is a blockchain node of dfinance platform. Indeed **dnode** implements core functional of dfinance, reach consensus, securing chain with PoS, processing transactions, p2p connections, etc.

You can find dnode source code into our Github [repository](https://github.com/dfinance/dnode).

## Run your dnode

Currently, you can connect to the testnet and have your own local node synched. There are two possible ways to do it.

### Docker

Just use public docker-compose version of **dnode** and get locally installed dnode via docker.

For details look at the **[testnet-bootstrap](https://github.com/dfinance/testnet-bootstrap)** Github repository.

### Build from source

You also always can build **dnode** from source, for it you will have to fetch and build dnode from Github [repository](https://github.com/dfinance/dnode). 

Also, you have to do:

* Install **dvm** and **compiler**, from **[dmv](https://github.com/dfinance/dvm)** Github repository.
* Launch **dvm** and **compiler** on recommended ports (or configure your own ports both in **dnode** and **dvm**, **compiler**).

## Testnet configuration

First of all init your local **dnode** with name of your node:

```text
dnode init <name>
```

After, download testnet version of **genesis.json**:

```shell
rm ~/.dnode/config/genesis.json
curl rpc.testnet.dfinance.co:26657/genesis | jq '.result.genesis' | less > ~/.dnode/config/genesis.json
```

Also, open config file (*~/.dnode/config.toml*) find **"persistent_peers"** variable and replace with: 

```toml
persistent_peers = "53bf6ba6dc6ec8024a10392deeeacf75d39aeae8@rpc.testnet.dfinance.co:26656"
```

Also by building from sources and follow instruction into **dnode** repository you can run your own local testnet to experiment with **dnode** and **dfinance blockchain**, or for contribution. We always welcome your pull requests.

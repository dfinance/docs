# Dnode

**dnode** is a blockchain node of **dfinance** platform. **dnode** implements core functional of **dfinance**: reach consensus, securing chain with PoS, processing transactions, p2p connections, etc.

You can find dnode source code [in dnode Github repository](https://github.com/dfinance/dnode).

## Run your dnode

There are multiple ways of running your dnode. We've sorted them from easiest to more complicated.

### Join testnet with testnet-bootstrap

For fastest and simplest launch we recommend using [testnet-bootstrap repos](https://github.com/dfinance/testnet-bootstrap). We've created it to make testnet-node launch as easy as it can be. See 4-step launch guide in its [README](https://github.com/dfinance/testnet-bootstrap#dfinance-testnet-bootstrap).

### Docker Image

Pre-built docker image is available on Docker Hub: [here's the link](https://hub.docker.com/r/dfinance/dnode). It already includes binary file for dnode so if you feel like it - go on - try it yourself.

### Build from source

You can build **dnode** from source, to do so fetch and build dnode from [Github repository](https://github.com/dfinance/dnode), use latest stable tag from [releases](https://github.com/dfinance/dnode/releases) page.

After that you need to:

* Install **dvm** from [dvm repository](https://github.com/dfinance/dvm).
* Launch **dvm** with recommended port setting \(or configure your own ports in both dnode and dvm\).

## Testnet configuration \(for docker or manual run\)

First of all init your local **dnode** with moniker \(name\) of your node:

```text
dnode init <moniker>
```

After that download testnet version of `genesis.json`:

```bash
# remove default genesis created on init
rm ~/.dnode/config/genesis.json

# this solution requires 'jq' util to be installed
curl https://rpc.testnet.dfinance.co/genesis | jq '.result.genesis' > ~/.dnode/config/genesis.json
```

Now replace seeds in \(_~/.dnode/config.toml_\) with current testnet seed node:

```bash
seeds = "b57d87dac7a9f77fbb11eb0f137a922fcc44cf0c@pub.testnet.dfinance.co:26656"
```

**Important**: if you set up full-node, you must open `26656` port on your machine, otherwise your node will not be able to broadcast and receive data from other nodes by P2P.


Once you opened port, configure your external address in \(_~/.dnode/config.toml_\):

```bash
external_address="your_ip:26656"
```

More detailed instruction on how to build `dnode` from sources can be found in [dnode repository](https://github.com/dfinance/dnode). If want some more space for experiments you can also use `dnode` to launch your own local testnet.

If you'd like to contribute - [see contributors section](https://github.com/dfinance/dnode#contributors). If you have any questions feel free to open [new issue](https://github.com/dfinance/dnode/issues/new).


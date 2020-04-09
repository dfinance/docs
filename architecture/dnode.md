# dnode

**dnode** is a blockchain node of **dfinance** platform. **dnode** implements core functional of **dfinance**: reach consensus, securing chain with PoS, processing transactions, p2p connections, etc.

You can find dnode source code [in dnode Github repository](https://github.com/dfinance/dnode).

## Run your dnode

There are multiple ways of running your dnode. We've sorted them from easiest to more complicated.

### Join testnet with testnet-bootstrap

For fastest and simplest launch we recommend using [testnet-bootstrap repos](https://github.com/dfinance/testnet-bootstrap). We've created it to make testnet-node launch as easy as it can be. See 4-step launch guide in its [README](https://github.com/dfinance/testnet-bootstrap#dfinance-testnet-bootstrap).

### Docker Image

Pre-built docker image is available on Docker Hub: [here's the link](https://hub.docker.com/r/dfinance/dnode). It already includes binary file for dnode so if you feel like it - go on - try it yourself.

### Build from source

You can build **dnode** from source, to do so fetch and build dnode from [Github repository](https://github.com/dfinance/dnode).

After that you need to:

* Install **dvm** and **compiler**, from [dvm repository](https://github.com/dfinance/dvm).
* Launch **dvm** and **compiler** with recommended port setting (or configure your own ports in both dnode and dvm+compiler).

## Testnet configuration (for docker or manual run)

First of all init your local **dnode** with moniker (name) of your node:

```text
dnode init <moniker>
```

After that download testnet version of `genesis.json`:

```bash
# remove default genesis created on init
rm ~/.dnode/config/genesis.json

# this solution requires 'jq' util to be installed
curl rpc.testnet.dfinance.co:26657/genesis | jq '.result.genesis' > ~/.dnode/config/genesis.json
```

Then open config file (_~/.dnode/config.toml_) find `persistent_peers` line and replace it with:

```bash
persistent_peers = "53bf6ba6dc6ec8024a10392deeeacf75d39aeae8@rpc.testnet.dfinance.co:26656"
```

More detailed instruction on how to build `dnode` from sources can be found in [dnode repository](https://github.com/dfinance/dnode). If want some more space for experiments you can also use `dnode` to launch your own local testnet.

If you'd like to contribute - [see contributors section](https://github.com/dfinance/dnode#contributors).
If you have any questions feel free to open [new issue](https://github.com/dfinance/dnode/issues/new).

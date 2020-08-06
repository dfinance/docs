# Dncli

**dncli** \(dfinance node CLI\) is a CLI application developed to work with **dnode**. With dncli you can query blockchain data, post transactions, and query network status.

It comes as binary application and can be downloaded from [GitHub release page](https://github.com/dfinance/dnode/releases). Alternatively you can [build it from sources](https://github.com/dfinance/dnode).

## Usage

After installing **dncli**, it should be configured:

```bash
dncli config chain-id dn-testnet
dncli config output json
dncli config indent true
dncli config trust-node true
dncli config compiler tcp://127.0.0.1:50051
dncli config node http://127.0.0.1:26657
dncli config keyring-backend file
```

After configuring, you can try it:

```text
dncli version
dncli --help
```

**dncli** contains multiple commands for each **dnode** module.

There are two types of commands in **dncli**: `transaction` and `query`. Transaction commands start with `tx` prefix, query commands start with `query` prefix. Difference between them is that `tx` commands imply building and broadcasting transaction, whereas `query` simply queries data from dnode.

You can try it yourself and see available commands:

```bash
dncli tx --help
dncli query --help

dncli q --help # Short version of query.
```

You can use `--help` option for any command, e.g.:

```bash
dncli tx vm --help
dncli tx vm execute --help

dncli q vm --help
dncli q vm compile --help
```

In case, your VM transaction contains an error, you always can query detailed information about the happened error, check next command:

```bash
dncli q vm tx [txId]
```

## Testnet configuration

**dncli** by default connects to local **dnode** \(at localhost:26657\) and **compiler** \(inside **dvm**\) \(at localhost:50051\). To connect to remote node or launched testnet, change these configuration settings:

```bash
dncli config compiler tcp://rpc.testnet.dfinance.co:50051
dncli config node http://rpc.testnet.dfinance.co:26657
```

Also, **compiler** address could be passed as `--compiler` option during execution of command requiring compilation, this is: 

```bash
# use --help to see full list of options
dncli q vm compile <file> <account>
```


# dncli

**dncli** (dfinance node CLI) is a CLI application developed to work with **dnode**. With dncli you can query blockchain data, post transactions, and query network status.

It comes as binary application and can be downloaded from [GitHub release page](https://github.com/dfinance/dnode/releases). Alternatively you can [build it from sources](https://github.com/dfinance/dnode).

## Usage

After installing **dncli**, it should be configured:

```bash
dncli config chain-id dn-testnet
dncli config output json
dncli config indent true
dncli config trust-node true
dncli config compiler 127.0.0.1:50053
dncli config node 127.0.0.1:26657
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
```

You can use `--help` option for any command, e.g.:

```bash
dncli tx vm --help
dncli tx vm execute-script --help

dncli query vm --help
dncli query vm compile-script --help
```

## Testnet configuration

**dncli** by default connects to local **dnode** (at localhost:26657) and **compiler** (at localhost:50053). To connect to remote node or launched testnet, change these configuration settings:

```bash
dncli config node rpc.testnet.dfinance.co:26657
dncli config compiler rpc.testnet.dfinance.co:50053
```

Also, **compiler** address could be passed as `--compiler` option during execution of commands requiring compillation. Those are:

```bash
# use --help to see full list of options
dncli query vm compile-script <file> <account>
dncli query vm compile-module <file> <account>
```

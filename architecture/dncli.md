# Dnode introduction

**Dncli** is a command-line interface application developed to iterate with **dnode**. With **dncli** you can query blockchain data, post transactions, query network status. 

It comes as binary application and can be downloaded from **dnode** GitHub **[release page](https://github.com/dfinance/dnode/releases)**. 

Also, **dncli** could be built from sources from **[dnode](https://github.com/dfinance/dnode)** repository.

## Usage

After installation of **dncli**, it should be configured: 

```shell
dncli config chain-id dn-testnet
dncli config output json
dncli config indent true
dncli config trust-node true
dncli config compiler 127.0.0.1:50053
dncli config node 127.0.0.1:26657
```

After configuration you can try a **dncli**:

```shell
dncli version
dncli --help
```

**Dncli** contains multiplay commands, for each **dnode** module that supports the cli interface.

There are two types of commands of **dncli**: transaction commands and query commands. Transaction commands start with **"tx"** prefix, query commands with **"query"** prefix. Different between them, that **"tx"** commands imply building and broadcasting transaction, while **"query"** using to query data from dnode.

You can try it yourself and see available commands:

```shell
dncli tx --help
dncli query --help
```

Also, you can use **--help** for a displayed commands, e.g.:

```shell
dncli tx vm --help
dncli tx vm execute-script --help
    
dncli query vm --help
dncli query vm compile-script --help
```

## Testnet configuration

**Dncli** by default connects to local **dnode** and **compiler**, to connect to remote one node or launched testnet change configuration settings:

```shell
dncli config node rpc.testnet.dfinance.co:26657
dncli config compiler rpc.testnet.dfinance.co:50053
```

Also, the **compiler** address could be passed just using **--compiler** flag, during the execution of compile commands (compile-script/compile-module).
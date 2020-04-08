# Getting started

Here is a guide on how to install **dncli** command line interface and connect to **dfinance** testnet.

## Installation using precompiled binaries

First of all download the latest version of **dncli** for your system from [release pages](https://github.com/dfinance/dnode/releases).

Install downloaded **dncli** binary.

For Mac OS/Linux:

```text
mv <downloaded binary path> ./dncli
chmod +x ./dncli
mv ./dncli /usr/local/bin/dncli
```

For Windows:

1. Go to **"Program Files"** directory.
2. Create there **"dn"** directory.
3. Rename the downloaded file to **"dncli"** and put it into **"dn"** directory.

Now **"cmd"** and execute

```text
setx path "%path%;%ProgramFiles%\dn"
```

Now restart **"cmd"**.

Check that installation successful done by running the command:

```text
dncli version
```

Your should see your current version of **dncli** in output.

## Configuration

Let's configure **dncli** and after go to the next step:

```text
dncli config chain-id dn-testnet
dncli config output json
dncli config indent true
dncli config trust-node true
dncli config compiler rpc.testnet.dfinance.co:50053
dncli config node rpc.testnet.dfinance.co:26657
```

These configurations will connect your local **dncli** with remote testnet nodes.

Check that **dncli** configurated correctly:

```text
dncli status
```

## Installation from sources

Before we start you should have a correct 'GOPATH', 'GOROOT' environment variables, also installed [Golang](https://golang.org/).

Required:

* golang 1.13.8 or later.
* protoc - here is [installation instruction](https://www.grpc.io/docs/quickstart/go/).

### Build and Install using Makefile

Clone dfinance node repository to suitable place

```text
git clone https://github.com/dfinance/dnode.git
```

Build and install _dncli_ as binary using Makefile

```text
make install-dncli
```

So after this command _dncli_ will be available from console

```text
dncli version --long
```

### Build without Makefile

And let's build _dncli_:

```text
GO111MODULE=on go build -o dncli cmd/dncli/main.go dncli
```

Command must execute fine, after it you can run _dncli_:

```text
./dncli version --long
```


# Quick Start

This document guide you on how to connect with launched dfinance testnet, try CLI to work with remote dfinance node (dnode), also create your first account, receive free testnet DFI, and execute first transactions.

## Installation

To communicate with dfinance testnet you have to download an application called dncli. It's a command-line interface application created to communicate with dnode. With dncli you can send transactions to dfinance blockchain, query data from blockchain. 

While we don't have dnode installed locally, we will connect to public testnet nodes. 

First of all download the latest version of dncli for your system from [release pages](https://github.com/dfinance/dnode/releases).

Install downloaded dncli binary.

**For Mac OS/Linux:**

    mv <downloaded binary path> ./dncli
    chmod +x ./dncli
    mv ./dncli /usr/local/bin/dncli

**For Windows:**

1. Go to "Program Files" directory.
2. Create there "dn" directory.
3. Rename the downloaded file to "dncli" and put it into "dn" directory.
4. Launch "cmd" and execute: 

    setx path "%path%;%ProgramFiles%\dn"

Now restart "cmd".

Check that installation successful done by running the command:

    dncli version

Your should see your current version of dncli in output.

Let's configure dncli and after go to the next step:

    dncli config chain-id dn-testnet
    dncli config output json
    dncli config indent true
    dncli config trust-node true
    dncli config compiler rpc.testnet.dfinance.co:50053
    dncli config node rpc.testnet.dfinance.co:26657

These configurations will connect your local **dncli** with remote testnet nodes.

Check that dncli configurated correctly:

    dncli status

## Account creation and free DFI

Let's create your first dfinance account:

    dncli keys add my-account

Put passphrase to encrypt your account locally, also copy mnemonic and keep in a safe place.

Go to dfinance [wallet portal](https://wallet.testnet.dfinance.co/) and request faucet to send your free DFI. Click there on request "Request Tokens" button and wait for few seconds, DFI coins will appear in your account.

After this let's query our account with dncli:

    dncli query account <address>

Replace *<address>* with your address. You will see the output with your address and with your balances, balances should contain DFI coins if we want to continue to the next steps.

## First transactions

Let's try to send basic coins transfer transactions between two accounts.

Create another account:

    dncli keys add recipient

You can query created account by address to see it doesn't exist with the command from the previous part of the current document.

To send 10 DFI coins to this account needs to execute the next command:

    dncli tx bank send <sender> <recipient> 10000000000000000000dfi --fees 1dfi

Replace *<sender>* with your *my-account* address and *<recipient>* with your *<recipient>* address.

We use "10000000000000000000dfi" as the amount because by default DFI has 18 decimals places, so to send 10 DFI you have to keep decimals.

After execution, you will get txId of the transaction in the output. To see transaction status you can execute:

    dncli query tx <txId>

Also now you can query a recipient account and see how balance updated there:

    dncli query account <address>

Replace *<address>* with recipient address to see updated balance.

## First Smart Contract

Dfinance platform allows writing smart contracts in two languages: Mvir and Move. You can choose the one you like best.

Current documentation contains examples in Mvir language, Move language examples would be added in the near time.

Dfinance provides [VSCode plugin](https://marketplace.visualstudio.com/items?itemName=damirka.move-ide), so you can download [VSCode](https://code.visualstudio.com/) and then install the plugin for Mvir/Move with compilation support.

Let's do the same we've done in the previous part, but with smart contracts: transfer coins between two accounts.

### Mvir version

    import 0x0.Account;
    import 0x0.Coins;
    
    main(recipient: address, amount: u128, denom: bytearray) {
        let coin: Coins.Coin;
        coin = Account.withdraw_from_sender(move(amount), move(denom));
    
        Account.deposit(move(recipient), move(coin));
        return;
    }

As you can see we import core modules from address 0x0. This address (0x0) reserved for core modules. Then we withdraw Coin resource from the sender account and deposit to the recipient.

## Move IDE for **VSCode**

Repository is here: [https://github.com/damirka/vscode-move-ide](https://github.com/damirka/vscode-move-ide)

VSCode marketplace link: [https://marketplace.visualstudio.com/items?itemName=damirka.move-ide](https://marketplace.visualstudio.com/items?itemName=damirka.move-ide)

First, go to plugin settings and update default account and compiler URL:

- Default account: use address generated during my-account creation.
- Compiler: rpc.testnet.dfinance.co:50053

Then:

- Create a new workspace.
- Put a configuration file into the workspace under .mvconfig.json name.

    {
        "network": "dfinance",
        "defaultAccount": "<address>",
        "compiler": "rpc.testnet.dfinance.co:50053",
        "compilerDir": "./out"
    }

Replace *<address>* with my-account address.

- Put file ****send.mvir contains script into the workspace.
- Open file send.mvir in the editor and run the command: 'Move: Compile'.
- Check ./out folder in the workspace to see the compiled file.
- Done!

**Terminal**

Let's save script under './send.mvir' name and compile:

    mkdir out
    dncli query vm compile-script ./send.mvir <address> --to-file ./out/send.mvir.json

Replace <address> with my-account address and execute the command.

You will see new file send.mvir.json contains the byte code of the script.

**Execute**

Now let's execute the compiled script.

We should make a transaction that will contain compiled byte code and put the right arguments.

We can do it with the command:

    dncli tx vm execute-script ./out/send.mvir.json <recipient> 10000000000000000000 dfi --from my-account --fees 1dfi

Replace *<recipient>* with recipient address account and execute, you can see the result of command execution by querying txId.

As you see arguments passed to execute-script match arguments in script "main" [function](https://www.notion.so/walletsofwings/Quick-Start-f89f82df1d804ab9a693b2a740c7cd2b#3d8adb16be7d4646a850ce4f311efa4a).

In nutshell, we have done the same, but instead of using dncli native transaction for sending coins, we used a smart contract.

Now, once the transaction confirmed and executed, you can query the recipient account again to see how balance changed (should be increased by 10 DFI).
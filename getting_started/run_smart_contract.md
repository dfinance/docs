# Run smart contract

Here is guide how to run your first smart contract in dfinance network using **dncli**.

## Smart contracts introduction

Dfinance platform allows writing smart contracts in two languages: Mvir and Move. You can choose the one you like best.
Current documentation contains examples in Mvir language, Move language examples would be added in the near time.

Dfinance provides [VSCode plugin](https://marketplace.visualstudio.com/items?itemName=damirka.move-ide), so you can download [VSCode](https://code.visualstudio.com/) and then install the plugin for Mvir/Move with compilation support.

Let's do the same we've done in the previous part of documentation, but with smart contracts: transfer coins between two accounts.

**Mvir**
```rust
import 0x0.Account;
import 0x0.Coins;

main(recipient: address, amount: u128, denom: bytearray) {
    let coin: Coins.Coin;
    coin = Account.withdraw_from_sender(move(amount), move(denom));

    Account.deposit(move(recipient), move(coin));
    return;
}
```

As you can see we import core modules from address 0x0. This address (0x0) reserved for core modules. Then we withdraw Coin resource from the sender account and deposit to the recipient.

## Compilation

Here is two options how you can compile your Mvir/Move modules/scripts.

### Move IDE for **VSCode**

Repository is here: [https://github.com/damirka/vscode-move-ide](https://github.com/damirka/vscode-move-ide)

VSCode marketplace link: [https://marketplace.visualstudio.com/items?itemName=damirka.move-ide](https://marketplace.visualstudio.com/items?itemName=damirka.move-ide)

First, go to plugin settings and update default account and compiler URL:

- Default account: use your dfinance address.
- Compiler: rpc.testnet.dfinance.co:50053

Then:

- Create a new workspace.
- Put a configuration file into the workspace under .mvconfig.json name.

```json
{
    "network": "dfinance",
    "defaultAccount": "<address>",
    "compiler": "rpc.testnet.dfinance.co:50053",
    "compilerDir": "./out"
}
```

Replace **&lt;address&gt;** with your dfinance address.

- Put file **send.mvir** contains script into the workspace.
- Open file **send.mvir** in the editor and run the command: **'Move: Compile'**.
- Check **./out** folder in the workspace to see the compiled file.
- Done!

### Terminal

Put the script under **'./send.mvir'** name and compile using dncli:

```shell
mkdir out
dncli query vm compile-script ./send.mvir <address> --to-file ./out/send.mvir.json
```

Replace **&lt;address&gt;** with your dfinance address and execute the command.

You will see new file **./out/send.mvir.json** contains the byte code of the script.

## Run

Now let's execute the compiled script.

We should make a transaction that will contain compiled byte code and put the right arguments.

Do it with the command:

```shell
dncli tx vm execute-script ./out/send.mvir.json <recipient> 10000000000000000000 dfi --from my-account --fees 1dfi
```

Replace **&lt;recipient&gt;** with recipient address account and execute, you can see the result of command execution by querying txId.

As you see arguments passed to execute-script match arguments in script function **"main"**.

In nutshell, we have done the same, but instead of using **dncli** native functional for sending coins, we used a smart contract.

Now, once the transaction confirmed and executed, you can query the recipient account again to see how balance changed (should be increased by 10 DFI).
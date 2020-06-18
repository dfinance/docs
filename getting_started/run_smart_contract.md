# Run smart contract

Here is guide how to run your first smart contract in dfinance network using **dncli**.

## Smart contracts introduction

**Dfinance** platform allows writing smart contracts in Move language developed by by Facebook's Libra and can be executed by Move VM.

Also, **Dfinance** provides [VSCode plugin](https://marketplace.visualstudio.com/items?itemName=damirka.move-ide), so you can download [VSCode](https://code.visualstudio.com/) and then install the plugin for Move language with syntax and errors highlight, compilation support, and language server support from the box.

Let's do the same we've done in the previous part of documentation, but with smart contracts: transfer coins between two accounts.

```rust
script {
    use 0x0::Account;
    use 0x0::DFI;

    fun main(sender: &signer, recipient: address, dfi_amount: u128) {
        Account::pay_from_sender<DFI::T>(sender, recipient, dfi_amount);
    }
}
```

As you can see we import core modules from address 0x0. This address \(0x0\) reserved for core modules. Currently we're importing two modules as part of our standard library: [Account](https://github.com/dfinance/dvm/blob/master/lang/stdlib/account.move) and [DFI](https://github.com/dfinance/dvm/blob/master/lang/stdlib/dfi.move). Account module is developed to work with Dfinance accounts from Move, DFI module contains resources to work with DFI balances of accounts. We will talk more about resources later in this documentation or you can read about them in [Move Book](https://move-book.com) \(the book about Move language developed by our team\).

Also, you can see the `signer` type. The signer type represents sender authority. In other words - using signer means accessing the sender's address and resources. It has no direct relation to signatures or literally signing, in terms of Move VM it simply represents sender. If you are going to use the `signer` type in your script, it must be the first argument in your main function. **Important**: you don't need to provide an argument for signer type when executing a script, as it will be done automatically.

Read more about Signer in [Move Book](https://move-book.com/resources/signer-type.html).

The script code using function `pay_from_sender` of Account module, that function withdraw balance resource balance from sender account and put withdrawn resource to recipient account. Another methods and options how to work with balances you can read in our [Standard Library](../move_vm/standard_lib.md) documentation.

Let's continue with such plain example and compile, execute our script.

## Compilation

Put the script under **'./send.move'** name or use [VSCode plugin](https://marketplace.visualstudio.com/items?itemName=damirka.move-ide) and compile using dncli:

```text
mkdir out
dncli query vm compile-script ./send.move <address> --to-file ./out/send.move.json
```

Replace **&lt;address&gt;** with your **dfinance** address and execute the command.

You will see new file **'./out/send.move.json'** contains the byte code of the script.

## Run

Now let's execute the compiled script.

We should make a transaction that will contain compiled byte code and put the right arguments.

Do it with the command:

```text
dncli tx vm execute-script ./out/send.move.json <recipient> 10000000000000000000 --from my-account --fees 1dfi
```

Replace **&lt;recipient&gt;** with recipient address account and execute, you can see the result of command execution by querying txId.

As you see arguments passed to execute-script match arguments in script function **"main"**.

In nutshell, we have done the same, but instead of using **dncli** native functional for sending coins, we used a smart contract.

Now, once the transaction confirmed and executed, you can query the recipient account again to see how balance changed \(should be increased by 10 DFI\).


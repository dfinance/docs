# Meet the network using CLI

Here is a step-by-step guide on how to operate with arbitrary **dfinance** node which covers creation of your first account, receiving free testnet DFI tokens and execution of first transaction

## Account creation and free DFI

Let's create your first **dfinance** account:

```shell
dncli keys add my-account
```

Put passphrase to encrypt your account locally, also copy **mnemonic** and keep in safe place.

Go to **dfinance** **[wallet portal](https://wallet.testnet.dfinance.co/)** and request faucet to send your free DFI. Click there on request **Request Tokens** button and wait for few seconds, DFI coins will appear on your account.

After this let's query our account with **dncli**:

```shell
dncli query account <address>
```

Replace **&lt;address&gt;** with your address. You will see output with your address and with your balances, balances should contains DFI coins if we want to continue to next steps


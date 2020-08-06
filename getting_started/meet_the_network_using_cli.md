# Meet the network using CLI

Here is a step-by-step guide on how to operate with arbitrary **dfinance** node which covers creation of your first account, receiving free testnet DFI tokens and execution of first transaction.

## Account creation and free DFI

Let's create your first **dfinance** account.

Generate new mnemonic:

```text
dncli keys mnemonic
```

Copy **mnemonic** and keep in safe place.

Create new account, use **mnemonic** generated from previous command:

```text
dncli keys add -i my-account
```

Save **passphrase** and keep in safe place. **Without mnemonic and passphrase you can't access your new account!**

Go to **dfinance** [**wallet portal**](https://testnet.dfinance.co/), use your **mnemonic** and **passphrase** to login, and request faucet to send your free DFI. Click there on request **Request Tokens** button and wait for few seconds, DFI coins will appear on your account. Also, the faucet sending testnet BTC and USDT coins besides DFI.

After this let's query our account with **dncli**:

```text
dncli q account <address>
```

Replace `<address>` with your address. You will see output with your address and with your balances, balances should contains DFI coins if we want to continue to next steps.


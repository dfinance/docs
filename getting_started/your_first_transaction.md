# Your first transaction

Here is guide how to send your first transaction in **dfinance** network using **dncli**.

## Transfer coins to recipient

Let's try to send basic coins transfer transactions between two accounts.

Create another account:

```text
dncli keys add recipient
```

To send **10 XFI** coins to this account needs to execute the next command:

```text
dncli tx bank send <sender> <recipient> 10000000000000000000xfi
```

Replace **&lt;sender&gt;** with your account address and **&lt;recipient&gt;** with **&lt;recipient&gt;** address.

We use **"1000000000000000000xfi"** as the amount because by default **XFI** has 18 decimals places, so to send **10 XFI** you have to keep decimals.

After execution, you will get transaction id in the output. To see transaction status execute:

```text
dncli q tx <txId>
```

Also now you can query a recipient account and see how balance updated:

```text
dncli q account <address>
```

Replace **&lt;address&gt;** with recipient address to see updated balance.


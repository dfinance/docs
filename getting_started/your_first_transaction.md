# Your first transaction

Here is guide how to send your first transaction in **dfinance** network using **dncli**.

## Transfer coins to recipient

Let's try to send basic coins transfer transactions between two accounts.

Create another account:

```text
dncli keys add recipient
```

To send **10 DFI** coins to this account needs to execute the next command:

```text
dncli tx bank send <sender> <recipient> 10000000000000000000dfi --fees 1dfi
```

Replace **&lt;sender&gt;** with your account address and **&lt;recipient&gt;** with **&lt;recipient&gt;** address.

We use **"10000000000000000000dfi"** as the amount because by default **DFI** has 18 decimals places, so to send **10 DFI** you have to keep decimals.

After execution, you will get transaction id in the output. To see transaction status execute:

```text
dncli query tx <txId>
```

Also now you can query a recipient account and see how balance updated:

```text
dncli query account <address>
```

Replace **&lt;address&gt;** with recipient address to see updated balance.

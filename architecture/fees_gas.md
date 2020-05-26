# Fees & Gas

Every transaction in **dfinance** protocol requires sender to pay fee, as well as it has maximum amount of **gas** which can be used during transaction execution. Gas is a cost of operation in our blockchain (such as executing smart-contract in **VM** or basic read-write operations on blockchain storage).

## Gas

The gas amount is an integer and is set by `--gas` option in **dncli**:

```bash
dncli tx bank send <sender> <recipient> <amount> --gas <value>
```

The default gas parameter in **dncli** is `500000`, if you see errors related to **"out of gas"** issue, try to increase gas until you find the optimal one for your transaction.

## Fees

Although **dfinance** supports different currencies (like ETH), transaction fees can be paid only in **DFI** currency.

Currently, minimal fee amount is **1 DFI**. Though this value may vary for each validator in the network as it's for validator to decide his minimal fee. This means that even if your transaction fee was too low for current validator, it still may be added in one of the next few blocks by validators whose minimal fee matches your value.

<!--
The minimum amount of fees by default is **1 DFI**, but this value could be configured by each validator in the network, which means each validator can have the different minimum amount of fee to include your transaction in a block.
-->

So when you send any transaction, you must provide minimal feee in **1 DFI**. In `dncli` use `--fees` option to set it, e.g.:

```bash
# 1dfi MUST be written without spaces between amount and denom
dncli tx vm execute-script <script.mvir.json> <args,...> --fees 1dfi
```

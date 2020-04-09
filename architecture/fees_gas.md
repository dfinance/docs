# Fees & Gas

Every transaction in **dfinance** protocol requires sender to pay fee, as well as it has maximum amount of **gas** which can be used during transaction execution. Gas is a cost of operation in our blockchain (such as executing smart-contract in **VM** or basic read-write operations on blockchain storage).

## Gas

The gas amount is an integer and is set by `--gas` option in **dncli**:

```bash
dncli tx bank send <sender> <recipient> <amount> --gas <value>
```

## Fees

Although **dfinance** supports different currencies (like ETH**), transaction fees can be paid only in **DFI** currency.

Currently, minimal fee amount is **1 DFI**.

<!--
The minimum amount of fees by default is **1 DFI**, but this value could be configured by each validator in the network, which means each validator can have the different minimum amount of fee to include your transaction in a block.
-->

So when you send any transaction, you must provide minimal feee in **1 DFI**. In `dncli` use `--fees` option to set it, e.g.:

```bash
# 1dfi MUST be written without spaces between amount and denom
dncli tx vm execute-script <script.mvir.json> <args,...> --fees 1dfi
```


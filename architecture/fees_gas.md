# Fees & Gas

Each transaction in **dfinance** protocol requires fees to be paid from the sender, also each transaction contains the maximum amount of **gas** that can be used during transaction execution. This **gas** value uses during write/read data from storage, and also during the execution of smart contracts with **Move VM**.

## Gas

The gas amount is an integer and could be provided by **--gas** flag in **dncli**:

```text
dncli tx bank send <sender> <recipient> <amount> --gas <value>
```

## Fees

Even **dfinance** supports different currencies, like **ETH**, transaction fees can be paid only in **DFI**.

The minimum amount of fees by default is **1 DFI**, but this value could be configured by each validator in the network, which means each validator can have the different minimum amount of fee to include your transaction in a block.

So when you send any transaction, you must provide at minimum **1 DFI** as fees, in **dncli** use **--fees** flag to provide gas, e.g.:

```text
dncli tx vm execute-script <script.mvir.json> <args,...> --fees 1dfi
```


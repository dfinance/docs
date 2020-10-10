# Fees & Gas

Every transaction in **dfinance** protocol requires sender to pay fee, as well as it has maximum amount of **gas** which can be used during transaction execution. Gas is a cost of operation in our blockchain \(such as executing smart-contract in **VM** or basic read-write operations on blockchain storage\).

## Gas

The gas amount is an integer and is set by `--gas` option in **dncli**:

```bash
dncli tx bank send <sender> <recipient> <amount> --gas <value>
```

The default gas parameter in **dncli** is `500000`, if you see errors related to **"out of gas"** issue, try to increase gas until you find the optimal one for your transaction.

### Block gas limit

The current testnet block gas limit is `10000000` gas. Means that transaction with gas limit greater than `10000000` will be not accepted by validators nodes.

In future this setting will become changeable via Government voting mechanism.

## Fees

Although **dfinance** supports different currencies \(like ETH\), transaction fees can be paid only in **XFI** currency.

Currently, minimal fee amount is **1 XFI**. Though this value may vary for each validator in the network as it's for validator to decide his minimal fee. This means that even if your transaction fee was too low for current validator, it still may be added in one of the next few blocks by validators whose minimal fee matches your value.

**dncli** sets fees automatically, so you can ignore `--fees` flag, alternatively, if you want to speed up your transaction confirmation time you can can set fees manually by using `--fees` flag, e.g.:

```bash
# Fees amount MUST be written without spaces between amount and denom
dncli tx vm execute <script.mvir.json> <args,...> --fees 1000000000000000000xfi
```


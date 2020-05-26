# Withdraw

You can withdraw your coins/tokens from dfinance back to the chain from which these coins/tokens arrived.

To withdraw your coins/tokens from **PegZone** you need to send only one transaction with **dncli**:

```text
dncli tx currencies destroy-currency [chainID] [symbol] [amount] [recipient] [flags]
```

In the case of Ethereum, **chainID** will be **ETH**, and **symbol** also **ETH**, **recipient** is your address in Ethereum network \(e.g. **0x01849...**\).

# Oracles Price Feed

This document described how to work with oracles data in case of **dfinance** network.

## Introduction to price feed oracles

**Dfinance** blockchain supports price feed oracles, every block new transactions coming from oracles nodes and posting prices.

Currently, dfinance supports next tickers:

- **ETH_USDT** - [Binance](https://www.binance.com/en/trade/ETH_USDT).
- **BTC_USDT** - [Binance](https://www.binance.com/en/trade/BTC_USDT).
- **DFI_ETH** - simulation.
- **DFI_BTC** - simulation.

A list of assets could be updated, so the actual one you can get from [API](https://rest.testnet.dfinance.co/oracle/assets).

Currently oracles nodes fetching price from Binance only. Oracle node application published in our Github [repository](https://github.com/dfinance/oracle-app) and we are welcome for contributions: dfinance needs more exchanges and ticker pairs.

Every block dfinance platform collect posted prices from whitelisted oracles and then choose median to store final price for the last block.

This functional implemented by dnode in [x/oracle](https://github.com/dfinance/dnode/tree/master/x/oracle) module.
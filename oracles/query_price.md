# Query Price

Current **dfinance** VM implementation supports querying the price for the provided ticker thanks to [Oracle](https://github.com/dfinance/dvm/blob/master/lang/stdlib/oracle.mvir) module:

```rust
module Oracle {
    native public get_price(ticker: u64): u64;
}
```

This is native function, that you can import into your module or script and use. It accepts the **u64** parameter as ticker id, because **dfinance** encode ticker name in case of smart contract to unique **u64** id, and returns u64 number as the current price of pair.

## Ticker id

Ticker id is a hash from string contains a description, pseudo-code to generate id looks so:

```text
ticker = xxHash(to_lower("ETH_USDT"))
```

**Dnode** and **dncli** already support everything from the box, so you don't need to worry and do any manipulations.

In your code you can write already with hashed value:

```rust
let price : u64;
price = Oracle.get_price(#"ETH_USDT");
```

In the case of passing arguments, you can make a script that receives ticket id as **u64** argument:

**Mvir**

```rust
import 0x0.Oracle;

main(ticker: u64) {
    let price: u64;
    price = Oracle.get_price(move(ticker));
    return; 
}
```

You can easily pass a ticker id argument so \(ticker id will be converted automatically\):

```text
dncli tx vm execute-script <script file> #"ETH_USDT" --from <account> --fees 1dfi
```

So **dncli** detects that it hashed value and automatically makes a right hash from it.

## Price

By default returned u64 price has reserved **8 decimals places**.

Means price for ETH\_USDT as **100.02** will be presented as **10002000000**.

To see an example look at our [API](https://rest.testnet.dfinance.co/oracle/currentprice/eth_usdt), ETH\_USDT part, how price presented there.

## Write a script

Let's write a script that takes any price ticker, getting a price and fire event with the price.

**Mvir**

```rust
import 0x0.Oracle;
import 0x0.Account;

main(ticker: u64) {
    let handle: Account.EventHandle<u64>;
    let price: u64;

    price = Oracle.get_price(move(ticker));

    handle = Account.new_event_handle<u64>();
    Account.emit_event<u64>(&mut handle, move(price));
    Account.destroy_handle<u64>(move(handle));
    return;
}
```

Compile it, and let's execute several transactions with different tickers:

```text
dncli tx vm execute-script <compiled file> #"ETH_USDT" --from <account> --fees 1dfi
dncli tx vm execute-script <compiled file> #"BTC_USDT" --from <account> --fees 1dfi
dncli tx vm execute-script <compiled file> #"DFI_ETH"  --from <account> --fees 1dfi
```

You can query transaction ids to see how events with price fired.

## Usage in module

Similarly, you can write your module, that will use the price from oracles.

Something like:

```rust
module PriceRequest {
    import 0x0.Oracle;

    public get_eth_usdt_price(): u64 {
        return Oracle.get_price(#"ETH_USDT");
    }
}
```

And then just use it in your scripts.


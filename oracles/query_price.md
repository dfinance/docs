# Query Price

Current **dfinance** VM implementation supports querying the price for the provided ticker thanks to [Oracle module](https://github.com/dfinance/dvm/blob/v0.4.0/lang/stdlib/oracle.move):

```rust
address 0x1 {
    module Oracle {
        native public fun get_price<Curr1, Curr2>(): u64;
    }
}
```

This is native function, that you can import into your module or script and use. It requires two generics \(pairs\) to call a function, e.g.: BTC\_USDT, ETH\_USDT, DFI\_BTC.

Let's try to query BTC\_USDT price:

```rust
let price = Oracle::get_price<Coins::BTC, Coins::USDT>();
```

As script it looks so:

```rust
script {
    use 0x1::Coins;
    use 0x1::Oracle;

    fun main(ticker: u64) {
        let _ = Oracle::get_price<Coins::BTC, Coins::USDT>();
    }
}
```

## Price

By default returned **u64** price has reserved **8 decimals places**.

Means price for ETH\_USDT as **100.02** will be presented as **10002000000**.

To see an example look at our [API](https://rest.testnet.dfinance.co/oracle/currentprice/eth_usdt), ETH\_USDT part, how price presented there.

## Write a script

Let's write a script that will take BTC\_USDT price and will emit an event with this ticker's price:

```rust
script {
    use 0x1::Event;
    use 0x1::Coins;
    use 0x1::Oracle;

    fun main(sender: &signer) {
        let price = Oracle::get_price<Coins::BTC, Coins::USDT>();

        let event_handle = Event::new_event_handle<u64>(sender);
        Event::emit_event(&mut event_handle, price);
        Event::destroy_handle(event_handle);
    }
}
```

Compile the script and execute:

```text
dncli tx vm execute <compiled file> --from <account>
```

You can query results by transaction id to see how events with price fired.

## Usage in module

Similarly, you can write your module, that will use the price from oracles.

Something like:

```rust
module PriceRequest {
    use 0x1::Oracle;
    use 0x1::Coins;

    public fun get_eth_usdt_price(): u64 {
        Oracle::get_price<Coins::ETH, Coins::USDT>()
    }
}
```

And then just use it in your scripts.

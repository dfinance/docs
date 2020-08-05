# Query Price

Current **dfinance** VM implementation supports querying the price for the provided ticker thanks to [Coins](https://github.com/dfinance/dvm/blob/v0.4.0/lang/stdlib/coins.move) module:

```rust
address 0x1 {
/// Enum-like module to make generic type-matching possible, every coin which is
/// officially supported by blockchain (or peg-zone specifically) is added here.
/// Ideally this module should be auto-generated and rarely updated via consensus
module Coins {
    struct ETH {}
    struct BTC {}
    struct USDT {}

    resource struct Price<Curr1, Curr2> {
        value: u128
    }

    public fun get_price<Curr1, Curr2>(): u128 acquires Price {
        borrow_global<Price<Curr1, Curr2>>(0x1).value
    }

    public fun has_price<Curr1, Curr2>(): bool {
        exists<Price<Curr1, Curr2>>(0x1)
    }
}
}
```

There are two functions, get_price and has_price, both require two generics \(pairs\) to make a call, e.g.: BTC\_USDT, ETH\_USDT, DFI\_BTC.

Let's try to query BTC\_USDT price:

```rust
let btc_usd = Coins::get_price<Coins::BTC, Coins::USDT>();
```

As script it looks so:

```rust
script {
    use 0x1::Coins;

    fun main() {
        let _ = Coins::get_price<Coins::BTC, Coins::USDT>();
    }
}
```

Also, you can check, if pair exists:

```rust
script {
    use 0x1::Coins;

    fun main() {
        // If BTC_USDT pair doesn't exist - throw error with code 101.
        assert(Coins::has_price<Coins::BTC, Coins::USDT>(), 101);
    }
}
```

## Price

By default returned **u128** price has reserved **8 decimals places**.

Means price for ETH\_USDT as **100.02** will be presented as **10002000000**.

To see an example look at our [API](https://rest.testnet.dfinance.co/oracle/currentprice/btc_usdt), ETH\_USDT part, how price presented there.

## Write a script

Let's write a script that will take BTC\_USDT price and will emit an event with this ticker's price:

```rust
script {
    use 0x1::Event;
    use 0x1::Coins;

    fun main() {
        let price = Coins::get_price<Coins::BTC, Coins::USDT>();

        Event::emit(price);
    }
}
```

Compile the script and execute.

You can query results by transaction id to see how events with price fired.

## Usage in module

Similarly, you can write your module, that will use the price from oracles.

Something like:

```rust
module PriceRequest {
    use 0x1::Coins;

    public fun get_eth_usdt_price(): u128 {
        Coins::get_price<Coins::ETH, Coins::USDT>()
    }
}
```

And then just use it in your scripts.

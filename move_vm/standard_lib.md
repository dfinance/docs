# Standard Library

Standard **Move VM** library is default modules that already developed and developers can use in developing new modules, scripts.

They all placed on the address **0x0**. So when you import something from **0x0**, you import standard modules, like:

```rust
use 0x0::Account;
use 0x0::Events;
use 0x0::DFI;
use 0x0::Coins;
```

You can look for actual standard modules in [dvm](https://github.com/dfinance/dvm/tree/master/lang) repository.

## Time

[Time](https://github.com/dfinance/dvm/blob/bf457b3145c5e448ece3258bbf67c22326559a12/lang/stdlib/time.move#L3) module allows to get current UNIX timestamp of latest block.

Example:

```rust
script {
    use 0x0::Time;

    fun main() {
        let _ = Time::now();
    }
}
```

Method will return u64 value as UNIX timestamp of latest block.

## Block

[Block](https://github.com/dfinance/dvm/blob/bf457b3145c5e448ece3258bbf67c22326559a12/lang/stdlib/block.move#L3) module allows to get current blockchain height.

```rust
script {
    use 0x0::Block;

    fun main() {
        let _ = Block::get_current_block_height();
    }
}
```

Function will return u64 value as height of latest block.

## Transaction

[Transaction](https://github.com/dfinance/dvm/blob/bf457b3145c5e448ece3258bbf67c22326559a12/lang/stdlib/transaction.move#L3) module contains functions to work with transaction data, currently supports two functions: `sender()`, `assert(bool, u64)`. 

Getting sender address of transaction:

```rust
script {
    use 0x0::Transaction;

    fun main() {
        let _ = Transaction::sender();
    }
}
```

Assert:

```rust
script {
    use 0x0::Transaction;

    fun main() {
        let a = 10;
        let b = 11;
        Transaction::assert(a == b, 101);
    }
}
```

In case you pass `false` as first argument of `assert(bool, u64)` or result of your expression, transaction will fail and return "sub_status" in event of transaction that will equal to your code provided as second argument.

## Compare

[Compare](https://github.com/dfinance/dvm/blob/bf457b3145c5e448ece3258bbf67c22326559a12/lang/stdlib/compare.move#L10) module allows to compare two vectors of u8 values (bytes).

Comparing two byte vectors:

```rust
script {
    use 0x0::Compare;
    use 0x0::Transaction;

    fun main() {
        let a = x"00";
        let b = x"01";
        Transaction::assert(Compare::cmp_lcs_bytes(&a, &b) == 0, 101);
    }
}
```

## DFI && Coins

[DFI](https://github.com/dfinance/dvm/blob/bf457b3145c5e448ece3258bbf67c22326559a12/lang/stdlib/dfi.move#L7) and [Coins](https://github.com/dfinance/dvm/blob/bf457b3145c5e448ece3258bbf67c22326559a12/lang/stdlib/coins.move#L6) modules allow to get a type of currency that you going to use in your code.

```rust
script {
	use 0x0::Account;
	use 0x0::DFI;
	use 0x0::Coins;

	fun main(recipient: address, dfi_amount: u128, eth_amount: u128, btc_amount: u128, usdt_amount: u128) {
		Account::pay_from_sender<DFI::T>(recipient, dfi_amount);
		Account::pay_from_sender<Coins::BTC>(recipient, eth_amount);
	    Account::pay_from_sender<Coins::BTC>(recipient, btc_amount);
	    Account::pay_from_sender<Coins::USDT>(recipient, usdt_amount);
	}
}
```

## Oracle

[Oracle](https://github.com/dfinance/dvm/blob/bf457b3145c5e448ece3258bbf67c22326559a12/lang/stdlib/oracle.move#L3) module allows to get current price of asset pair.

```rust
script {
    use 0x0::Oracle;

    fun main(ticker: u64) {
        let _ = Oracle::get_price(ticker);
    }
}
```

More about work with oracles can see in our [oracles documentation](/oracles/README.md).

## Event

Event module allows to work with events: generate new event handlers, and fire events.

Example with firing event contains provided number:

```rust
script {
    use 0x0::Event;

    fun main(a: u64) {
        let event_handle = Event::new_event_handle<u64>();
		Event::emit_event(&mut event_handle, a);
		Event::destroy_handle(event_handle);
    }
}
```

Or you can store event handler in resource and fire events when you need:

```rust
module MyEvent {
    use 0x0::Event;
    use 0x0::Transaction;

    resource struct T {
        eh: Event::EventHandle<u64>,
    }

    public fun init() {
        move_to_sender<T>(T {
            eh: Event::new_event_handle(),
        });
    }

    public fun fire_event(a: u64) acquires T {
        let i = borrow_global_mut<T>(Transaction::sender());

        Event::emit_event(
            &mut i.eh,
            a
        );
    }
}
```

## Account

## Dfinance

[Dfinance] module allows you to work with coins balances, get coins info, also register new tokens, etc.

First of all, Dfinance module presents type for all balances in the system, it's Dfinance::T:

resource struct T<Coin> {
    value: u128
}

The value field contains information about actual balance for specific coin/token, e.g.:

Also, you can create empty coin:

Get denom, decimals and actual value:

And check if it's user token:

Also, you can create you own reasource and make it token too!

## Vector

[Vector](https://github.com/dfinance/dvm/blob/bf457b3145c5e448ece3258bbf67c22326559a12/lang/stdlib/vector.move#L4) module contains functions to work with `vector`
 type.

For example:

```rust
script {
    use 0x0::Vector;

    fun main() {
        let v = Vector::empty<u64>();
        let i = 0;

        loop {
            if (i == 10) {
                break
            };

            Vector::push_back(&mut v, i);
            i = i + 1;
        };
    }
}
```

Vector module great describe in [Move Book](https://move-book.com/chapters/vector.html).

## Signature

[Signature](https://github.com/dfinance/dvm/blob/bf457b3145c5e448ece3258bbf67c22326559a12/lang/stdlib/signature.move#L3) module allows to verify ed25519 signature:

```rust
script {
    use 0x0::Signature;
    use 0x0::Transaction;

    fun main(signature: vector<u8>, pub_key: vector<u8>, message: vector<u8>) {
        let is_verified = Signature::ed25519_verify(signature, pub_key, message);
        Transaction::assert(is_verified, 101);
    }
}
```

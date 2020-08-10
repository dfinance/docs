# Standard Library

Standard **Move VM** library is default modules that already developed and developers can use in developing new modules, scripts.

They all placed on the address **0x1**. So when you import something from **0x1**, you import standard modules, like:

```rust
use 0x1::Account;
use 0x1::Event;
use 0x1::DFI;
use 0x1::Coins;
...
```

You can look for actual standard modules in [dvm](https://github.com/dfinance/dvm/tree/master/stdlib/modules) repository.

## Time

[Time](https://github.com/dfinance/dvm/blob/master/stdlib/modules/time.move) module allows getting current UNIX timestamp of latest block.

Example:

```rust
script {
    use 0x1::Time;

    fun main() {
        let _ = Time::now();
    }
}
```

The method will return u64 value as UNIX timestamp of the latest block.

## Block

[Block](https://github.com/dfinance/dvm/blob/master/stdlib/modules/block.move) module allows getting current blockchain height.

```rust
script {
    use 0x1::Block;

    fun main() {
        let _ = Block::get_current_block_height();
    }
}
```

The method will return u64 value as the height of the latest block.

## Compare

[Compare](https://github.com/dfinance/dvm/blob/master/stdlib/modules/compare.move) module allows comparing two vectors of u8 values \(bytes\).

Comparing two-byte vectors:

```rust
script {
    use 0x1::Compare;

    fun main() {
        let a = x"00";
        let b = x"01";
        assert(Compare::cmp_lcs_bytes(&a, &b) == 0, 101);
    }
}
```

## DFI && Coins

[DFI](https://github.com/dfinance/dvm/blob/master/stdlib/modules/dfi.move) and [Coins](https://github.com/dfinance/dvm/blob/master/stdlib/modules/coins.move) modules allow to get a type of currency that you going to use in your code.

```rust
script {
    use 0x1::Account;
    use 0x1::DFI;
    use 0x1::Coins;

    fun main(sender: &signer, payee: address, dfi_amount: u128, eth_amount: u128, btc_amount: u128, usdt_amount: u128) {
        Account::pay_from_sender<DFI::T>(sender, payee, dfi_amount);
        Account::pay_from_sender<Coins::ETH>(sender, payee, eth_amount);
        Account::pay_from_sender<Coins::BTC>(sender, payee, btc_amount);
        Account::pay_from_sender<Coins::USDT>(sender, payee, usdt_amount);
    }
}
```

## Oracle

[Coins](https://github.com/dfinance/dvm/blob/master/stdlib/modules/coins.move) module also contains oracles functions: get price and has price.

```rust
script {
    use 0x1::Coins;

    fun main() {
        assert(Coins::has_price<Coins::ETH, Coins::USDT>(), 101);
        
        let _ = Coins::get_price<Coins::ETH, Coins::USDT>();
    }
}
```

More about work with oracles can see in our [oracles documentation](../oracles/).

## Event

[Event](https://github.com/dfinance/dvm/blob/master/stdlib/modules/event.move) module allows us to emit events.

Example with emitting event contains provided number:

```rust
script {
    use 0x1::Event;

    fun main(a: u64) {
        Event::emit<u64>(a);
    }
}
```

Or you you can emit event from your module:

```rust
module MyEvent {
    use 0x1::Event;

    struct MyStruct {
        value: u64
    }

    public fun my_event(a: u64) {
        Event::emit(MyStruct {
            value: a
        });
    }
}
```

## Signer

[Signer](https://github.com/dfinance/dvm/blob/master/stdlib/modules/signer.move) module allows to work with the `signer` type. To get address of signer:

```rust
script {
    use 0x1::Signer;

    fun main(sender: &signer) {
        let _ = Signer::address_of(sender);
    }
}
```

Signer type is required for functions which work with resources, address of signer could be useful in case of resource related functions: `borrow_global`, `borrow_global_mut`, `exists`, `move_from`.

Read more about the signer type in [Move Book](https://move-book.com/resources/signer-type.html).

## Account

[Account](https://github.com/dfinance/dvm/blob/master/stdlib/modules/account.move) module allows to work with user balances: get balances, deposit coins/tokens to balances, withdraw them to deposit in another module, etc.

Also, it creates an account, if the account doesn't exist yet, and related data, like event handlers for sending/receiving payments.

A lot of different methods can be used to send tokens from account A to account B, as these one-line methods:

```rust
script {
    use 0x1::Account;
    use 0x1::DFI;

    fun main(sender: &signer, payee: address, amount: u128, metadata: vector<u8>) {
        // Move DFI from sender account to payee.
        Account::pay_from_sender<DFI::T>(sender, payee, amount);

        // Again move DFI, but with metadata.
        Account::pay_from_sender_with_metadata<DFI::T>(sender, payee, amount, metadata);
    }
}
```

Also, you can just withdraw from sender balance and deposit to payee:

```rust
script {
    use 0x1::Account;
    use 0x1::DFI;

    fun main(sender: &signer, payee: address, amount: u128) {
        // Move DFI from sender account to payee.
        let dfi = Account::withdraw_from_sender<DFI::T>(sender, amount);

        // Again move DFI, but with metadata.
        Account::deposit(sender, payee, dfi);
    }
}
```

Or deposit to another module:

```rust
script {
    use {{address}}::Swap;
    use 0x1::DFI;
    use 0x1::Coins;
    use 0x1::Account;

    fun main(sender: &signer, seller: address, price: u128) {
        let dfi = Account::withdraw_from_sender(sender, price);

        // Deposit USDT to swap coins.
        Swap::swap<Coins::USDT, DFI::T>(sender, seller, dfi);
    }
}
```

Also, get a balance:

```rust
script {
    use 0x1::Coins;
    use 0x1::Account;

    fun main(sender: &signer, addr: address) {
        // My balance.
        let my_balance = Account::balance<Coins::ETH>(sender);

        // Someone balance.
        let someone_balance = Account::balance_for<Coins::ETH>(addr);

        assert(my_balance > 0, 101);
        assert(someone_balance > 0, 102);
    }
}
```

For the rest of the features of Account module look at [account.move](https://github.com/dfinance/dvm/blob/master/stdlib/modules/account.move).

## Dfinance

[Dfinance](https://github.com/dfinance/dvm/blob/master/stdlib/modules/dfinance.move) module allows you to work with coins balances, get coins info, also register new tokens, etc.

First of all, Dfinance module presents type for all balances in the system, it's `Dfinance::T`:

```rust
resource struct T<Coin> {
    value: u128
}
```

The value field contains information about actual balance for specific coin/token, e.g.:

```rust
script {
    use 0x1::Account;
    use 0x1::DFI;

    fun main(sender: &signer, amount: u128) {
        // Use DFI::T to get Dfinance::T<DFI::T> contains balance.
        let dfi : 0x1::Dfinance::T<DFI::T> = Account::withdraw_from_sender<DFI::T>(sender, amount);
        Account::deposit_to_sender(sender, dfi);
    }
}
```

Also, you can create an empty coin:

```rust
module BankDFI {
    use 0x1::Dfinance;
    use 0x1::DFI;

    resource struct T {
        balance: Dfinance::T<DFI::T>,
    }

    public fun create(account: &signer)  {
        move_to<T>(account, T {
            balance: Dfinance::zero<DFI::T>()
        })
    }
}
```

Get denom, decimals, and actual value:

```rust
script {
    use 0x1::Dfinance;
    use 0x1::Account;
    use 0x1::DFI;

    fun main(sender: &signer, amount: u128) {
        let dfi = Account::withdraw_from_sender<DFI::T>(sender, amount);

        // Get denom vector<8>.
        let _ = Dfinance::denom<DFI::T>();

        // Get value of withdrawed dfi.
        let value = Dfinance::value(&dfi);

        assert(amount == value, 101);

        Account::deposit_to_sender(sender, dfi);
    }
}
```

And check if it's user token or system coin:

```rust
script {
    use {{address}}::MyToken;
    use 0x1::Dfinance;
    use 0x1::DFI;

    fun main() {
        assert(Dfinance::is_token<DFI::T>() == false, 101);
        assert(Dfinance::is_token<MyToken::T>(), 102);
    }
}
```

Also, you can create your resource and make it token too!

```rust
module MyToken {
    use 0x1::Dfinance;

    resource struct Token {
    }

    public fun create(account: &signer): Dfinance::T<Token>  {
        // Create new token with denom "wow" (hex == 776f77).
        Dfinance::tokenize<Token>(account, 10, 0, x"776f77")
    }
}
```

And also deposit it to your balance:

```rust
script {
    use {{sender}}::MyToken;
    use 0x1::Account;

    fun main(sender: &signer) {
        let new_tokens = MyToken::create(sender);
        Account::deposit_to_sender(sender, new_tokens);
    }
}
```

More documentation about the feature provided by Dfinance module see in [dfinance.move](https://github.com/dfinance/dvm/blob/master/stdlib/modules/dfinance.move).

## Vector

[Vector](https://github.com/dfinance/dvm/blob/master/stdlib/modules/vector.move) module contains functions to work with `vector` type.

For example:

```rust
script {
    use 0x1::Vector;

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

Vector module great describe in [Move Book](https://move-book.com/advanced-topics/managing-collections-with-vectors.html).

## Signature

[Signature](https://github.com/dfinance/dvm/blob/master/stdlib/modules/signature.move) module allows to verify ed25519 signature:

```rust
script {
    use 0x1::Signature;

    fun main(signature: vector<u8>, pub_key: vector<u8>, message: vector<u8>) {
        let is_verified = Signature::ed25519_verify(signature, pub_key, message);
        assert(is_verified, 101);
    }
}
```

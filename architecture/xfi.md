# XFI & Other coins

**XFI** is the main currency in **dfinance** network.

XFI is used for:

* PoS - to stake or delegate XFI.
* Fees - to pay fees in XFI to process transactions.
* Gov - to vote with XFI per proposals: updates, improvements, etc.
* Economic model - users can provide liquidity, loans with XFI.

## Decimals

XFI coin has 18 decimals places, which means 1.0 XFI can be represented as integer as **1000000000000000000**, while 0.**000000000000000001 XFI** as 1 as integer.

When working with **dncli** amounts need to be integers, so convert your amount to integer before executing any command.

As it's the same 18 decimals places, like in ETH, you can use same resources to convert amounts, [like this one](https://www.etherchain.org/tools/unitConverter) \(use ether-wei pair\).

## Smart contracts

XFI is a built-in type inside Dfinance's standard library which you can use to send transactions envolving XFI coin. Here's how it looks like \([link to GitHub](https://github.com/dfinance/dvm/blob/master/stdlib/modules/xfi.move)\):

```rust
module XFI {
    // type representing XFI coin
    struct T {}
}
```

Module can be imported from standard library:

```rust
use 0x1::XFI;
```

The type `XFI::T` inside module `0x1::XFI` can be used as type parameter in generic functions, like in this example:

```rust
script {
     use 0x1::Dfinance;
     use 0x1::Account;
     use 0x1::XFI;

     fun main(sender: &signer, recipient: address, amount: u128) {
         // Withdraw XFI resource from sender balance with provided amount.
         let withdraw : Dfinance::T<XFI::T> = Account::withdraw_from_sender<XFI::T>(sender, amount);

         // Deposit withdrawn XFI balance to recipient address.
         Account::deposit<XFI::T>(sender, recipient, withdraw);
     }
 }
```

Provided script uses functions `withdraw_from_sender<T>` and `deposit<T>` which contain generic type `T`. By passing `XFI::T` as type parameter into these generic functions, we make them work with XFI balances. Note that other coin types can too be passed as type parameters.

You can learn more about generics in Move in the [Move book](https://move-book.com/advanced-topics/understanding-generics.html).

## Other coins

Current mainnet supports other coins along with XFI (just for test purposes):

**IMPORTANT: DON'T DEPOSIT REAL MAINNET ETH VIA PEGZONE**

* **ETH** - ETH representation, can be transfered through [PegZone](../pegzone/).
* **BTC** - simulation of BTC, can be recieved by [faucet](https://wallet.dfinance.co).
* **USDT** - simulation of Tether USDT, can be recieved also by [faucet](https://wallet.dfinance.co).

Same as XFI, all coins can be sent between accounts with CLI:

```text
dncli tx bank send <sender> <recipient> 1xfi
dncli tx bank send <sender> <recipient> 1eth
dncli tx bank send <sender> <recipient> 1btc
dncli tx bank send <sender> <recipient> 1usdt
```

Also, coins types can be imported from `0x1::Coins` module to use in smart contracts:

```rust
script {
    use 0x1::Account;
    use 0x1::Coins;

    fun main(sender: &signer, recipient: address, eth_amount: u128, btc_amount: u128, usdt_amount: u128) {
        Account::pay_from_sender<Coins::ETH>(sender, recipient, eth_amount);
        Account::pay_from_sender<Coins::BTC>(sender, recipient, btc_amount);
        Account::pay_from_sender<Coins::USDT>(sender, recipient, usdt_amount);
    }
}
```

Coins module follows the same pattern as XFI but has multiple types \([link to GitHub](https://github.com/dfinance/dvm/blob/master/stdlib/modules/coins.move)\):

```rust
module Coins {
    struct ETH {}
    struct BTC {}
    struct USDT {}
}
```

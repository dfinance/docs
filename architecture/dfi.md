# DFI & Other coins

**DFI** is the main currency in **dfinance** network.

DFI is used for:

* PoS - to stake or delegate DFI.
* Fees - to pay fees in DFI to process transactions.
* Gov - to vote with DFI per proposals: updates, improvements, etc.
* Economic model - users can provide liquidity, loans with DFI.

## Decimals

DFI coin has 18 decimals places, which means 1.0 DFI can be represented as integer as **1000000000000000000**, while 0.**000000000000000001 DFI** as 1 as integer.

When working with **dncli** amounts need to be integers, so convert your amount to integer before executing any command.

As it's the same 18 decimals places, like in ETH, you can use same resources to convert amounts, [like this one](https://www.etherchain.org/tools/unitConverter) \(use ether-wei pair\).

## Smart contracts

DFI is a built-in type inside Dfinance's standard library which you can use to send transactions envolving DFI coin. Here's how it looks like \([link to GitHub](https://github.com/dfinance/dvm/blob/bf457b3145c5e448ece3258bbf67c22326559a12/lang/stdlib/dfi.move#L8)\):

```rust
module DFI {
    // type representing DFI coin
    struct T {}
}
```

Module can be imported from standard library:

```rust
use 0x0::DFI;
```

The type `DFI::T` inside module `0x0::DFI` can be used as type parameter in generic functions, like in this example:

```rust
script {
     use 0x0::Dfinance;
     use 0x0::Account;
     use 0x0::DFI;

     fun main(sender: &signer, recipient: address, amount: u128) {
         // Withdraw DFI resource from sender balance with provided amount.
         let withdraw : Dfinance::T<DFI::T> = Account::withdraw_from_sender<DFI::T>(sender, amount);

         // Deposit withdrawn DFI balance to recipient address.
         Account::deposit<DFI::T>(sender, recipient, withdraw);
     }
 }
```

Provided script uses functions `withdraw_from_sender<T>` and `deposit<T>` which contain generic type `T`. By passing `DFI::T` as type parameter into these generic functions, we make them work with DFI balances. Note that other coin types can too be passed as type parameters.

You can learn more about generics in Move in the [Move book](https://move-book.com/advanced-topics/understanding-generics.html).

## Other coins

Current testnet supports other coins along with DFI:

* **ETH** - ETH representation, can be transfered through [PegZone](../pegzone/).
* **BTC** - simulation of BTC, can be recieved by [faucet](https://testnet.dfinance.co).
* **USDT** - simulation of Tether USDT, can be recieved also by [faucet](https://testnet.dfinance.co).

Same as DFI, all coins can be sent between accounts with CLI:

```text
dncli tx bank send <sender> <recipient> 1dfi  --fees 1dfi
dncli tx bank send <sender> <recipient> 1eth  --fees 1dfi
dncli tx bank send <sender> <recipient> 1btc  --fees 1dfi
dncli tx bank send <sender> <recipient> 1usdt --fees 1dfi
```

Also, coins types can be imported from `0x0::Coins` module to use in smart contracts:

```rust
script {
    use 0x0::Account;
    use 0x0::Coins;

    fun main(sender: &signer, recipient: address, eth_amount: u128, btc_amount: u128, usdt_amount: u128) {
        Account::pay_from_sender<Coins::ETH>(sender, recipient, eth_amount);
        Account::pay_from_sender<Coins::BTC>(sender, recipient, btc_amount);
        Account::pay_from_sender<Coins::USDT>(sender, recipient, usdt_amount);
    }
}
```

Coins module follows the same pattern as DFI but has multiple types \([link to GitHub](https://github.com/dfinance/dvm/blob/bf457b3145/lang/stdlib/coins.move)\):

```rust
module Coins {
    struct ETH {}
    struct BTC {}
    struct USDT {}
}
```


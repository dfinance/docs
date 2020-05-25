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

As it's the same 18 decimals places, like in ETH, you can use same resources to convert amounts, [like this one](https://www.etherchain.org/tools/unitConverter) (use ether-wei pair).

## Smart contracts

The Dfinance has a smart contract module that represents DFI balance as a structure type, you can look at [DFI module](https://github.com/dfinance/dvm/blob/bf457b3145c5e448ece3258bbf67c22326559a12/lang/stdlib/dfi.move#L8) in the standard library. This module can be imported so:

```rust
use 0x0::DFI;
```

The type `DFI::T` inside module `0x0::DFI` can be used in generic functions, like in the next script example:

```rust
script {
    use 0x0::Account;
    use 0x0::DFI;

    fun main(recipient: address, amount: u128) {
        // Withdraw DFI resource from sender balance with provided amount.
        let withdraw = Account::withdraw_from_sender<DFI::T>(amount);
        
        // Deposit withdrawn DFI balance to recipient address.
        Account::deposit<DFI::T>(recipient, withdraw);
    }
}
```

Provided script used function `withdraw_from_sender<T>` and `deposit<T>` that contains generics `T`, using generics function can understand with which kind of resource/type it should work, in our case, it's `DFI::T` structure type, that represents user DFI balance.

More about generics you can read in [Move book](https://move-book.com/chapters/generics.html).

## Other coins

Current testnet supports other coins together with DFI:

* **ETH** - ETH representation, can be received using [PegZone](/pegzone/README.md).
* **BTC** - simulation of BTC, can be recieved by [faucet](https://testnet.dfinance.co).
* **USDT** - simulation of Tether USDT, can be recieved also by [faucet](https://testnet.dfinance.co).

Same as DFI, all coins can be transferred between accounts with CLI:

```text
dncli tx bank send <sender> <recipient> 1dfi  --fees 1dfi
dncli tx bank send <sender> <recipient> 1eth  --fees 1dfi
dncli tx bank send <sender> <recipient> 1btc  --fees 1dfi
dncli tx bank send <sender> <recipient> 1usdt --fees 1dfi
```

Also, coins types can be imported from `0x0::Coins` module to use in smart contracts, see the next example:

```rust
script {
	use 0x0::Account;
	use 0x0::Coins;
	
	fun main(recipient: address, eth_amount: u128, btc_amount: u128, usdt_amount: u128) {
		Account::pay_from_sender<Coins::ETH>(recipient, eth_amount);
		Account::pay_from_sender<Coins::BTC>(recipient, btc_amount);
		Account::pay_from_sender<Coins::USDT>(recipient, usdt_amount);
	}
}
```

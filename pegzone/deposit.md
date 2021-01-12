# Deposit

In this documentation explained how to deposit testnet ropsten ETH from your account to **dfinance**.

**Important**: use only ropsten ETH, as all mentioned contracts deployed in ropsten ethereum testnet, and this is experimental software, you can lose your ETH.

## Using wallet

Required:

* [Metamask](https://metamask.io/)
* Ropsten ETH - request from some known faucet, for example, use this [one](https://faucet.ropsten.be/).

Go to [wallet](https://wallet.dfinance.co) portal and recover/create an account.

Then click on **"Transfer"** button to transfer **ETH** from your Metamask account to **dfinance** blockchain.

After 100+ confirmations you ETH will appear on your account in **dfinance**.

## By sending transaction

Using [eth-peg-zone](https://github.com/dfinance/eth-peg-zone) you can write a script in a preferred language, or using [web3](https://github.com/ethereum/web3.js/), or even use any other wallet with support of transaction encoding.

Just send an ethereum transaction to **Bridge** contract address with ETH and signature/parameters of [exchange function](https://github.com/dfinance/eth-peg-zone/blob/cf1ded5369af3c021c47f4bcdea76266462e20af/contracts/Bridge.sol#L199) in **Bridge** contract.

Parameters of **exchange** function:

* **\_currencyId** - you can query from contract, or use **0** for ETH.
* **\_recipient** - your **"wallet1..."** address converted in right format. [Example](https://github.com/dfinance/eth-peg-zone/blob/cf1ded5369af3c021c47f4bcdea76266462e20af/helpers/wb.js) how convert address in the same repository.
* **\_amount** - amount of coins/tokens.

## Contracts

Currenty deployed Bridge contract can be found by address: [0xE2FD9D943FFC4adD2F5952025f32E9a984B3637b](https://ropsten.etherscan.io/address/0xE2FD9D943FFC4adD2F5952025f32E9a984B3637b).


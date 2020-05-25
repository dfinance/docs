# PegZone

This document described how PegZone works in the case of **dfinance** protocol.

## Introduction

**PegZone** is a protocol allowing to cross-chain values transfers between chains.

Currently dfinance supports Ethereum blockchain: ETH and ERC20 tokens transfers. The protocol built on smart contracts and allowing to lock ETH and ERC20 tokens in smart contract in Ethereum blockchain. This smart contract calling **Bridge** and manages by approved validators list \(Proof-Of-Authority\).

Once validators nodes see new values locked in the smart contract, validators release equal amount minus fees into dfinance blockchain, on provided address in dfinance network.

Parameters of current **PegZone**:

* Listed coins/tokens: **ETH**.
  * **ETH** parameters:
    * Maximum capacity \(ETH\): 1 000 000.
    * Minimum exchange \(WEI\): 1 000.
    * Fees: 0.1%.
* Minimum confirmations: 100. 

PegZone takes fees only when your deposit your coins/tokens to dfinance, when you withdraw validators don't take fees at all.

Nice UML to see how it works:

![DFI to WETH UML](https://raw.githubusercontent.com/dfinance/eth-peg-zone/master/res/eth_wei_flow.png)

For more details, include smart contracts, check our [eth-peg-zone](https://github.com/dfinance/eth-peg-zone) repository.

**IMPORTANT:** contracts deployed to ropsten testnet, so use only ropsten ETH. This is very experimental software, it could contain critical issues and bugs, use only testnet coins/tokens.

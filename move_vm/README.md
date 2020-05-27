# Move VM

This document describes how Move VM works, which types of smart contracts transaction supported by dnode, and how to work with smart contracts in case of **dfinance** blockchain.

## Introduction

Dfinance uses Move VM to implement smart contracts functional: allow users to develop, deploy and execute their smart contracts. The current implementation works due to **dnode** implementation with connection to [**dvm**](https://github.com/dfinance/dvm). **DVM** is the implementation of **Move VM** with support of GRPC protocol, that allows communication between dvm and dnode.

Move VM is developed by [Libra](https://libra.org/), a blockchain platform by Facebook. The main difference from other existing virtual machines, like EVM, it's:

* Support of Move language - resource oriented-language developed for Move VM.
* Move VM is resource-oriented: developer can define a resource, place resource under an account and move resources between accounts. But resource can be never duplicated, reused or discarded.
* Bytecode verification. To be executable code must be verifiable.
* Support of transaction-as-script: transaction can contain user script, which won't be published in the blockchain as a smart contract, but instead will be executed. It gives blockchain users more power and flexibility by allowing them to do multiple operations within single transaction written in Move language.

All this makes Move VM much safer than other blockchains VMs. For example, the famous DAO hack just couldn't happen, because of the resource model and bytecode verification.

We recommend reading next parts of this documentation together with [Move book](move_book.md) which is written by one of our team members.


# Dfinance Move VM

This document describes how Move VM works, which types of smart contracts transaction supported by dnode, and how to work with smart contracts in case of **dfinance** blockchain.

## Move VM

Dfinance uses Move VM to implement smart contracts functional: allow users to develop, deploy and execute their smart contracts. The current implementation works due to **dnode** implementation with connection to **[dvm](https://github.com/dfinance/dvm)**. **DVM** is the implementation of **Move VM** with support of GRPC protocol, that allows communication between dvm and dnode.

Move VM is developed by [Libra](https://libra.org/), a blockchain platform by Facebook. The main difference from other existing virtual machines, like EVM, it's:

- Move VM resource-oriented, a developer can define a resource, place resource under an account, move resources between accounts. The resource can be never duplicated, reused or discarded.
- Byte code verification. Not verified code can't be executed via VM.
- Supports transaction scripting, means transaction could contain user script, that will be not stored in the blockchain as smart contract, but will be executed during current transaction processing.

All this makes Move VM much safe than other blockchains VMs. For example, the famous DAO hack just couldn't happen, because of the resource model and byte code verification.
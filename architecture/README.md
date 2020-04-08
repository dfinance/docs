# Architecture

Dfinance network consists of several main components:

* **dnode** - blockchain node, that's realize core layer, include [Tendermint](https://tendermint.com/) consensus, Proof Of Stake modules, oracles functional, VM functional, etc. **Dnode** is built on [Cosmos SDK](https://github.com/cosmos/cosmos-sdk), and has it's own [repository](https://github.com/dfinance/dnode).
* **dncli** - command-line interface to iteract with dnode, also allows to launch REST API server, has same repository that [dnode](https://github.com/dfinance/dnode).
* **dvm** - Move Virtual Machine by [Libra](https://developers.libra.org/) Facebook packed as GRPC server. Allows to execute smart contracts via GRPC protocol, connects to **dnode** to read data from storage, **dnode** connects to VM to execute smart contracts. Has it's own [repository](https://github.com/dfinance/dvm).
* **compiler** - Mvir/Move language compiler packed in GRPC server, also requires **dnode** to functional correctly.

In next documentation parts all components reviewed with details, so go on.


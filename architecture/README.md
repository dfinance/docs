# Architecture

Dfinance network consists of several main components:

* **dnode** - blockchain node - the core layer. Includes [Tendermint consensus](https://tendermint.com/), Proof Of Stake modules, oracles functional, VM functional, etc. **dnode** is built with [Cosmos SDK](https://github.com/cosmos/cosmos-sdk). See [dnode repository on GitHub](https://github.com/dfinance/dnode).
* **dncli** - command-line interface to iteract with dnode, also allows launching REST API server, has same repository that [dnode](https://github.com/dfinance/dnode).
* **dvm** - Move Virtual Machine by [Libra](https://developers.libra.org/) packed as gRPC server. Allows smart constracts execution via gRPC. Connects to **dnode** to read data from storage, **dnode** connects to VM to execute smart contracts. [See repository](https://github.com/dfinance/dvm). Also, contains compiler of Move language. Requires **dnode** for correct functioning.

You'll find more precise description of every component in other sections of this documentation.


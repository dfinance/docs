# Addresses

Dfinance protocol uses [Bech32](https://en.bitcoin.it/wiki/Bech32) address format. Bech32 encoding provides robust integrity checks on data and the human readable part (HRP) provides contextual hints that can assist UI developers with providing informative error messages.

* Human readable part is called a prefix and in the case of dfinance it's `wallet`. 
* Default HDPath for dfinance addresses is `44'/118'/0'/0/0`.

```text
wallet173pur9yxzauc7pccwwpk7whnf30czvf53wkcyn # Example of dfinance address, contains prefix 'wallet', then '1' and address bytes. 
```

Use [secp256k1](https://en.bitcoin.it/wiki/Secp256k1) algorithm to generate public and private keys, then use Bech32 to create addresses.

Mnemonic based keys supported by [bip39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) and [bip32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki) implementations.

Example in Golang:

```go
package main

import (
	"encoding/hex"
	"fmt"

	"github.com/cosmos/cosmos-sdk/crypto/keys/hd"
	sdk "github.com/cosmos/cosmos-sdk/types"
	"github.com/cosmos/go-bip39"
	"github.com/tendermint/tendermint/crypto/secp256k1"
)

func main() {
	// Configure Cosmos SDK.
	prefix := "wallet"
	config := sdk.GetConfig()
	config.SetBech32PrefixForAccount(prefix, prefix + sdk.PrefixPublic)
	config.Seal()

	// Generate new address from new private key.
	privKey := secp256k1.GenPrivKey()
	pubKey  := privKey.PubKey()
	addr    := sdk.AccAddress(pubKey.Address())

	fmt.Printf("Private key: %s\nPublic key: %s\nAddress: %s\n", hex.EncodeToString(privKey[:]), hex.EncodeToString(pubKey.Bytes()), addr)

	// Generate address from new mnemonic.
	entropy, err := bip39.NewEntropy(256)
	if err != nil {
		panic(err)
	}

	passphrase := "12345678" // Replace with your passphrase.
	hdPath := "44'/118'/0'/0/0"
	mnemonic, err := bip39.NewMnemonic(entropy)
	if err != nil {
		panic(err)
	}

	fmt.Printf("\nNew generated mnemonic is: %s\n", mnemonic)

	seed, err := bip39.NewSeedWithErrorChecking(mnemonic, passphrase)
	if err != nil {
		panic(err)
	}

	masterPrivKey, ch := hd.ComputeMastersFromSeed(seed)

	// If hdPath is empty, just use masterPrivKey[:], don't need to derive.
	derivedPrivKey, err := hd.DerivePrivateKeyForPath(masterPrivKey, ch, hdPath)
	if err != nil {
		panic(err)
	}

	derivedPubKey := secp256k1.PrivKeySecp256k1(derivedPrivKey).PubKey()
	derivedAddr   := sdk.AccAddress(derivedPubKey.Address())

	fmt.Printf("Private key: %s\nPublic key: %s\nAddress: %s\n", hex.EncodeToString(derivedPrivKey[:]), hex.EncodeToString(derivedPubKey.Bytes()), derivedAddr)
}
```

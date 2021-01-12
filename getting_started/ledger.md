# Ledger Support

**dncli** and [wallet](https://wallet.dfinance.co) both supports [Ledger](https://www.ledger.com/) via Cosmos Ledger App.

Install Cosmos Ledger App using official [instruction](https://support.ledger.com/hc/en-us/articles/360013713840-Cosmos-ATOM-).

## Wallet

Launch Cosmos App on your ledger device and visit [wallet](https://wallet.dfinance.co), login by clicking on 'Unlock with Ledger' button and follow instructions appear on your screen.

## dncli

To add account to **dncli** use next command:

```text
dncli keys add my-account --index 0 --ledger
```

It will add a new account to your ledger, if you need another account just increase the index parameter.

## Offline signatues

Also, if you want to sign something with **dncli**, for example, your validator creation transaction from remote server, you can just generate that transaction by adding `--generate-only` flag and use signer address in `--from` flag, for example:

```text
dncli tx staking create-validator ..... --generate-only --from [adress] > tx.json
```

See generated transaction:

```text
cat tx.json
```

You can sign and broadcast the transaction using **dncli** from your local machine:

```text
# sign transaction
dncli sign tx.json --from [address] > signed.json 

# broadcast transaction
dncli tx broadcast signed.json
```

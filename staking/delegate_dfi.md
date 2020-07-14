# Delegate DFI

This documentation describing how to delegate your DFI to validators in **dfinance** network.

Any user, who has DFI on his balance, can delegate his DFI to one validator or multiplay validators using different DFI coins.

To find validators and delegate you can use our [explorer](https://explorer.testnet.dfinance.co/validators) and [wallet](https://wallet.testnet.dfinance.co/validators), or using **dncli**:

```bash
# The staking module contains delegators/validators commands.

# Staking query commands.
dncli q staking --help

# Staking transaction commands.
dncli tx staking --help
```

## How to delegate DFI

First of all, choose the validator you want to delegate your DFI and send a `delegate` transaction to the network.

Use [wallet](https://wallet.testnet.dfinance.co/) to delegate or continue with **dncli**.

See the list of validators:

```bash
dncli q staking validators
```

Choose validator to delegate and send transaction:

```bash
dncli tx staking delegate [validator-addr] [amount] --from [account]
```

As you see, when you delegate, you must provide the following arguments:

    * [validator-addr] - validator operator address you want to delegate (has `walletvaloper1` prefix instead of standard `wallet1`). 
    * [amount] - DFI amount to delegate. 
    * [account] - delegator account.

Once your transaction will be executed and confirmed, see your account lost that amount of DFI that you delegated:

```bash
dncli q account [delegator-addr]
```

Also, see new delegation done by your account:

```bash
dncli q staking delegations [delegator-addr]
```

Delegated DFI stored in the `staking` module, and you can get them back by [unbond](#how-to-unbond-undelegate) your coins. 

Since you have your first delegation, you can start getting rewards, read more in [Rewards & Inflation](/staking/rewards_inflation.md). Also, if you chose validators, that missing blocks or double sign them (trying to attack network), you can be punished the same as your validator, see [Slashing](/staking/slashing.md) to see what's risks brings delegation.

## How to unbond (undelegate)

When you want to get staked DFI back, you can send unbond (undelegate) transaction to the network. The unbond procedure will return DFI to the user account in time, usually, it's 3 weeks unbonding period (21 days), but for testnet, it's only 1 day. During this period delegator still can be slashed for potential misbehaviors committed by the validator before the unbonding process started.

Use [wallet](https://wallet.testnet.dfinance.co/your_validators) to unbond or continue with **dncli**.

Find delegations from your address:

```bash
dncli q staking delegations [delegator-addr]
```

Where:

    * [delegator-addr] - delegator address (usually your account address).

Choose a validator you want to unbond and run:

```bash
dncli tx staking unbond [validator-addr] [amount] --from [account]
```

Once transaction confirmed, check the status of unbonding DFI coins:

```bash
dncli q staking unbonding-delegations [delegator-addr]
```

Once the unbonding period completed you will get your DFI coins back on the account. You can have maximum **7** unbonding stakes and redelegations at the same time.

## Redelegate

In situations when you don't want to unbond your DFI and at the same time don't want to continue delegate to a specific validator, or you want to delegate part of your staked DFI to another validator, you can redelegate.

Use [wallet](https://wallet.testnet.dfinance.co/your_validators) to redelegate or continue with **dncli**.

Run redelegate command:

```bash
dncli tx staking redelegate [src-validator-addr] [dst-validator-addr] [amount] --from [account]
```

Where:

    * [src-validator-addr] - validator address which already contains delegated DFI.
    * [dst-validator-addr] - new validator address, which we want to redelegate.
    * [amount] - DFI amount to redelegate.
    * [account] - delegator account.

Once your transaction confirmed, check how your delegations changed:

```bash
dncli q staking delegations [delegator-addr]
```

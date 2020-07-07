# Delegate DFI

Any user, who has DFI on his balance, can delegate his DFI to one or multiplay validators. 

Find validators and delegate you both can from our [explorer](https://explorer.testnet.dfinance.co/validators) and [wallet](https://wallet.testnet.dfinance.co/validators), and also using **dncli**, see module staking:

```bash
# To see staking query commands.
dncli query staking --help

# To see staking transaction commands.
dncli tx staking --help
```

## How to delegate DFI

First of all, choose the validator you want to delegate your DFI and send a special transaction `delegate` to the network.

<!-- need to fix it here once Igor complete staking wallet -->
You can send such a transaction using our [wallet](https://wallet.testnet.dfinance.co/), by clicking on your liked validator, click on button **"Stake"**, follow instructions. and send delegate transaction.

Also, you can do it using **dncli**, see list of validators using next command:

```bash
dncli query staking validators
```

Choose validator to delegate and send delegate transaction using the next command:

```bash
dncli tx staking delegate [validator-addr] [amount] --from [account]
```

As you see, when you delegate, you have to provide a few parameters:

    * [validator-addr] - validator operator address you want to delegate (has `walletvaloper1` prefix instead of standard `wallet1`). 
    * [amount] - DFI amount to delegate. 
    * [account] - delegator account.

Once your delegation transaction will be executed and confirmed, you can check, that your account lost that amount of DFI that you delegated:

```bash
dncli query account [delegator-addr]
```

And now you can see new delegation done by your account:

```bash
dncli query staking delegations [delegator-addr]
```

Now that DFI stored in the staking module, and you can get them back by unbond your delegated coins. 

Since you have a delegation running, you can start getting rewards, read more in [Rewards & Inflation](/staking/rewards_inflation.md). Also, if your chosen validators start missing blocks or double sign them (trying to attack network), you can be slashed too, as their delegator, see [Slashing](/staking/slashing.md) to see what's risks brings delegation.

## How to unbond (undelegate)

When the user wants to get his staked DFI back, the user can send another transaction to unbond (undelegate) DFI. This procedure will return DFI to the user account in a set period, usually, it's 3 weeks unbonding period (21 days). During this period delegator still can be slashed potential misbehaviors committed by the validator before the unbonding process started.

<!-- need to fix it once we see wallet flow -->
You can unbond your DFI using [wallet](https://wallet.testnet.dfinance.co/your_validators), just found a validator which you already delegated DFI, follow instructions, and send the transaction to unbound this part of your DFI stake.

Also, you can use **dncli**, see all delegations done by your address:

```bash
dncli query staking delegations [delegator-addr]
```

Where:

    * [delegator-addr] - delegator address (usually your account address).

Choose a validator you want to unbond and run next command:

```bash
dncli tx staking unbond [validator-addr] [amount] --from [account]
```

After your transaction will be executed, you can check the status of your unbonding DFI coins:

```bash
dncli query staking unbonding-delegations [delegator-addr]
```

### Redelegate

In situations when you don't want to unbond your DFI and at the same time don't want to continue delegate to a specific validator, or you want to delegate part of your already delegated DFI to another validator, you can redelegate your coins.


<!-- need to fix it once we see wallet flow -->
You can redelegate staked DFI using [wallet](https://wallet.testnet.dfinance.co/your_validators), just choose a delegation that you want to redelegate and click on 'Redelegate', follow instructions, and send redelegate transaction.

```bash
dncli tx staking redelegate [src-validator-addr] [dst-validator-addr] [amount] --from [account]
```

Where:

    * [src-validator-addr] - validator address which already contains delegated DFI.
    * [dst-validator-addr] - new validator address, which we want to redelegate.
    * [amount] - DFI amount to redelegate.
    * [account] - delegator account.

After redelegate transaction will be confirmed and executed, you can check how your delegations changed:

```bash
dncli query staking delegations [delegator-addr]
```

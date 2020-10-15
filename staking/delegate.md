# Delegate sXFI & LPT

This section describes process of delegation of sXFI and LPT to validators in **dfinance** network.

Any user who has sXFI or LPT on his account's balance can delegate his coins/tokens to one or multiple validators (though a single sXFI or LPT cannot be used twice).

To find validators and delegate you can use our [explorer](https://explorer.testnet.dfinance.co/validators) and [wallet](https://wallet.testnet.dfinance.co/), or use **dncli**:

```bash
# The staking module contains delegators/validators commands.

# Staking query commands.
dncli q staking --help

# Staking transaction commands.
dncli tx staking --help
```

## How to delegate sXFI & LPT

First, choose the validator you want to delegate your sXFI or LPT to and send `delegate` transaction to the network.

Use [wallet](https://wallet.testnet.dfinance.co/) to delegate.
Or use **dncli**:

See the list of validators:

```bash
dncli q staking validators
```

Choose validator and check validator extended info:

```bash
 dncli q distribution validator [validator-addr]
```

**Important:** Find `max_bonding_delegations_lvl` parameter and compare with `bonding_tokens`, if you do small math `max_bonding_delegations_lvl` - `bonding_tokens`, you are going to see MAXIMUM amount of sXFI you can delegate to validator. It doesn't affect LPT staking, you can delegate so much LPT as you want.

Choose optimal amount of sXFI or LPT to delegate and send transaction:

```bash
dncli tx staking delegate [validator-addr] [amount] --from [account]
```

As you see, when you delegate, you provide the following arguments:

- [validator-addr] - validator operator address you want to delegate (has `walletvaloper1` prefix instead of standard `wallet1`);
- [amount] - sXFI or LPT amount to delegate. e.g. 2500000000000000000000sxfi or 100000000000000000000lpt.
- [account] - delegator account.

Example:

```bash
dncli tx staking delegate walletvaloper10tyyh... 15000000000000000000000sxfi --from [account] # Delegate 15k sXFI.
dncli tx staking delegate walletvaloper10tyyh... 100000000000000000000lpt --from [account] # Delegate 100 LPT.
```

Once your transaction executed and confirmed, check your account balance - you'll see that it has changed - sXFI you've delegated are not accesible (but not spent - see below):

```bash
dncli q account [delegator-addr]
```

Also, see new delegation done by your account:

```bash
dncli q staking delegations [delegator-addr]
```

Delegated sXFI or LPT coins are stored in the `staking` module, and you can get them back by [unbonding](#how-to-unbond-undelegate) your coins.

Since you have your first delegation, you can start getting rewards, read more in [Rewards & Inflation](/staking/rewards_inflation.md). Also, if you choose validators, who are missing blocks or double sign them (trying to attack the network), you can also be punished for supporting him. See [Slashing](/staking/slashing.md) to see what risks delegation brings.

## How to unbond (undelegate)

When you want to get staked sXFI or LPT back, you can send *unbond* (undelegate) transaction to the network, this will start *unbonding procedure*. Unbonding procedure will take some time, usually, it's 7 days unbonding period for both sXFI and LPT. During this period the delegator still can be slashed for potential misbehaviors committed by the validator before the unbonding process ends.

Use [wallet](https://wallet.testnet.dfinance.co/) to unbond or continue with **dncli**.

Find delegations from your address:

```bash
dncli q staking delegations [delegator-addr]
```

Where:

- [delegator-addr] - delegator address (usually your account address).

Choose validator you want to unbond and run:

```bash
dncli tx staking unbond [validator-addr] [amount] --from [account]
```

Once transaction confirmed, check the status of unbonding sXFI/LPT coins:

```bash
dncli q staking unbonding-delegations [delegator-addr]
```

Once the unbonding period is completed you will get your sXFI or LPT coins back on the account. You can have maximum **7** unbonding stakes and redelegations at the same time.

## Redelegation

In situations when you don't want to unbond your sXFI and at the same time don't want to delegate to a specific validator, or if you want to delegate part of your staked sXFI or LPT to another validator, you can redelegate.

Use [wallet](https://wallet.testnet.dfinance.co/your_validators) to redelegate or continue with **dncli**.

Run redelegate command:

```bash
dncli tx staking redelegate [src-validator-addr] [dst-validator-addr] [amount] --from [account]
```

Where:

- [src-validator-addr] - validator address which already contains delegated sXFI.
- [dst-validator-addr] - new validator address, which we want to redelegate.
- [amount] - sXFI or LPT amount to redelegate.
- [account] - delegator account.

Once your transaction is confirmed, check your delegations:

```bash
dncli q staking delegations [delegator-addr]
```

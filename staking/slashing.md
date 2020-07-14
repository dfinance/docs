# Slashing

This documentation describing the slashing mechanism that carries about punishments on validators and their delegators.

As secure of dfinance network depends on validators, fewer performance validators or attackers validators should be punished. 

Each active validator has to pre-vote each new proposed block and also propose to block himself when his time comes, this is how works [Tendermint](https://tendermint.com/). If a validator doesn't sign an approved block or miss his proposed round, such validator could be slashed (because of compromises network security). 

More about network security you can read in Tendermint documentation and other links we provide in [More](/staking/more.md) section. Right now let's see how validator could be slashed.

To see slashing commands use **dncli** `slashing` module:

```bash
# To see query commands.
dncli q slashing

# To see transactions commands.
dncli tx slashing
```

Slashing parameter described in genesis blocks, you can look at them:

```bash
dncli q slashing params 
```

Current testnet configuration looks so:

```json
{
  "signed_blocks_window": "100",
  "min_signed_per_window": "0.500000000000000000",
  "downtime_jail_duration": "600000000000",
  "slash_fraction_double_sign": "0.050000000000000000",
  "slash_fraction_downtime": "0.010000000000000000"
}
```

Where:

  * `min_signed_per_window` - the percent of blocks must be signed by validator during blocks window, currently 50%.
  * `signed_blocks_window` - size of blocks window, currently 100 blocks.
  * `downtime_jail_duration` - jail duration while validator can't send unjail transaction, currently 600000000000 nanoseconds (10 minutes).
  * `slash_fraction_double_sign` - the percent of stake that validator/delegator loose in case validator double sign block, currently 5%.
  * `slash_fraction_downtime` - the percentage of stake that validator/delegator loose in case of validator downtime.

If validators slashed for downtime (missed signatures/proposals), validator and his delegators loose part of their stakes (see `slash_fraction_downtime`), also, such validator will be  `jailed`, after jail period (see `downtime_jail_duration`) validator can send transaction to [unjail](#unjail) himself. While validator jailed, he can't propose/sign new blocks and participate in the consensus at all.

If validator double sign block, what's much more critical than downtime, then validator and his delegators will lose part of their stakes (see `slash_fraction_double_sign`), also, validator becoming `jailed` forever, and tombstoned -  removed from validators list and can't participate in the consensus at all anymore.

To see your validator slashing statistic, use **dncli**:

```dncli
dncli q slashing signing-info [validator-conspub]
```

Where: 

  * [validator-conspub] - is consensus public key of validator, can be found in the output using querying validator commands, like `dncli q staking validators`.

## Unjail 

If your validator slashed for downtime, it will be jailed. This is a period when validators can't produce blocks and participate in consensus. To become active again, a validator must send `unjail` transaction. Such a transaction can be sent only after the jail period completed (see `downtime_jail_duration`), currently, it's 10 minutes.

To send unjail transaction use **dncli**:

```bash
dncli tx slashing unjail --from [account]
```

If the transaction processed successfully, your validator will be unjailed.

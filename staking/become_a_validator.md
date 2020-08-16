# Become a validator

Any user can become a validator on dfinance network.

Being one means:

1. Having a full-sync (and always-online) node
2. Having account registered as validator
3. Generating new blocks when having enough votes to be in the top of active validators

<!-- By becoming a validator means register account as a validator, up a full node, and generate new blocks, if the validator has enough voting power to be in the top active validators. -->

## Node Setup

To become a validator:

1. Set up a full node, using a dedicated server or cloud services. The validator machine must have a good performance and latency and must be always online.
2. Install **dnode**/**dncli** on your node using [dnode](/architecture/dnode.md) and [dncli](/architecture/dncli.md) instructions.
3. Ensure that your node is in sync with the rest of the network by requesting the latest testnet block. Request testnet status to see latest block or use [explorer](https://explorer.testnet.dfinance.co/):

Use these commands to compare block height on your node and on testnet:

```bash
# Both commands require jq.

# Get latest block from testnet
curl https://rest.testnet.dfinance.co/blocks/latest  | jq '.block.header.height'

# Get latest block from local node
curl localhost:1317/blocks/latest  | jq '.block.header.height'
# Or with dncli.
dncli q block
```

Once the difference between your height and testnet height is small (just a few blocks), you can start the process of registering your account as a validator.

### Important

Validator's private key is stored under `~/.dnode/config/` path and contains the key file:

* `priv_validator_key.json` - private key of validator, backup it and don't miss, as it's the only way to access your validator.

## Create validator

After you setup a full-node time to create a validator. Make sure you made a backup of `priv_validator_key.json` and store it in a safe place.

Requirements for next steps:

* Synchronized **dnode** (full-node).
* Opened **26656 port** on your machine.
* Installed **dncli** and **dnode** (with docker or not).
* Account with at least 2.0 XFI.

During validator creation we will need both **dnode** and **dncli** working from your machine launched with full-node, so make sure they both available from the console:

```bash
dnode version  # Check dnode available.
dncli version  # Check dncli available.
```

In case you're using docker, **dncli** still should be installed in your console (see instruction), while **dnode** you can get inside docker:

First, let's try to find validator public key:

```bash
dnode tendermint show-validator
```

In case you are using [testnet-bootstrap](https://github.com/dfinance/testnet-bootstrap) docker-compose:

```bash
cd testnet-bootstrap # Go to testnet bootstrap directory.
docker-compose exec dnode bash # Run bash inside a dnode container.
dnode tendermint show-validator # Print validator public key, copy it.
exit # Exit from container.
```

If you see the validator consensus public key we can continue, copy the validator consensus address, we will need it in the next step. If you don't see validator public key, you have to init your **dnode** instance, see dnode [documentation](/architecture/dnode.md).

**dncli** contains staking module, that required both for validator/delegator operations, see help:

```bash
dncli q staking
dncli tx staking
```

Let's create a validator and official register it in the network:

```bash
dncli tx staking create-validator \
  --amount=1000000000000000000xfi \
  --pubkey=<pub_key> \
  --moniker=<moniker> \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --from <account>
```

Where:

* `amount` - XFI amount to self-stake, currently **1.0 XFI**.
* `pubkey` - validator consensus public key received during `dnode tendermint show-validator` command.
* `moniker` - your dnode moniker, use one you used during `dnode init <moniker>` command. You can see it in `~/.dnode/config/config.toml` file
* `commission-rate` - how much your validator is going to take a commission from received rewards/fees, currently 10%.
* `commission-max-rate` - maximum that validator can take as comission.
* `commission-max-change-rate` - how percent per day validator can change comission, currently 1% per day.
* `from` - an account that is going to send transaction and will self-stake coins for your validator, also, you can use this account to manage your validator later.

Replace command values with your own and send the transaction to the network.  Most interesting parameters are commission related, we will discuss them later.
Once the transaction will be confirmed, you will become a validator in the dfinance network.

Congrats!

To see list of validators use command:

```bash
dncli q staking validators
```

It is the start of the road to become an active validator in the top 31 and start getting rewards and fees for generated blocks. To increase your voting power you can delegate coins to your validator, see [Delegate XFI](/staking/delegate_dfi.md).

By changing commission params, making it less, you become more profitable for delegators to vote for you (see [Rewards & Inflation](/staking/rewards_inflation.md)), so it could be a good start to bring attention. Don't forget to always monitor your validator, it must be online most of the time, if you miss too many blocks, you can be [unbonded](#unbonding) and [slashed](/staking/slashing.md).

## Socialize your validator

Having validator in **dfinance** network is not only about setting up validator node and producing blocks, but it's also public work, delegators (especially from the community) want to know to whom they delegate their XFI.

This way you can update your validator with social parameters, that other network users can read:

```bash
dncli tx staking edit-validator \
  --moniker="pirate_boris" \
  --website="https://dfinance.co" \
  --identity=A4094774EFC4F6FC \
  --security-contact="boris@dfinance.co" \
  --details="money printing machine!" \
  --from <account>
```

All flags are optional. See arguments and then replace them with your own:

  * `moniker` - moniker of your validator.
  * `website` - website of your validator.
  * `identity` - can be used to verify identity with systems like [Keybase](https://keybase.io/) or UPort. Also, it's a great way to retrieve your Keybase avatar. See how to [generate identity](#generate-identity-using-keybase) using keybase.
  * `details` - few words about you.
  * `security-contact` - a way to send your email/message.

### Generate identity using Keybase

1. Go to [Keybase.io](https://keybase.io/).
2. Create your account and download the app.
3. Add pgp key to your account using the terminal. Click `add a PGP key` and follow instructions.
4. After generating keys, you will get a 16 letters ID, like `ID A4094774EFC4F6FC`.
5. Use ID as a value for `identity`.

## Change comission parameters

If you want to reduce/increase your commission as a validator, you can make another transaction with the new commission rate. Don't forget, that you can't change commission more than on `commission-max-change-rate` per day.

```bash
dncli tx staking edit-validator \
  --commission-rate="0.11" \
  --from <account>
```

Also, an important parameter is minimum self delegation (`--min-self-delegation`), you can change it also, as more self delegation you have, as more trust you will have in eyes of delegators. You can only increase `--min-self-delegation`.

Example (min self delegation to 250000 XFI):

```bash
dncli tx staking edit-validator \
  --min-self-delegation="250000000000000000000000" \
  --from <account>
```

## Statuses

Validators could have the following statuses:

* `Bonded (2)` - active validators in the top 31. Generate blocks, receiving rewards and fees.
* `Unbonded (0)` - validators that don't have enough voting power to be in the top 31. Means, can't generate new blocks, get rewards, etc.
* `Unbonding (1)` - once validator leaves top 31 it becomes unbonding, means, validator and all delegators have to wait during unbonding time to get XFI back, or just redelegate them now. You can read about unbonding period in [delegation manual](/staking/delegate_dfi.md#how-to-unbond-(undelegate)).

Once you create a validator, and if you don't have enough power to get to top 101, your validator will get status **Unbonded**, then when it reaches enough voting it will automatically move to **Bonded** status and start generating blocks.

### Unbonding

When your validator got **Unbonding** status, that could happen for several reasons:

* Validator goes out from top 31.
* Validator unbound self delegated XFI more than promised (see `--min-self-delegation` parameter). In such a case the validator will be also `jailed`.
* Validator missed too many blocks to sign/propose. The default amount of missed blocks to become unbonding are 50% of blocks during the 31 blocks window. Will be `jailed` also.
* Validator double sign blocks. In this case the validator will be tombstoned and `jailed` forever.

In all cases you still can [redelegate](/staking/delegate_dfi.md#redelegate) your XFI to another validator not to wait **Unbonding** period.

More about jailing and slashing read in [Slashing](/staking/slashing.md) section.

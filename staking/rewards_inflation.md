# Rewards & Inflation

For staking users in the dfinance network getting rewards in sXFI coins. Validators receive rewards by generating and signing new blocks, they take a commission, it's their default reward for supporting the network. 

Delegators that delegate sXFI or LPT to validators also receive rewards in proportion to the amount of their staked sXFI and LPT. 

Read more about the inflation and rewards model supported by the dfinance network in the current documentation.

## Inflation

Dfinance inflation model is in production version and implemented in current testnet. We are releasing information about it step by step, as dfinance inflation model very innovative and using a lot of parameters.

You can read about introduction in our latest [article](https://medium.com/dfinance/token-economics-954874a35252).

To see inflation parameters check `mint` module:

```bash
# To see query commands.
dncli q mint --help

# To see tx commands.
dncli tx mint --help
```

More information will be provided soon with coming documentation updates.

## Rewards

Use [wallet](https://wallet.testnet.dfinance.co) to see & withdraw rewards or continue with **dncli**.

See `distribution` module:

```bash
# To see query commands.
dncli q distribution

# To see tx commands.
dncli tx distribution
```

To check if you have any rewards use next command:

```bash
dncli q distribution rewards [delegator-addr] [<validator-addr>]
```

Where:

    * [delegator-addr] - address of delegator. 
    * [<validator-addr>] - address of validator. Optional.

If you are validator, see earned commission:

```bash
dncli q distribution commission [validator]
```

**Important:** in current testnet rewards withdrawing disabled and will become available with mainnet.

To withdraw rewards send transaction:

```bash
dncli tx distribution withdraw-all-rewards --from [account]
```

Where:

    * [account] - is an account that has rewards.

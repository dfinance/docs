
# Modules

There are two types of smart contracts in **dfinance**: module and script.

Difference between them that module could be deployed into blockchain storage and be stored under the deployer account, while scripts exist only within one transaction and can't be deployed.

## Write a module

Let's see an example of a small module that will just add two numbers (a and b):

**Mvir**
```rust
module Math {
    public add(a: u64, b: u64): u64 {
        return move(a) + move(b);
    }
}
```

Let's compile this module using VSCode plugin or **dncli**. As during compilation, the compiler requires an address of module owner, to keep the owner in compiled code and verify during deploy (that transaction sender and address using during compilation match), we will need to provide an address.

Using **dncli**, we can do it so:

```shell
dncli query vm compile-module <path-to-mvir> <address> --to-file <output file>
```

After you replace variables with your own, you will get a compiled module to the output file.

After compilation, you can try to deploy the module:

```shell
dncli tx vm deploy-module <output file> --from <account> --fees 1dfi
```

Check transaction execution by its id returned in the output.

```shell
dncli tx query <id>
```

If you see a **contract_status** event, that contains status **keep**, everything deployed fine. 

After this, you can import your module in other modules and scripts, like:

```rust
import <address>.Math;
```

Just replace **&lt;address&gt;** with your correct address and you can do an import.

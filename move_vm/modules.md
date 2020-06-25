# Modules

There are two types of smart contracts in **dfinance**: module and script.

Difference between them that module is published into blockchain storage and is stored under the publisher account, while script is simply a transaction-as-script and can only operate with existing modules.

The Move Book also has a section about [modules](https://move-book.com/chapters/module.html) in Move language.

## Write a module

Let's see an example of a small module that will just add two numbers \(a and b\):

```rust
module Math {
    public fun add(a: u64, b: u64): u64 {
        a + b
    }
}
```

Let's compile this module using **dncli**. Compiler requires sender's address as it's included into bytecode. This address will then be verified on module publish.

```text
dncli query vm compile <path-to-mvir> <address> --to-file <output file>
```

Replace variables in this pattern with your own and you will get a compiled module in the specified output file. When it's done, you can publish your module:

```text
dncli tx vm publish <output file> --from <account>
```

Check your transaction by querying its id, which was returned in the output.

```text
dncli tx query <id>
```

If you see a **contract\_status** event, with status **keep** inside, everything published fine!

When it's done your module is be published under your address, and you and other users can access it in their modules or scripts:

```rust
use {{address}}::Math;
```

Just replace `{{address}}` with yours and you can use this module.


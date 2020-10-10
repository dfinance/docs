# Script Arguments

Each script can contain only one function, usually, it's **"main"**, but you can define it however you want, This function can have arguments and will be executed when you send a transaction with your script.

With **execute** command you can pass arguments to script function, see help:

```text
dncli tx vm execute --help
```

**Dncli** **"execute"** supports different kind of arguments, as:

* Boolean values. Example: **true, false**.
* U64, U8, U128 values \(unsigned integers\).
* vector\<u8\> values \(hex\). Can be used for string values. Example: **0x68656c6c6f2c20776f726c6421**.
* Address values. Example: **wallet1jk4ld0uu6wdrj9t8u3gghm9jt583hxx7xp7he8**.

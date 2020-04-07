# Script Arguments

Each script contains **"main"** function, and there could be arguments.

So, with **execute-script** command you can pass arguments, see:

```shell
dncli tx vm execute-script --help
```

**Dncli** **"execute-script"** supports different kind of arguments, as:

- Boolean values. Example: **true, false**.
- U64, U8, U128 values (unsigned integers).
- ByteArray values (hex). Example: **"68656c6c6f2c20776f726c6421"**.
- Strings (pseudo support via ByteArray type). Example: **"hello, world!"**.
- Address values. Example: **wallet1jk4ld0uu6wdrj9t8u3gghm9jt583hxx7xp7he8**.
- Hashed values. Currently used mostly to get ID of ticker for oracles. Example: **#"DFI_ETH"**. The value will be converted to **u64** using [xxHash](https://github.com/Cyan4973/xxHash), e.g. **xxHash(lower_case(string))**.

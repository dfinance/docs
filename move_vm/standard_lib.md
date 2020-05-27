# Standard Library

Standard **Move VM** library is default modules that already developed and developers can use in developing new modules, scripts.

They all placed on the address **0x0**. So when you import something from **0x0**, you import standard modules, like:

```rust
use 0x0::Account;
use 0x0::Events;
use 0x0::DFI;
use 0x0::Coins;
```

You can look for actual standard modules in [dvm](https://github.com/dfinance/dvm/tree/master/lang) repository.


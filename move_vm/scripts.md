# Scripts

As already mentioned, **dfinance** supports transaction scripting. It means users can compile and execute scripts. Different between modules here is that you can't deploy script and use it again in the future, each script executing by new transaction every time.

## Write a script

Let's write a basic script, accepts two arguments, a and b values, and then using module math make a sum from these two numbers and then fire events.

**Mvir**

```rust
import 0x0.Account;
import <address>.Math;

main(a: u64, b: u64) {
    let sum: u64;
    let handle: Account.EventHandle<u64>;

    sum = Math.add(move(a), move(b));

    handle = Account.new_event_handle<u64>();
    Account.emit_event<u64>(&mut handle, move(sum));
    Account.destroy_handle<u64>(move(handle));
    return;
}
```

Replace **&lt;address&gt;** with the address you used during deploy of the module in previous part of current documentation.

The script accepts two arguments in function **"main"**, then calculate sum with provided arguments, and fire event with this sum. Both arguments are **u64** integers.

Compile using [VSCode](https://marketplace.visualstudio.com/items?itemName=damirka.move-ide) plugin or **dncli**.

With **dncli**:

```text
dncli query vm compile-script <script file> <address> --to-file <output file>
```

And then execute with arguments:

```text
dncli tx vm execute-script <output file> 15 20 --from <my address> --fees 1dfi
```

You can verify execution with querying transaction by id.

There will be even fired event, that will contain **"keep"** status and the resulting sum, like:

```json
[
   {
      "type":"contract_events",
      "attributes":[
         {
            "key":"guid",
            "value":"0x030000000000000077616c6c657400000000000095abf6bf9cd39a391567e4508becb25d0f1b98de"
         },
         {
            "key":"sequence_number",
            "value":"0"
         },
         {
            "key":"type",
            "value":"U64"
         },
         {
            "key":"data",
            "value":"0x2300000000000000"
         }
      ]
   },
   {
      "type":"contract_status",
      "attributes":[
         {
            "key":"status",
            "value":"keep"
         }
      ]
   }
]
```


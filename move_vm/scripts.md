# Scripts

As already mentioned, **dfinance** supports transaction scripting. It means users can compile and execute scripts. Different between modules here is that you can't publish script and use it again in the future, each script executing by new transaction every time.

The Move Book also has a section about [scripts](https://move-book.com/syntax-basics/function.html) in Move language.

## Write a script

Let's write a basic script, accepts two arguments, a and b values, and then using module math make a sum from these two numbers and then fire events.

```rust
script {
   use 0x1::Event;
   use {{sender}}::Math;

   fun main(a: u64, b: u64) {
      let sum = Math::add(a, b);
      Event::emit(sum);
   }
}
```

Replace `{{sender}}` with the address you used during publish of the module in the previous part of current documentation.

The script accepts two arguments in function **"main"**, then calculate sum with provided arguments, and fire event with this sum. Both arguments are **u64** integers.

Compile the script using **dncli**:

```text
dncli query vm compile <script file> <address> --to-file <output file>
```

And then execute with arguments:

```text
dncli tx vm execute <output file> 15 20 --from <my address>
```

You can verify execution with querying transaction by id.

There will be even fired event, that will contain **"keep"** status and the resulting sum, like:

```json
[
   {
      "type":"contract_events",
      "attributes":[
         {
            "key":"sender_address",
            "value":"wallet1qjgqxwk55p9ejlupmeza0r02hyextys9rrthgg"
         },
         {
            "key":"source",
            "value":"script"
         },
         {
            "key":"type",
            "value":"u64"
         },
         {
            "key":"data",
            "value":"2300000000000000"
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
   },
   {
      "type":"message",
      "attributes":[
         {
            "key":"action",
            "value":"execute_script"
         },
         {
            "key":"sender",
            "value":"wallet1qjgqxwk55p9ejlupmeza0r02hyextys9rrthgg"
         }
      ]
   },
   {
      "type":"transfer",
      "attributes":[
         {
            "key":"recipient",
            "value":"wallet17xpfvakm2amg962yls6f84z3kell8c5la07d0l"
         },
         {
            "key":"amount",
            "value":"1dfi"
         }
      ]
   }
]
```

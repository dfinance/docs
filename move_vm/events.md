# Events

Each transaction processed by **dfinance** blockchain can have events.

You can see them by querying transaction by id, they're stored under 'events' key:

```javascript
"events": [
    ...
]
```

You can also see events in transaction logs in [block explorer](https://explorer.testnet.dfinance.co/txs?page=1).

## VM related events

For smart contracts related transactions, there are reserved types for events, such as:

* **contract\_status** - contains contract execution status. Usually contains **"status"** attribute, which represents one of the possible statuses:
  * **"keep"** - when transaction executed via VM \(means passed pre-verification, byte code, arguments, etc\), but still can contain error and exists together with status **"error"**.
  * **"discard"** - when transaction not passed via VM, because of fail on pre-validation.
  * **"error"** - when transaction contains an error, happens together with status **"keep"**, contains attributes:
    * **major\_status** - the major status of error, integer.
    * **sub\_status** - the sub status of error, integer, optional.
    * **message** - text message, optional.
* **contract\_events** -  contains events generated during smart contract execution. In our example of the script, we generated such an event using `Event::emit`. Contains next attributes \(attributes always sorted in the same sequence\):
  * **sender_address** - address of account which sent transaction that sent event.
  * **source** - the place where the event was sent, it could be `script` (in case it sent from user script), or path to module which sent event, e.g.: `0x1::Account`.
  * **type** - contains data type, similar to contract arguments, but also could be a struct \(in such case there will be reference to which indeed struct used in the event\). A struct could be decoded using [lcs](https://github.com/the729/lcs).
  * **data** - [lcs](https://github.com/the729/lcs) encoded data in hex. It could be decoded using [lcs](https://github.com/the729/lcs).

The **data** field always using LCS encoding \(Libra Canonical Serialization\). There is a community [description](https://github.com/librastartup/libra-canonical-serialization/blob/master/DOCUMENTATION.md) of how it works. Also, Golang [library](https://github.com/the729/lcs), where you can see examples and use for your own projects. So decoding of the **"data"** field should happen with LCS.

There are two reserved events: sent and received events that fire when withdrawing or depositing of resources happens. The implementation you can found in the standard library in [Account](https://github.com/dfinance/dvm/master/lang/stdlib/account.move) module.

Events example:

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
            "value":"0x1::Account"
         },
         {
            "key":"type",
            "value":"0x1::Account::SentPaymentEvent"
         },
         {
            "key":"data",
            "value":"4d01000000000000000000000000000003646669db4b0ed53d2fd0a74ce8f0d106e7ab144eb0fbab00"
         },
         {
            "key":"sender_address",
            "value":"wallet1qjgqxwk55p9ejlupmeza0r02hyextys9rrthgg"
         },
         {
            "key":"source",
            "value":"0x1::Account"
         },
         {
            "key":"type",
            "value":"0x1::Account::ReceivedPaymentEvent"
         },
         {
            "key":"data",
            "value":"4d010000000000000000000000000000036466690490033ad4a04b997f81de45d78deab93265920500"
         },
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
            "value":"0a00000000000000"
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

Events attributes always sorted in the same sequence, so you can go over `contract_events` attributes to parse event objects.

To catch events you can use REST API, for example, all events from Account module, look at this URL to see how filters work:

```text
https://rest.testnet.dfinance.co/txs?contract_events.source=0x1::Account
```

Also, look at our [swagger](https://swagger.testnet.dfinance.co/?urls.primaryName=Cosmos%20SDK%20API) for details.

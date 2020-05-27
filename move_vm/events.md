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
* **contract\_events** -  contains events generated during smart contract execution. In our example of the script, we generated such an event using EventHandler. Contains next attributes:
  * **guid** - the unique id of the event.
  * **sequence\_number** - sequence number of this event within current EvenHandler.
  * **type** - contains data type, similar to contract arguments, but also could be a struct. A struct could be decoded using [lcs](https://github.com/the729/lcs).
  * **data** - [lcs](https://github.com/the729/lcs) encoded data in hex \(with prefix - "0x"\). It could be decoded using [lcs](https://github.com/the729/lcs).

The **data** field always using LCS encoding \(Libra Canonical Serialization\). There is a community [description](https://github.com/librastartup/libra-canonical-serialization/blob/master/DOCUMENTATION.md) of how it works. Also, Golang [library](https://github.com/the729/lcs), where you can see examples and use for your own projects. So decoding of the **"data"** field should happen with LCS.

There are two reserved events: sent and received events that fire when withdrawing or depositing of resources happens. The implementation you can found in the standard library in [Account](https://github.com/dfinance/dvm/blob/bf457b3145c5e448ece3258bbf67c22326559a12/lang/stdlib/account.move#L14) module.

Guid is a unique id, contains a sequence number of EventHandler for the current account and address of this account. You can look at the [Event](https://github.com/dfinance/dvm/blob/4a35f84f04f7c313f65e3dc6463c28e06b4537ea/lang/stdlib/account.mvir#L129) module that generates guids for events. Also, it handles the creation of new `EventHandle` resources, that can be used by your module or script to create new kinds of events. Look at [standard library](standard_lib.md#events) documentation.

To catch events you can use REST API, using guid, for example, look at this URL to see how filters works:

```text
https://rest.testnet.dfinance.co/txs?contract_events.guid=0x060000000000000077616c6c657400000000000095abf6bf9cd39a391567e4508becb25d0f1b98de
```

Also, look at our [swagger](https://swagger.testnet.dfinance.co/?urls.primaryName=Cosmos%20SDK%20API) for details.


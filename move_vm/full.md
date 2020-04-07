### Script Arguments

Each script contains "main" function, and there could be arguments.

So, with **execute-script** command you can pass arguments, see:

    dncli tx vm execute-script --help

Dncli "execute-script" supports different kind of arguments, as:

- Boolean values. Example: true, false.
- U64, U8, U128 values (unsigned integers).
- ByteArray values (hex). Example: "68656c6c6f2c20776f726c6421".
- Strings (pseudo support via ByteArray type). Example: "hello, world!".
- Address values. Example: wallet1jk4ld0uu6wdrj9t8u3gghm9jt583hxx7xp7he8.
- Hashed values. Currently used mostly to get ID of ticker for oracles. Example: #"DFI_ETH". The value will be converted to uint64 using [xxHash](https://github.com/Cyan4973/xxHash), e.g. xxHash(lower_case(string)).

### Standard Library

Standard Move VM library is default modules that already developed and developers can use in developing new modules, scripts.

They all placed on the address 0x0. So when you import something from 0x0, like:

    import 0x0.Account;
    import 0x0.Coins;

You import standard module Account and Coins.

You can look for actual standard modules in [dmv](https://github.com/dfinance/dvm/tree/master/lang) repository.

### Execution

**Events**

Each transaction processed by dfinance blockchain could have events.

You can easily check it by querying transaction by id and find events array:

    "events": [
    	...
    ]

For example, you can look at [this transaction](https://explorer.testnet.dfinance.co/txs/4CABD878F502DF756988B8B08309C67C42CAA1CC4ECA3CBEA1BD6171DE0316EF) in explorer.

For smart contracts related transactions, there are reserved types for events, as:

- **contract_status** - contains contract execution status. Usually contains attribute **"status"**, that represents one of the possible statuses:
    - **keep** - when transaction executed via VM (means passed pre-verification, byte code, arguments, etc), but still can contain error and exists together with status **"error"**.
    - **discard** - when transaction not passed via VM, because of fail on pre-validation.
    - **error** - when transaction contains an error, happens together with status "**keep"**, contains attributes:
        - **major_status** - the major status of error, integer.
        - **sub_status** - the sub status of error, integer, optional.
        - **message** - text message, optional.
- **contract_events** -  contains events generated during smart contract execution. In our example of the script, we generated such an event using EventHandler. Contains next attributes:
    - **guid** - the unique id of the event.
    - **sequence_number** - sequence number of this event within current EvenHandler.
    - **type** - contains data type, similar to contract arguments, but also could be a struct. A struct could be decoded using [lcs](https://github.com/the729/lcs).
    - **data** - [lcs](https://github.com/the729/lcs) encoded data in hex (with prefix - "0x"). It could be decoded using [lcs](https://github.com/the729/lcs).

The **"data"** field always using LCS encoding (Libra Canonical Serialization). There is a community [description](https://github.com/librastartup/libra-canonical-serialization/blob/master/DOCUMENTATION.md) of how it works. Also, Golang [library](https://github.com/the729/lcs), where you can see examples and use for your own projects. So decoding of the **"data"** field should happen with LCS.

The temporary solution to catch events is using events guids. There are two reserved events: withdraw and deposit from an account. The implementation you can found in standard lib in [account.mvir](https://github.com/dfinance/dvm/blob/4a35f84f04f7c313f65e3dc6463c28e06b4537ea/lang/stdlib/account.mvir#L8).

Guid is a unique id, contains a sequence number of EventHandler for the current account and address of this account. You can look at the [function](https://github.com/dfinance/dvm/blob/4a35f84f04f7c313f65e3dc6463c28e06b4537ea/lang/stdlib/account.mvir#L129) that generates guids for events.

So, you can write your own version of the function to generate guid of reserved events, and then use it in your project. We prepared an [example](https://github.com/borispovod/guid) Golang repository for you.

To catch events you can use REST API, using guid, for example, look at this [URL](https://rest.testnet.dfinance.co/txs?contract_events.guid=0x060000000000000077616c6c657400000000000095abf6bf9cd39a391567e4508becb25d0f1b98de) to see how filters work, also look at our [swagger](https://swagger.testnet.dfinance.co/?urls.primaryName=Cosmos%20SDK%20API) for details.

### Resources

Resources are the main new future brings by Move VM. Resources are a special type of data in Move VM, that controls belongs to accounts and to state storage.

It means, when you create a new resource in your smart contract, you can bring your resource to your account, or another user account in case your module allows it, also, you can check if resource exists on account, and also destroys it. Everything in within your module rules.

Let's try to create a new module contains resource, let's say it will be just hold your coins and withdraw them from account only in case your ask you provide right secret value.

Secret value in your case can be anything encoded with sha3_256 script. Let's call this module MyColdStorage. To make it easy, we will hold only one coin resources, deposited once, not multiplay: 

**Important: Mvir is very low level language, so could contains very specific constructions, you can use Move language instead.**

**Mvir**

    module MyColdStorage {
        import 0x0.Hash;
        import 0x0.Coins;
    
        resource T {
            secretKey: bytearray, 
            deposit: Coins.Coin,
        }
    
        public deposit(secretKey: bytearray, deposit: Coins.Coin) acquires T {
            let d_ref: &mut Self.T;
    
            assert(copy(secretKey) != h"", 101);
    
            if (exists<T>(get_txn_sender())) {
                d_ref = borrow_global_mut<T>(get_txn_sender());
                assert(*(&copy(d_ref).secretKey) == h"", 102);
    
                *(&mut copy(d_ref).secretKey) = move(secretKey);
                *(&mut move(d_ref).deposit) = move(deposit);
            } else {
                move_to_sender<T>(T {
                    secretKey: move(secretKey),
                    deposit: move(deposit),
                });
            }
    
            return;
        }
    
        public withdraw(rawKey: bytearray): Coins.Coin acquires T {
            let hash: bytearray;
            let d_ref: &mut Self.T;
            let to_withdraw: Coins.Coin;
    
            assert(exists<T>(get_txn_sender()), 103);
    
            d_ref = borrow_global_mut<T>(get_txn_sender());
    
            hash = Hash.sha3_256(move(rawKey));
            assert(move(hash) == *(&copy(d_ref).secretKey), 104);
    
            to_withdraw = *(&copy(d_ref).deposit);
            *(&mut copy(d_ref).secretKey) = h"";
            *(&mut move(d_ref).deposit) = Coins.zero_coin();
    
            return move(to_withdraw);
        }
    }

Provided code create new module "MyColdStorage" and resource named "T" (default name for default resource), that contains secret key and also your coins deposit.

Don't look for now at scary pointers and references, we just interesting in few methods, indeed: **borrow_global_mut**, **move_to_sender**, **exists**, and also **acquires** notation.

**move_to_sender**

So created resource should be stored under account, indeed here we call **move_to_sender** to move resource under sender of account. **move_to_sender** restrict cases when someone else can move resource to your account, and in such case secure it.

Let's see how it's done in **Mvir**:

    move_to_sender<T>(T {
        secretKey: move(secretKey),
        deposit: move(deposit),
    });

In **"deposit"** function we move created resource to sender. After this, we can start work with our resource.

**exists**

Also, in **"deposit"** function we verifies that resource already exists, in such case, we verify with assert, that current secret key is empty (means deposit already withdrawn), and then fill borrowed resource with new deposit and secret key.

**borrow_global_mut**

Allows to get a mutable reference to a resource, that could be changed then. There is also just **borrow_global**, that allows to borrow just reference, not mutable. 

**acquires**

Acquires notation after function definition means that current function can change exists resource, this why both **"deposit"** and **"withdraw"** functions has this notation.

So deposit function creates a new resource, while withdraw check if resource existing, borrow a resource, withdraw deposit, if hashes matches, and then put empty **bytearray** to **secretKey** (so resource can be reused).

You can try to compile and deploy module, and then via script call deposit with your hash of your secret value, and then withdraw by passing your secret value.  

Here is [repository](https://github.com/borispovod/cold-storage-example) to help you, contains module and scripts examples.

### More Docs

- [Libra Documentation](https://developers.libra.org/)
- [Move Technical Paper](https://developers.libra.org/docs/assets/papers/libra-move-a-language-with-programmable-resources/2019-09-26.pdf)
- [Move Whitepaper Deep Dive](https://medium.com/coinmonks/whitepaper-deep-dive-move-facebook-libra-blockchains-new-programming-language-7dbd5b242c2b)
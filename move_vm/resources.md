# Resources

Resources are the main new future brings by **Move VM**. Resources are a special type of data in Move VM, that controls belongs to accounts and to state storage.

It means, when you create a new resource in your smart contract, you can bring your resource to your account, or another user account in case your module allows it, also, you can check if resource exists on account, and also destroys it. Everything in within your module rules.

## Develop a resource

Let's try to create a new module contains resource, let's say it will be just hold your coins and withdraw them from account only in case your ask you provide right secret value.

Secret value in your case can be anything encoded with sha3_256 script. Let's call this module **MyColdStorage**. To make it easy, we will hold only one coin resources, deposited once, not multiplay: 

**Important: Mvir is very low level language, so could contains very specific constructions, you can use Move language instead.**

**Mvir**
```rust
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
```

Provided code create new module **"MyColdStorage"** and resource named **"T"** (default name for default resource in modules), that contains secret key and also your coins deposit.

Don't look for now at scary pointers and references, we just interesting in few methods, indeed: **borrow_global_mut**, **move_to_sender**, **exists**, and also **acquires** notation.

### move_to_sender

So created resource should be stored under account, indeed here we call **move_to_sender** to move resource under sender of account. **move_to_sender** restrict cases when someone else can move resource to your account, and in such case secure it.

Let's see how it's done in **Mvir**:

```rust
move_to_sender<T>(T {
    secretKey: move(secretKey),
    deposit: move(deposit),
});
```

In **"deposit"** function we move created resource to sender. After this, we can start work with our resource.

### exists

```rust
if (exists<T>(get_txn_sender())) {
    ...
}
```

Also, in **"deposit"** function we verifies that resource already exists, in such case, we verify with assert, that current secret key is empty (means deposit already withdrawn), and then fill borrowed resource with new deposit and secret key.

### borrow_global_mut

```rust
d_ref = borrow_global_mut<T>(get_txn_sender());
```

Allows to get a mutable reference to a resource, that could be changed then. There is also just **borrow_global**, that allows to borrow just reference, not mutable. 

### acquires

```rust
public deposit(secretKey: bytearray, deposit: Coins.Coin) acquires T {
    ...
}
```

Acquires notation after function definition means that current function can change exists resource, this why both **"deposit"** and **"withdraw"** functions has this notation.

So deposit function creates a new resource, while withdraw check if resource existing, borrow a resource, withdraw deposit, if hashes matches, and then put empty **bytearray** to **secretKey** (so resource can be reused).

### Deploy

You can try to compile and deploy module, and then via script call deposit with your hash of your secret value, and then withdraw by passing your secret value.  

Here is [repository](https://github.com/borispovod/cold-storage-example) to help you, contains module and scripts examples.
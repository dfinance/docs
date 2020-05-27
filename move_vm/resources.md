# Resources

Resource is the main feature of **Move VM**. Resource is a special type in Move VM, which has strict rules of usage - therefore more safety, and is created to work with digital assets.

Resource type can only be defined and managed in a single module. This module sets rules for accessing, destroying, transfering and checking existence of resources defined in it.

## Develop a resource

Let's create a swap module, that will allow us to swap coins between users.

We will make it easy, it will support only one swap per coin pair, which means, you can't create multiple swaps using the same pair in the same account. Just two functions - to publish your offer and to allow other users to swap it for specified price.

```rust
module Swap {
    use 0x0::Dfinance;
    use 0x0::Transaction;
    use 0x0::Account;

    // The resource of module which contains swap parameters.
    resource struct T<Offered, Expected>{
        offered: Dfinance::T<Offered>,
        price: u128,
    }

    // Create a swap deal with two coin pairs: Offered and Expected.
    public fun create<Offered, Expected>(offered: Dfinance::T<Offered>, price: u128) {
        let sender = Transaction::sender();
        Transaction::assert(!exists<Offered, Expected>(sender), 101);

        move_to_sender<T<Offered, Expected>>(
            T<Offered, Expected> {
                offered: offered,
                price
            }
        );
    }

    // Get the price of the swap deal.
    public fun get_price<Offered, Expected>(seller: address): u128 acquires T {
        let offer = borrow_global<T<Offered, Expected>>(seller);
        offer.price
    }

    // Change price before swap happens.
    public fun change_price<Offered, Expected>(new_price: u128) acquires T {
        let offer = borrow_global_mut<T<Offered, Expected>>(Transaction::sender());
        offer.price = new_price;
    }

    // Swap coins and deposit them to accounts: both creator and buyer.
    public fun swap<Offered, Expected>(seller: address, exp: Dfinance::T<Expected>) acquires T {
       let T<Offered, Expected> { offered, price } = move_from<T<Offered, Expected>>(seller);
       let exp_value = Dfinance::value<Expected>(&exp);

       Transaction::assert(exp_value == price, 102);
       Account::deposit(seller, exp);
       Account::deposit_to_sender(offered);
    }

    // Check if the swap pair already exists for the account.
    public fun exists<Offered, Expected>(addr: address): bool {
        ::exists<T<Offered, Expected>>(addr)
    }
}
```

Provided code creates new module **"Swap"** and resource named **"T"** \(default name for default resource in modules\), which holds information about the deal.

To create a swap use **"create"** function, to make an exchange use **"swap"** function. Other methods in this module provide ability to get/set price, check if swap option already exists at specific address. All methods use generics Offered and Expected, which allow them to make unique resources for each swap.

Even though there's a lot of code inside, we'll focus on 4 main methods: `borrow_global_mut`, `move_to_sender`, `move_from`, `exists` and on `acquires` keyword.

### move\_to\_sender&lt;T&gt;\(T\)

When resource is created, it needs to be moved to address \(otherwise it will never be actually created - there's no 'contract storage' - only accounts\). It is important to note that newly created resource can be moved only to the sender of transaction - this makes initalization of resource impossible at someone else's address.

To move resource to sender `move_to_sender<T>(T)` method is used - as obvious as it is - where T is a generic type and instance of this type - a resource:

```rust
// Create a swap deal with two coin pairs: Offered and Expected.
public fun create<Offered, Expected>(offered: Dfinance::T<Offered>, price: u128) {
    let sender = Transaction::sender();
    Transaction::assert(!exists<Offered, Expected>(sender), 101);

    move_to_sender<T<Offered, Expected>>(
        T<Offered, Expected> {
            offered: offered,
            price
        }
    );
}
```

In `create` function we created new resource which contains information about the swap, and moved it to sender. After this, we can start working with our resources.

### exists&lt;T&gt;\(address\)

Allow us to check if the resource already exists on the specific address or not:

```rust
// Check if swap pair already exists for account.
public fun exists<Offered, Expected>(addr: address): bool {
    ::exists<T<Offered, Expected>>(addr)
}
```

### borrow\_global\_mut&lt;T&gt;\(address\)

```rust
// Change price before swap happens.
public fun change_price<Offered, Expected>(new_price: u128) acquires T {
    let offer = borrow_global_mut<T<Offered, Expected>>(Transaction::sender());
    offer.price = new_price;
}
```

Allows getting a mutable reference to a resource, that could be changed then. There is also just `borrow_global` to get immutable reference:

```rust
// Get the price of the swap deal.
public fun get_price<Offered, Expected>(seller: address): u128 acquires T {
    let offer = borrow_global<T<Offered, Expected>>(seller);
    offer.price
}
```

`borrow_global` gives immutable reference to a resource stored under address. You can use this reference to read resource but can't change it.

### acquires

Every function which accesses already created resource must have `acquires` keyword in it signature after which acquired resources are listed. Look at the usage of `borrow_global` and `borrow_global_mut` again. Resource `T` is acquired by both methods `get_price` and `change_price`.

### move\_from&lt;T&gt;\(address\)

```rust
public fun swap<Offered, Expected>(seller: address, exp: Dfinance::T<Expected>) acquires T {
    let T<Offered, Expected> { offered, price } = move_from<T<Offered, Expected>>(seller);
    let exp_value = Dfinance::value<Expected>(&exp);

    Transaction::assert(exp_value == price, 102);
    Account::deposit(seller, exp);
    Account::deposit_to_sender(offered);
}
```

`move_from<T>(address)` function moves the resource from address. After taking resource off account, it must be used - either destructured \(like in example below\) or passed to another function. Resources are not automatically destroyed like regular variables and their lifetime must be specified.

### Summary

So `create` function creates a new resource, `swap` function allows to swap \(deposit coins to both accounts and _destroy_ resource T\); we've also added methods to get price of the deal and to change it.

### Deploy

You can try to compile and deploy module, and then via script call deposit with hash of your secret value, and then withdraw by passing your secret value.

[Here is repository](https://github.com/borispovod/cold-storage-example) to help you. It already contains module and scripts examples.

### Scripts

Here are a few scripts examples, of how you can work with Swap module \(don't forget to replace  with your address\):

**Create**

```rust
script {
    use {{sender}}::Swap;
    use 0x0::DFI;
    use 0x0::Coins;
    use 0x0::Account;

    fun main(amount: u128, price: u128) {
        let dfi = Account::withdraw_from_sender(amount);

        // Deposit DFI coins in exchange to UDST.
        Swap::create<DFI::T, Coins::USDT>(dfi, price);
    }
}
```

**Swap**

```rust
script {
    use {{sender}}::Swap;
    use 0x0::DFI;
    use 0x0::Coins;
    use 0x0::Account;

    fun main(seller:address, price: u128) {
        let usdt = Account::withdraw_from_sender(price);

        // Deposit USDT to swap coins.
        Swap::swap<DFI::T, Coins::USDT>(seller, usdt);
    }
}
```

### More about resources

Resources are the most interesting and the most complex topic in Move language. But once you've gotten the idea, the rest is easy.

To know Move better and to learn about resources specifically - see [Move Book](https://move-book.com/chapters/resource.html). It has a lot to add to the topic and is aimed to make learning Move as easy as possible.


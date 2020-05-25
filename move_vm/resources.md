# Resources

Resources are the main new future brings by **Move VM**. Resources are a special type of data in Move VM, that controls belongs to accounts and to state storage.

It means, when you create a new resource in your smart contract, you can bring your resource to your account, or another user account in case your module allows it, also, you can check if resource exists on account, and also destroys it. Everything in within your module rules.

## Develop a resource

Let's create a swap module, that will allow us to swap coins between users.

We will make it easy, it will support only one swap per coin pair, which means, you can't create multiplay swaps using the same pair in the same account. Just two functions - to publish your offer and to allow other users to swap it.

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

Provided code create new module **"Swap"** and resource named **"T"** \(default name for default resource in modules\), that information about the deal.

To create a swap use **"create"** function, to make an exchange use **"swap"** function. Other methods allow us to get/set price, check if swap already exists for a specific address. All methods use generics Offered and Expected, which allows them to make unique resources for each swap.

We mostly interesting in resource related methods: **borrow\_global\_mut**, **move\_to\_sender**, **move\_from**, **exists**, and also **acquires** notation.

### move\_to\_sender

So created resource T<Offered, Expected> should be stored under sender account because of **move\_to\_sender** call. **move\_to\_sender** restrict cases when someone else can move resources to your account, and in such case secure it.

Let's see how it's done:

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

In **"create"** function we move created resource contains information about the swap to sender. After this, we can start work with our resources.

### borrow\_global\_mut

```rust
// Change price before swap happens.
public fun change_price<Offered, Expected>(new_price: u128) acquires T {
    let offer = borrow_global_mut<T<Offered, Expected>>(Transaction::sender());
    offer.price = new_price;
}
```

Allows getting a mutable reference to a resource, that could be changed then. There is also just **borrow\_global**, that allows borrowing just reference, not mutable:

```rust
// Get the price of the swap deal.
public fun get_price<Offered, Expected>(seller: address): u128 acquires T {
    let offer = borrow_global<T<Offered, Expected>>(seller);
    offer.price
}
```

As you see, **borrow\_global** doesn't allow to change the resource, but allows us to read it.
### exists

Allow us to check if the resource already exists on the specific address or not:

```rust
// Check if swap pair already exists for account.
public fun exists<Offered, Expected>(addr: address): bool {
    ::exists<T<Offered, Expected>>(addr)
}
```

### move_from & acquires

```rust
public fun swap<Offered, Expected>(seller: address, exp: Dfinance::T<Expected>) acquires T {
    let T<Offered, Expected> { offered, price } = move_from<T<Offered, Expected>>(seller);
    let exp_value = Dfinance::value<Expected>(&exp);

    Transaction::assert(exp_value == price, 102);
    Account::deposit(seller, exp);
    Account::deposit_to_sender(offered);
}
```

**"move_from"** moves the resource from the user account.

Acquires notation after function definition means that current function can change exists resource or read it, this why **"swap"** and **"get_price"**, **"set_price"** functions have this notation.

So **"create"** function creates a new resource, swap function allows to swap (deposit coins to both accounts and destroy T resource), get the price, and set price to change information about the deal.

### Deploy

You can try to compile and deploy module, and then via script call deposit with your hash of your secret value, and then withdraw by passing your secret value.

Here is [repository](https://github.com/borispovod/cold-storage-example) to help you, contains module and scripts examples.

### Scripts

Here are a few scripts examples, how possible to work with Swap module (don't forget to replace {{sender}} with your address):

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

Resources are the most interesting and in the same time complex thing in Move language. We are trying to explain it better, this why 
we created a section in [Move Book](https://move-book.com/chapters/resource.html) dedicated to resources. It has a great explanations, other useful examples and recommendations.

Must to read! You can continue learning about resources there.

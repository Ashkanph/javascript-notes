
* Rust doesn’t have the null feature that many other languages have.

```rust
    enum IpAddr {
        V4(u8, u8, u8, u8),
        V6(String),
    }

    let home = IpAddr::V4(127, 0, 0, 1);

    let loopback = IpAddr::V6(String::from("::1"));
```

### option
* The `Option<T>` enum is a regular enum, and `Some(T)` and `None` are still variants of type `Option<T>`

* If we use None rather than Some, we need to tell Rust what type of Option<T> we have, because the compiler can’t infer the type that the Some variant will hold by looking only at a None value.

```rust
    let some_number = Some(5);
    let some_string = Some("a string");

    let absent_number: Option<i32> = None;
```

### match

```rust
#[derive(Debug)] // so we can inspect the state in a minute
enum UsState {
    Alabama,
    Alaska,
    // --snip--
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        }
    }
}
‍‍‍‍```

* Matches in Rust are `exhaustive`: we must exhaust every last possibility in order for the code to be valid.

```rust
    fn plus_one(x: Option<i32>) -> Option<i32> {
        match x {
            None => None,  // اگر این نان را نزاریم ایراد میگیره چون تمام حالت‌های ممکن پوشش داده نشده‌اند
            Some(i) => Some(i + 1),
        }
    }

    let five = Some(5);
    let six = plus_one(five);
    let none = plus_one(None);
```

* The `_ pattern` will match any value. By putting it after our other arms, the `_` will match all the possible cases that aren’t specified before it. The `()` is just the unit value, so nothing will happen in the `_` case. 


```rust
    let some_u8_value = 0u8; 
    match some_u8_value {     // فقط ۱ و ۳ و ۵ و ۷ برام مهمه. بقیه یوهشت ها که میشه تا ۲۵۷ مهم نیستن. ۰ و ۲ و ۴ هم مهم نیست
        1 => println!("one"),
        3 => println!("three"),
        5 => println!("five"),
        7 => println!("seven"),
        _ => (),
    }
```

### if let

```rust
let some_u8_value = Some(0u8);
match some_u8_value {
    Some(3) => println!("three"),
    _ => println!("Not three"),
}

// Is equal to

if let Some(3) = some_u8_value {
    println!("three");
} else {
    println!("Not three");
}
```


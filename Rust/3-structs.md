
Ownership: 

* For changing a field in a struct, the entire instance must be mutable; Rust doesn’t allow us to mark only certain fields as mutable.


```rust
struct User {
    username: String, //we used the owned String type rather than the &str string slice type; Because we want instances of this struct to own all of its data and for that data to be valid for as long as the entire struct is valid
    email: String,
    sign_in_count: u64,
    active: bool,
}

fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
}

let mut user1 = build_user(String::from("someone@example.com"), String::from("someusername123"));

// Struct update syntax (..), specifies that the remaining fields not explicitly set should have the same value as the fields in the given instance.


let user2 = User {
	email: String::from("another@example.com"),
	username: String::from("anotherusername567"),
	..user1
};
```

* Touple structs

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);
```

* Unit-Like Structs Without Any Fields
	* You can also define structs that don’t have any fields! These are called unit-like structs because they behave similarly to (), the unit type. Unit-like structs can be useful in situations in which you need to implement a trait on some type but don’t have any data that you want to store in the type itself.

## struct example

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    // square is an `Associated Functions` and must be called with ::
    fn square(size: u32) -> Rectangle {
        Rectangle {
            width: size,
            height: size,
        }
    }

    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

// Each struct is allowed to have multiple impl blocks.
impl Rectangle {
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };
    let rect2 = Rectangle {
        width: 10,
        height: 40,
    };

    println!(
        "The area of the rectangle is {} square pixels.",
        rect1.area()
    );

    println!("rect1 is {:#?}", rect1);
    println!("Can rect1 hold rect3? {}", rect1.can_hold(&rect2));
    // square is an `Associated Functions` and must be called with ::
    let sq = Rectangle::square(3);
}

fn area(rectangle: &Rectangle) -> u32 {
    rectangle.width * rectangle.height
}
```
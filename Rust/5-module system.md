
* Packages: A Cargo feature that lets you build, test, and share crates
* Crates: A tree of modules that produces a library or executable
* Modules and use: Let you control the organization, scope, and privacy of paths
* Paths: A way of naming an item, such as a struct, function, or module

### Package

* A package must contain zero or one library crates, and no more. It can contain as many binary crates as you’d like, but it must contain at least one crate (either library or binary).
* A package contains a Cargo.toml file that describes how to build those crates.
* If a package contains src/main.rs and src/lib.rs, it has two crates: a library and a binary, both with the same name as the package. A package can have multiple binary crates by placing files in the src/bin directory: each file will be a separate binary crate.

### Modules

* Modules let us organize code within a crate into groups for readability and easy reuse. 
* Modules also define Rust’s privacy boundary (public and private).
* In Rust is that all items (functions, methods, structs, enums, modules, and constants) are private by default. You must use `pub` to make them public for external usage.

```rust
mod front_of_house {  // We define a module by starting with the mod keyword
    pub mod hosting {
        pub fn add_to_waitlist() {}
        fn seat_at_table() {}
    }

    mod serving {
        fn take_order() {}

        fn serve_order() {}

        fn take_payment() {}
    }
}

// mod front_of_house;   اگر به جای آکلاد باز و بسته در جلوی نام مودال یک ; بگذاریم یعنی برو محتوای آن ماژول را از فایلی با همین نام بگیر و بیار بزار اینجا. در آن فایل هم می‌توانیم همین کار را با ماژول هاستینگی که بالا تعریف شده انجام بدیم و در یک فایل دیگه بشکنیمش

pub fn eat_at_restaurant() {
    // Absolute path
    crate::front_of_house::hosting::add_to_waitlist();  // کریت در اینجا همان روت است

    // Relative path
    front_of_house::hosting::add_to_waitlist();

    // Relative path
    front_of_house::hosting::seat_at_table(); // خطا. چون این تابع پابلیک نیست و نمی‌توانیم از بیرون به آن دسترسی داشته باشیم
}
```


* The default for enum variants is to be public

```rust
mod back_of_house {
    pub enum Appetizer {
        Soup,      // اینها بای دیفالت پابلیک هستند (برخلاف آیتم‌های استراکت‌ها) و می‌توان از بیرون به آنها دسترسی داشت
        Salad,
    }
}

pub fn eat_at_restaurant() {
    let order1 = back_of_house::Appetizer::Soup;
    let order2 = back_of_house::Appetizer::Salad;
}

```

* super مثل استفاده از .. در مسیرهای دایرکتوری‌هاست. یعنی یکی برو به عقب


#### Use

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use self::front_of_house::hosting; // `use` in a relative path
// use crate::front_of_house::hosting; // `use` in a absolute path
// pub use crate::front_of_house::hosting;  // Re-exporting technique: It would public to the external usage

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
}
``

* For providing new names with `as`:

```rust
use std::fmt::Result;
use std::io::Result as IoResult;

fn function1() -> Result {
    // --snip--
}

fn function2() -> IoResult<()> {
    // --snip--
}
```

```rust
use std::io;
use std::io::Write;

// Is equal to

use std::io::{self, Write};
```

* Global operator: To bring all public items defined in a path into scope

```rust
use std::collections::*;
```




* کارگو دارای تنظیمات پیشفرض برای پنح پروفایل مختلف است و ما می‌توانیم در کارگو.توام‌ال مقادیر موردنظرمان را آورراید کنیم:
    * اطلاعات بیشتر در: https://doc.rust-lang.org/cargo/reference/profiles.html
    * Cargo has 4 built-in profiles: `dev`, `release`, `test`, and `bench`

* برای ساختن و دیدن داکیومنت: `cargo doc --open‍`
* از /// برای کامنت‌های داکیومنت استفاده میشود که از مارک‌داون پشتیبانی می‌کنند و آنچه بعدشان می‌اید را داکیومنت می‌کنند
* از !// برای کامنت گذاشتن برای کل ماژول یا کریت استفاده می‌شود
    ```rust
    //! # My Crate
    //!
    //! `my_crate` is a collection of utilities to make performing certain
    //! calculations more convenient.

    /// Adds one to the number given.
    ///
    /// # Examples
    ///
    /// ```
    /// let arg = 5;
    /// let answer = my_crate::add_one(arg);
    ///
    /// assert_eq!(6, answer);
    /// ```
    pub fn add_one(x: i32) -> i32 {
        x + 1
    }
    ```

* Exporting a Convenient Public API with pub use: برای راحت‌تر کردن دسترسی به متدها و مقادیری که بصورت تودرتو داخل کریت ما هستند استفاده می‌شود
    ```rust
    // Filename: src/lib.rs

    //! # Art
    //!
    //! A library for modeling artistic concepts.

    pub use self::kinds::PrimaryColor;
    pub use self::kinds::SecondaryColor;
    pub use self::utils::mix;

    pub mod kinds {
        // --snip--
    }

    pub mod utils {
        // --snip--
    }


    //Filename: src/main.rs

    use art::mix;   // instead of using art::utils::mix
    use art::PrimaryColor;

    fn main() {
        // --snip--
    }
    ```

* [راهنمای چگونگی پابلیش کریت ما در کریتس.آی‌او](https://doc.rust-lang.org/book/ch14-02-publishing-to-crates-io.html#setting-up-a-cratesio-account)

## Workspace
* کمک می‌کنند تا داخل کریت فعلی‌مان کریت‌های دیگر هم داشته باشیم
* همه کریت‌ها تنها یک تارگت و یک کارگو.لاک در ریشه کریت دارند اما هر کدام کارگو.توام‌ال خودشان را دارند
* مثال: برای داشتن اپ زیر چنین می‌کنیم:
    ```rust
    ├── Cargo.lock
    ├── Cargo.toml
    ├── add-one
    │   ├── Cargo.toml
    │   └── src
    │       └── lib.rs
    ├── adder
    │   ├── Cargo.toml
    │   └── src
    │       └── main.rs
    └── target

    // Filename: Cargo.toml
    [workspace]
    members = [
        "adder",
        "add-one",
    ]

    cargo new add-one --lib // کریت ادد-وان رو می‌سازیم

    // Filename: add-one/src/lib.rs
    pub fn add_one(x: i32) -> i32 {
        x + 1
    }

    // Filename: adder/Cargo.toml
    [dependencies]
    add-one = { path = "../add-one" }   // برای استفاده از ادد-وان در اددر

    // Filename: adder/src/main.rs
    use add_one;    // استفاده از ادد-وان در مِین اددر
    fn main() {
        let num = 10;
        println!(
            "Hello, world! {} plus one is {}!",
            num,
            add_one::add_one(num)
        );
    }

    cargo build // بیلد کردن تمام پروژه
    cargo run -p adder  // تنها این پکیج را ران کن

    // برای استفاده از یک کریت خارجی در داخل ورک‌اسپیس

    // Filename: add-one/Cargo.toml
    [dependencies]
    rand = "0.5.5"  // استفاده از رند در ادد-وان
    // با این کار اطلاعات این کریت در ریشه قرار گرفته و نصب می‌شود اما ما نمی‌توانیم در کریت‌های دیگر، 
    // مثلا در کریت اددر از آن استفاده کنیم،‌ مگر اینکه خط بالا را به دپندنسی آنها هم اضافه کنیم

    cargo test              // تست تمام توابع و ماژول‌های تست در پروژه
    cargo test -p add-one   // اجرای تست‌های ادد-وان

    cargo publish -all      //  پابلیش تمام پروژه
    cargo publish -p add-one  //  تنها ادد-وان را پابلیش کن
    ```

* ‍`cargo install`: برای نصب و استفاده از کریت‌های باینری. آنهایی که فقط لایبرری هستند و باینری ندارند را نصب نمی‌کند

* Extending Cargo with Custom Commands: Cargo is designed so you can extend it with new subcommands without having to modify Cargo. If a binary in your $PATH is named cargo-something, you can run it as if it was a Cargo subcommand by running cargo something. Custom commands like this are also listed when you run `cargo --list`. 
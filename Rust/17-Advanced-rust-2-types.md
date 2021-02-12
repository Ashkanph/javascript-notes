# Creating Type Synonyms with Type Aliases
* Type alias: برای اینکه یک نام جدید برای یکی از تایپ‌های راست بگذاریم تا مثلا خوانایی کد بالاتر بره یا اینکه اگر لانگ‌تایپ بود اینجوری کد ما کوتاه‌تر بشه
    ```rust
    type Kilometers = i32;

    let x: i32 = 5;
    let y: Kilometers = 5;  // در اینجا کیلومتر همون آی۳۲ هست و فرقی نداره

    println!("x + y = {}", x + y);
    ```
    * کوتاه‌تر کردن لانگ‌تایپ‌ها
        ```rust
        type Thunk = Box<dyn Fn() + Send + 'static>;
        let f: Thunk = Box::new(|| println!("hi"));

        fn takes_long_type(f: Thunk) {
            // --snip--
        }

        fn returns_long_type() -> Thunk {
            // --snip--
        }
        ```
    * An example that has been used in `std::io`
        ```rust
        type Result<T> = std::result::Result<T, std::io::Error>;
        ```

# The Never Type
* با ! مشخص می‌شود
* known in type theory lingo as the empty type because it has no values. We prefer to call it the never type because it stands in the place of the return type when a function will never return.
    ```rust
    fn bar() -> ! {
        // --snip--
        // This code is read as “the function bar returns never.” 
    }

* یک مثال
    ```rust
        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
            // در اینجا یک شاخه مچ، عدد برگردانده و یک شاخه دیگر کانتینیو
            // در یک حلقه بوده و گفته اگر ارور بود حلقه را کانتینیو کن
            // اما چطور ممکنه دو شاخه تایپ یکسانی نداشته باشند؟
            // در اینجا ممکن شده چون کانتینیو مقدار بازگشتیش برابر نِوِر تایپ هست
        };
    ```
    * The formal way of describing this behavior is that expressions of type ! can be coerced into any other type.
    * پنیک هم نِوِر تایپ برمی‌گردانه

# Dynamically Sized Types and the Sized Trait
* Sometimes referred to as DSTs or unsized types
    * not &str, but str on its own, is a DST
    * We can’t know how long the string is until runtime, meaning we can’t create a variable of type str, nor can we take an argument of type str.
    * راست نیاز داره بدانه هر تایپ چقدر طول داره برای همین در استر میایم یه اشاره‌گر به مقدار استر به همراه طول شته را ذخیره می‌کنیم
    * A &T is a single value that stores the memory address of where the T is located, a &str is two values: the address of the str and its length

* To work with DSTs, Rust has a particular trait called the `Sized` trait to determine whether or not a type’s size is known at compile time.
    ```rust
    fn generic<T>(t: T) {
        // --snip--
    }

    // is actually treated as though we had written this:

    fn generic<T: Sized>(t: T) {
        // --snip--
    }
    ```
    * By default, generic functions will work only on types that have a known size at compile time. However, you can use the following special syntax to relax this restriction:
        ```rust
        fn generic<T: ?Sized>(t: &T) {
            // --snip--
        }
        ```
## Lifetime
* ‍‍‍‍`lifetime`, which is the scope for which that reference is valid
* `borrow checker`: The Rust compiler has a borrow checker that compares scopes to determine whether all borrows are valid.
* لایف‌تایم را با ' مشخص می‌کنیم و معمولا با حروف تماما کوچک و کوتاه انتخاب می‌شوند. مثال زیر:
    ```rust
    &i32        // a reference
    &'a i32     // a reference with an explicit lifetime
    &'a mut i32 // a mutable reference with an explicit lifetime
    ```
* لایف تایم چیزی را تغییر نمیده فقط زمان اعتبار رفرنس را میگه

* از لایف‌تایم برای مواردی مثل موارد زیر استفاده می‌کنیم
```rust
// برنامه‌ای که اندازه دو استرینگ را مقایسه می‌کنه و بزرگترینش رو برمی‌گردونه
fn longest(x: &str, y: &str) -> &str {
    // Compile-time error: missing lifetime specifier: 
    // help: this function's return type contains a borrowed value, but the signature does not say whether it is borrowed from `x` or `y`
    // مشکل اینجا اینه که این تابع در زمان کامپایل نمی‌دونه کدوم‌یک از رفرنس‌های ورودی ایکس یا ایگرگ برگردونده میشه. در نتیجه باروچکر نمی‌دونه زمان عمر این رفرنس‌ها باید چقدر باشه. ما باید با لایف‌تایم این مسئله رو براش مشخص کنیم
    if x.len() > y.len() { 
        x
    } else {
        y
    }
}

fn main() {
    let string1 = String::from("abcd");
    let string2 = "xyz";

    let result = longest(string1.as_str(), string2);
    println!("The longest string is {}", result);
}

// برای حل مشکل باید تابع لانگست را اینطوری بنویسیم
// اینجا ما به تابع لانگست گفتیم که ورودی‌ها هرو دو دارای زمان عمر یکسان و برابر هستند. در ضمن گفتیم که خروجی تابع هم دارای زمان اعتباری همون اندازه س که میشه برابر با کوتاهترین زمان اعتباری که دو تا ورودی دارند
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

* بکارگیری لایف‌تایم به کاربرد ما بستگی داره. مثلا برای تابع زیر احتساجی نیست که مقدار ایگرگ هم لایف‌تایم یکسانی مثل ایکس داشته باشه
    ```rust
    fn longest<'a>(x: &'a str, y: &str) -> &'a str {
        x
    }
    ```

*  اما تابع زیر خطا میده چون لایف‌تایم هیچیک از ورودی‌ها به خروجی ربطی ندارد. در اینجا ریزالت ساخته میشه و در پایان تابع هم از بین میره
    ```rust
    fn longest<'a>(x: &str, y: &str) -> &'a str {
        let result = String::from("really long string");
        result.as_str() // cannot return value referencing local variable `result`
    }
    ```

### Lifetime Annotations in Struct Definitions
* It’s possible for structs to hold references, but in that case we would need to add a lifetime annotation on every reference in the struct’s definition.

```rust
struct ImportantExcerpt<'a> {
    part: &'a str,
}

fn main() {
    let novel = String::from("Call me Ishmael. Some years ago...");
    let first_sentence = novel.split('.').next().expect("Could not find a '.'");
    let i = ImportantExcerpt {
        part: first_sentence,
    };
}
```

* `Lifetime elision rules`: الگوهایی هستند که به مرور فهمیدند در آنها همیشه باید لایف‌تایم برای رفرنس‌ها بکار رود و آنها را در کامپایلر گذاشتند تا دیگر نیازی به ذکر لایف‌تایم نباشد و خود کامپایلر حتی با نگذاشتن لایف‌تایم‌ها برایشان لایف‌تایم بگذارد

* Lifetimes on function or method parameters are called input lifetimes, and lifetimes on return values are called output lifetimes.

### The Static Lifetime
* Means that this reference can live for the entire duration of the program
* All string literals have the 'static lifetime; They stored directly in the program’s binary, which is always available.
    ```rust
    let s: &'static str = "I have a static lifetime.";
    ```
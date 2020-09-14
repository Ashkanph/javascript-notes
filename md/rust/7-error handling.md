
* در راست دو نوع ارور داریم:
    * Recoverable: Such as a file not found error, it’s reasonable to report the problem to the user and retry the operation. Using the type `Result<T, E>`
    * Unrecoverable: Are always symptoms of bugs, like trying to access a location beyond the end of an array. Ursing the `panic!` macro

* By default, when a panic occurs, the program starts `unwinding`, which means Rust walks back up the stack and cleans up the data from each function it encounters. But this walking back and cleanup is a lot of work. The alternative is to immediately `abort`, which ends the program without cleaning up. Memory that the program was using will then need to be cleaned up by the operating system. you can choose abort in panics by adding panic = 'abort' to the appropriate [profile] sections in your Cargo.toml file. For example, if you want to abort on panic in release mode, add this:

    ```rust 
    [profile.release]
    panic = 'abort'
    ```
* برای بک‌تریس کردن یا فهمیدن محل دقیق پنیک در کد خومان باید از متغیر محیطی زیر در اجرای برنامه استفاده کنیم:

    ```rust 
    RUST_BACKTRACE=1 cargo run
    ```

## Recoverable Errors with Result

* Result type

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

*  اگر ندانیم که یک تابع آیا تایپ ریزالت را برمیگردانه یا نه می‌تانیم در داکیومنت استاندارد راست ببینیم یا اینکه به یه تایپ اشتباه منتسبش کنیم تا کامپایلر ارور بگیره و تایپ اصلیش را بهمان بگه:

```rust
    let f: u32 = File::open("hello.txt");
    // Error: expected type `u32` found enum `std::result::Result<std::fs::File, std::io::Error>`
```

* برای استفاده از ریزالت:

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt");

    let f = match f {
        Ok(file) => file,
        Err(error) => panic!("Problem opening the file: {:?}", error),
    };

    // راه خلاصه تر، استفاده از آنرپ و اکسپکت هست. هر دو اگر خطایی نبود، اوکی را برمیگردانند و اگر خطا بود پنیک می‌کنند. تنها تفاوت در این است که اکسپکت یک پیام خطای اختصاصی از کاربر میگیرد تا بعد از پنیک نشان دهد

    let f2 = File::open("hello.txt").unwrap();
    let f3‍‍ = File::open("hello.txt").expect("Failed to open hello.txt");
}

```

* برای هندل کردن خطاهای متفاوت

```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let f = File::open("hello.txt");

    let f = match f {
        Ok(file) => file,
        Err(error) => match error.kind() {
            ErrorKind::NotFound => match File::create("hello.txt") {
                Ok(fc) => fc,
                Err(e) => panic!("Problem creating the file: {:?}", e),
            },
            other_error => {
                panic!("Problem opening the file: {:?}", other_error)
            }
        },
    };
}

// کد بالا را بصورت زیر هم می‌توان نوشت که باعث تمیزتر شدن و خواناتر شدن کد می‌شود و از مچ های تودرتو جلوگیری می‌کند:

fn main() {
    let f = File::open("hello.txt").unwrap_or_else(|error| {
        if error.kind() == ErrorKind::NotFound {
            File::create("hello.txt").unwrap_or_else(|error| {
                panic!("Problem creating the file: {:?}", error);
            })
        } else {
            panic!("Problem opening the file: {:?}", error);
        }
    });
}
```

### Error propagation
* اینکه خطا را به خارج از فانکشن برگردانیم تا آنجا هندل شود

```rust
use std::fs::File;
use std::io;
use std::io::Read;

fn read_username_from_file() -> Result<String, io::Error> {
    let f = File::open("hello.txt");

    let mut f = match f { // اف را میوتیبل در نظر گرفته چون در خطهای بعدی باید به مچ فرستاده شود
        Ok(file) => file,
        Err(e) => return Err(e),
    };

    let mut s = String::new();

    match f.read_to_string(&mut s) { // میوتیبل بودن اف برای اینجا بود
        Ok(_) => Ok(s),
        Err(e) => Err(e),
    }
}

// همین کد را میتوان با عملگر ؟ خلاصه کرد. اگر جلوی فراخوانی تابعی که ریزالت را برمیگرداند از ؟ استفاده کنیم اگر خطایی وجود داشته باشد خودش همانجا تابع را تمام کرده و ارور را برمیگرداند. اگر هم خطا نباشبد که اوکی را برگردانده و تابع را ادامه می‌دهد.

// عملگر ؟ یک اختلاف با مچ دارد و آن اینکه قبل از برگرداندن خطا، آنرا به نوع خطای خروجیِ تابع، کست میکند

// تابع بالا را می‌توان بصورت خلاصه‌تر با ؟ اینطوری نوشت:
fn read_username_from_file() -> Result<String, io::Error> {
    let mut f = File::open("hello.txt")?;  // اگر اینجا خطا روی دهد مستقیما آن خطا به نوع آی‌او تبدیل شده و از تابع ریترن می‌شود و خطهای بعدی تابع اجرا نمی‌شوند
    let mut s = String::new();
    f.read_to_string(&mut s)?;  // اینجا هم همینطور
    Ok(s)
}

// تابع بالا را باز هم می‌توان خلاصه‌تر نوشت:

fn read_username_from_file() -> Result<String, io::Error> {
    let mut s = String::new();

    File::open("hello.txt")?.read_to_string(&mut s)?;

    Ok(s)
}

// این تابع بالا را باز هم می‌توان خلاصه‌تر نوشت:
use std::fs;
use std::io;

fn read_username_from_file() -> Result<String, io::Error> {
    fs::read_to_string("hello.txt") // این تابع در کتابخانه استاندارد راست وجود دارد
}
‍‍‍```

* We’re only allowed to use the `?` operator in a function that returns `Result` or `Option` or another type that implements `std::ops::Try`


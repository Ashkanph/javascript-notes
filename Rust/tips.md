
* [Write-Compile-Fix Loop Latency](https://github.com/ferrous-systems/elements-of-rust#write-compile-fix-loop-latency)
    * `cargo watch`: استفاده از این پلاگین برای اینکه مدام تغییرات کد را تحت نظر بگیره
        * برای نصبش: `cargo install cargo-watch`
    * `cargo check`: Skips the LLVM codegen and only looks for compilation errors
    * `cargo watch -s 'clear; cargo check --tests --color=always 2>&1 | head -40'`
        * این کد فکر کنم خطاها را یکی یکی نشان میده

* let expressions in Rust always accept a pattern, which lets you do things like:
	```rust
		let foo = (1, (2, 3));
		let (a, (b, _)) = foo;
	```

* "Downgrading" a mutable binding to immutable is quite common in Rust. It can be done by a block expression:
	```rust
		let fields = {
		    let mut fields = …;
		    fields.sort_by_key(…);
		    fields
		};

		// or simpky by

		let mut fields = …;
		fields.sort_by_key(…);
		let fields = fields;
	```

* To cast a float to an integer, you can use as. For example: `let b = (a / 100_000.0) as i64;`

* The assignment operator = in Rust can be chained: `a = b = 10;`

* #[no_mangle] : برای اینکه نام تابع در بیلد تغییر نکند

* Since a return statement makes the program jump somewhere, else, it's value can be anything, for example the following code cause no error:
	```rust
		fn main() {
            let _a: String = return;
        }
	```

* std::borrow::Cow
```rust
use std::borrow::Cow;

// Result must be string
fn get_name_badly() -> String {
    std::env::var("USER")               // syntax :)
    .unwrap_or("Who am I".to_string())  // performance :(
}

// Result may be String or &str
fn get_name_better() -> Cow<'static, str> {
    std::env::var("USER")
    .map(|v| Cow::Owned(v))                // syntax :()
    .unwrap_or(Cow::Borrowed("Who am I"))  // performance :)
}

// Result may be String or &str
fn get_name_best() -> Cow<'static, str> {
    std::env::var("USER")
    .map(|v| v.into())                  // syntax :()
    .unwrap_or("Who am I".into())       // performance :)
}

fn main() {
    println!("Hello, {}", get_name_badly());
    println!("Hello, {}", get_name_better());
    println!("Hello, {}", get_name_best());
}
```

* با استفاده از متد دیفالت میشه یک استراکت رو با مقادیر دیفالت ساخت
    ```rust
    use std::{path::PathBuf, time::Duration};

    // note that we can simply auto-derive Default here.
    #[derive(Default, Debug)]
    struct MyConfiguration {
        // Option defaults to None
        output: Option<PathBuf>,
        // Vecs default to empty vector
        search_path: Vec<PathBuf>,
        // Duration defaults to zero time
        timeout: Duration,
        // bool defaults to false
        check: bool,
    }

    impl MyConfiguration {
        // add setters here
    }

    fn main() {
        // construct a new instance with default values
        let mut conf = MyConfiguration::default();
        // do something with conf here
        conf.check = true;
        println!("conf = {:#?}", conf);
        /*  خروجی
        conf = MyConfiguration {
            output: None,
            search_path: [],
            timeout: 0ns,
            check: true,
        }
        */
    }
    ```

* Pass variables to closure
    ```rust
    // Use this
    #![allow(unused)]
    fn main() {
        use std::rc::Rc;

        let num1 = Rc::new(1);
        let num2 = Rc::new(2);
        let num3 = Rc::new(3);
        let closure = {
            // `num1` is moved
            let num2 = num2.clone();  // `num2` is cloned
            let num3 = num3.as_ref();  // `num3` is borrowed
            move || {
                *num1 + *num2 + *num3;
            }
        };
    }

    // Instead of this

    #![allow(unused)]
    fn main() {
        use std::rc::Rc;

        let num1 = Rc::new(1);
        let num2 = Rc::new(2);
        let num3 = Rc::new(3);

        let num2_cloned = num2.clone();
        let num3_borrowed = num3.as_ref();
        let closure = move || {
            *num1 + *num2_cloned + *num3_borrowed;
        };
    }
    ```

* میانگین گرفتن از یک آرایه وکتور از اعداد
    ```rust
    fn get_average(marks: &[f32]) -> f32 {
        (marks.iter().sum::<f32>() / marks.len() as f32).floor()
    }

    fn main() {
        assert_eq!(get_average(&[2., 2., 2., 2.]), 2.);
        assert_eq!(get_average(&[1., 5., 87., 45., 8., 8.]), 25.);
    }
    ```

* An `unsized type`, must always be used behind a pointer like `&` or `Box`.
    * examples like str, std::path::Path

* Tuple Matching
    ```rust
    let a = Some(5);
    let b = Some(false);

    let c = match a {
        Some(a) => {
            match b {
                Some(b) => whatever,
                None => other_thing,
            }
        }
        None => {
            match b {
                Some(b) => another_thing,
                None => a_fourth_thing,
            }
        }
    };

    // این بالایی را میشه بصورت جمع‌و‌جورتر پایین نوشتش

    let a = Some(5);
    let b = Some(false);

    let c = match (a, b) {
        (Some(a), Some(b)) => whatever,
        (Some(a), None) => other_thing,
        (None, Some(b)) => another_thing,
        (None, None) => a_fourth_thing,
    };
    ```

    *
    ```rust
    // iterate from 49 to 0
    for item in (0..50).rev() {}

    // iterate from 50 to 0
    for item in (0..=50).rev() {}

    // iterate from 50 to 1
    for item in (1..=50).rev() {}
    ```

* Using serde-json withour a predefined struct:
    ```rust
    use serde_json::Value;

    fn main() {
        let j = r#"{
                    "userid": 103609,
                    "verified": true,
                    "access_privileges": [
                    "user",
                    "admin"
                    ]
                }"#;

        let parsed: Value = serde_json::from_str(j).unwrap();
        
        println!("aaaa {}", Value::default());
        println!("aaaa {}", parsed["verified"] == true);
        println!("aaaa {}", parsed["userid"]);
        println!("aaaa {}", parsed["salam"]);
        println!("aaaa {}", parsed["access_privileges"][1]);
        /*
            aaaa null
            aaaa true
            aaaa 103609
            aaaa null
            aaaa "admin"
        */
    }
    ```
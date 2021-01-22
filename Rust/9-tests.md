
### Tests

* Tests are Rust functions that verify that the non-test code is functioning in the expected manner.
* To change a function into a test function, add `#[test]` on the line before fn.
* در تست در صورت خطا، پنیک برمی‌گردانیم
* می‌توانیم از این ماکروها استفاده کنیم
    * assert!
    * assert_eq!
    * and assert_ne!
* Under the surface, the assert_eq! and assert_ne! macros use the operators == and !=, respectively. When the assertions fail, these macros print their arguments using debug formatting, which means the values being compared must implement the PartialEq and Debug traits. All the primitive types and most of the standard library types implement these traits. For structs and enums that you define, you’ll need to implement PartialEq to assert that values of those types are equal or not equal. You’ll need to implement Debug to print the values when the assertion fails. Because both traits are derivable traits, as mentioned in Listing 5-12 in Chapter 5, this is usually as straightforward as adding the #[derive(PartialEq, Debug)] annotation to your struct or enum definition.
* در راست می‌توانیم فانکشن‌های پرایوت را هم در ماژول تست، تست کنیم و بهشان دسترسی داشته باشیم
* ‍‍`#[cfg(test)]` : آوردن این قبل از تعریف ماژول، مشخص می‌کند که یک ماژول تست است و فقط در هنگام کارگو تست باید اجرا شود و نه کارگو بیلد
* در راست دو جور تست داریم:
    * unit test: همینکه مولفه‌های برنامه رو بطور مجزا تست کنیم. در اینجا میشه به فانکشن‌های پرایوت ماژول‌ها دسترسی داشت. مثل مثال‌های پایین
    * integration test: اینکه تعدادی رو با هم تست کنیم. به فانکشن‌های پرایوت ماژول‌ها دسترسی نداریم. و تست‌ها را باید در فولدر زیر بنویسیم:
        *  `tests/` کدهای تست اینتگریشین باید در این فولدر باشند و برای استفاده از بخش‌های برنامه باید آنها را ادد کنیم 
        *  اگر بخواهیم از یک سری تابع هلپر برای استفاده در اینتگریشین تستمان استفاده کنیم باید آنها را در آدرس زیر بگذاریم تا با دستور کارگو تست آنها هم اجرا نشوند« 
            *  `tests/common/mod.rs`‍‍‍‍


* برای داشتن پیام کاستوم برای اسرت:

    ```rust
    #[test]
    fn greeting_contains_name() {
        let result = greeting("Carol");
        assert!(
            result.contains("Carol"),
            "Greeting did not contain name, value was `{}`",
            result
        );
    }
    ```

* برای اینکه پنیک کردن نتیجه را تست کنیم. آن استرینگ هم برای اینکه مطمئن شویم با این پیام پنیک می‌کند
    ```rust
    #[cfg(test)]
    mod tests {
        use super::*;

        #[test]
        #[should_panic(expected = "Guess value must be less than or equal to 100")] // این خط
        fn greater_than_100() {
            Guess::new(200);
        }
    }
    ```

* We can write tests that return Result<T, E>.
    * We can’t use the `#[should_panic]` annotation on tests that use `Result<T, E>`
    ```rust
    #[cfg(test)]
    mod tests {
        #[test]
        fn it_works() -> Result<(), String> {
            if 2 + 2 == 4 {
                Ok(())
            } else {
                Err(String::from("two plus two does not equal four"))
            }
        }
    }
    ```

*  `cargo test` to ‍‍compiles your code in test mode and runs the resulting test binary
    * Use `--` as a separator: 
        * Before it: List the arguments that go to cargo test
            * `cargo test --help` displays the options you can use with cargo test
        * After it: The ones that go to the test binary
            * `cargo test -- --help` displays the options you can use after the separator --

* When you run multiple tests, by default they run in parallel using threads.
    * To change number of threads: `cargo test -- --test-threads=1`

* By default, if a test passes, Rust’s test library captures anything printed to standard output.
    * به عنوان مثال اگر در تست‌ها پرینت‌ال‌ان داشته باشیم، اگر تست موفقیت‌آمیز باشه خروجی‌اش چاپ نمیشه و فقط در صورتی چاپ میشه که تست رد بشه. برای نمایش پرینت‌های تست موفقیت‌آمیز باید از این روش استفاده کنیم: ‍`cargo test -- --show-output` 

* اگر بخواهیم میتونیم فقط یک فانکشن یا مجموعه‌ای از یک سری تست‌ها رو اجرا کنیم:‌‍‍ 
    * `cargo test one_hundred` // فقط فانکشن تست وانهاندرد رو اجرا کن یا تمامی فانکشن تست‌هایی رو اجرا کن که این کلمه در اسمشون هست

* اگر تستی خیلی حجیم باشه میتانیم مشخص کنیم که اون رو ایگنور کنه
    ```rust
    #[test]
    #[ignore] // Ignore this test when cargo test
    fn expensive_test() {
        // code that takes an hour to run
    }
    ```
    اگر بخوایم تست‌های ایگنورشده را فقط اجرا کنیم: `cargo test -- --ignored`
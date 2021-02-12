
# Function pointers
* تابعی که به عنوان آرگومان به تابع دیگر پاس داده میشه
* Functions coerce to the type fn (with a lowercase f), not to be confused with the Fn closure trait. The fn type is called a function pointer. 
    ```rust
    fn add_one(x: i32) -> i32 {
        x + 1
    }

    fn do_twice(f: fn(i32) -> i32, arg: i32) -> i32 {
        f(arg) + f(arg)
    }

    fn main() {
        let answer = do_twice(add_one, 5);

        println!("The answer is: {}", answer);
    }
    ```
* Returning Closures
    ```rust
    fn returns_closure() -> dyn Fn(i32) -> i32 { // Error: doesn't have a size known at compile-time
        |x| x + 1
    }

    // تابع بالا که یک کلوژر را برمی‌گرداند خطا می‌دهد. پس آنرا بصورت زیر تغییر می‌دهیم

    fn returns_closure() -> Box<dyn Fn(i32) -> i32> {
        Box::new(|x| x + 1)
    }
    ```

* برخلاف توابع، کلوژرها نیازی به مشخص کردن تایپ پارمترها و خروجی ندارند. اما اگر بخواهیم می‌توانیم انجام دهیم و ایرادی ندارد

* We can use closures as inline anonymous functions
* کلوژرها خصوصیت مهمی دارند که توابع ندارند:
    * Closures can capture their environment and access variables from the scope in which they’re defined
    ```rust
    fn main() {
        let x = 4;

        let equal_to_x = |z| z == x; می‌تواند به ایکس از اسکوپ بیرونی دسترسی داشته باشد

        let y = 4;

        assert!(equal_to_x(y));
    }

    fn main() {
        let x = 4;

        fn equal_to_x(z: i32) -> bool {
            z == x // error[E0434]: can't capture dynamic environment in a fn item
        }

        let y = 4;

        assert!(equal_to_x(y));
    }
    ```
    * این گرفتن و ذخیره کردن مقادیر از اسکوپ بیرونی باعث مصرف مموری می‌شود و در نتیجه سرباری دارد که توابع ندارند

* تمام این توابع زیر خروجی یکسانی دارند و صحیح هستند
    ```rust
    fn  add_one_v1   (x: u32) -> u32 { x + 1 }
    let add_one_v2 = |x: u32| -> u32 { x + 1 };
    let add_one_v3 = |x|             { x + 1 };
    let add_one_v4 = |x|               x + 1  ; // ٔخ curly brackets needed because the closure body is a single expression
    ```

* کلوژرها برای هر پارامتر خود تایپی قطعی دارند و نمی‌شود تایپ را تغییر داد. به عنوان مثال، کد زیر خطا می‌دهد:
    ```rust
    let example_closure = |x| x;

    let s = example_closure(String::from("hello"));
    let n = example_closure(5); // expected struct `std::string::String`, found integer
    ```

* کلوژرها به سه روش می‌توانند به اسکوپ بیرونی دترسی داشته باشند:
    * `FnOnce` consumes the variables it captures from its enclosing scope, known as the closure’s environment. To consume the captured variables, the closure must take ownership of these variables and move them into the closure when it is defined. The Once part of the name represents the fact that the closure can’t take ownership of the same variables more than once, so it can be called only once.
    * `FnMut` can change the environment because it mutably borrows values.
    * `Fn` borrows values from the environment immutably.
    * هر سه مورد بالا تریت هستند
    * راست خودش با توجه به نحوه استفاده از کلوژر تعیین می‌کند که کدام یک از تریت‌های بالا برای کلوژر مورد استفاده قرار بگیرد
    * All closures implement FnOnce because they can all be called at least once. Closures that don’t move the captured variables also implement FnMut, and closures that don’t need mutable access to the captured variables also implement Fn.
    * If you want to force the closure to take ownership of the values it uses in the environment, you can use the move keyword before the parameter list. This technique is mostly useful when passing a closure to a new thread to move the data so it’s owned by the new thread.
        ```rust
        fn main() {
            let x = vec![1, 2, 3];

            let equal_to_x = move |z| z == x;

            println!("can't use x here: {:?}", x); // error[E0382]: borrow of moved value: `x`

            let y = vec![1, 2, 3];

            assert!(equal_to_x(y));
        }
        ```

* شبیه‌سازی کاری که زمان زیادی لازم دارد
    ```rust
    use std::thread;
    use std::time::Duration;

    fn simulated_expensive_calculation(intensity: u32) -> u32 {
        println!("calculating slowly...");
        thread::sleep(Duration::from_secs(2));
        intensity
    }
    ```

* ساخت یک کشر. که یک تابع کلوژر برای محاسبات می‌گیرد. و فقط یکبار مقداری را محاسبه می‌کند و در دفعات بعد تنها آنرا برمیگرداند بدون محاسبه دوباره
    ```rust
    struct Cacher<T>
    where
        T: Fn(u32) -> u32,
    {
        calculation: T,
        value: Option<u32>,
    }

    impl<T> Cacher<T>
    where
        T: Fn(u32) -> u32,
    {
        fn new(calculation: T) -> Cacher<T> {
            Cacher {
                calculation,
                value: None,
            }
        }

        fn value(&mut self, arg: u32) -> u32 {
            match self.value {
                Some(v) => v,
                None => {
                    let v = (self.calculation)(arg);
                    self.value = Some(v);
                    v
                }
            }
        }
    }
    ```

    * تست زیر شکست میخورد چون با دادن مقدار دو هم باز مقدار یک برگردانده می‌شود
        ```rust
        #[test]
        fn call_with_different_values() {
            let mut c = Cacher::new(|a| a);

            let v1 = c.value(1);
            let v2 = c.value(2);

            assert_eq!(v2, 2);
            // برای رفع این مشکل می‌توانیم کاری کنیم که کشر یک هش مپ از مقادیر و نتایج ذخیره کند
            // میتوانیم طوری تغییرش بدهیم که به غیر از اینتجر، تایپ جنریک بگیرد و روی همه نوع پارامتر و خروجی قابل اعمال باشد
        }
        ```

* `Concurrent programming`, where different parts of a program execute independently
*  `parallel programming`, where different parts of a program execute at the same time
* دو روش معمول پیاده‌سازی تریدها
    * Many operating systems provide an API for creating new threads. This model where a language calls the operating system APIs to create threads is sometimes called `1:1`, meaning one operating system thread per one language thread.
    * Many programming languages provide their own special implementation of threads. Programming language-provided threads are known as `green threads`, and languages that use these green threads will execute them in the context of a different number of operating system threads. For this reason, the green-threaded model is called the `M:N model`: there are M green threads per N operating system threads, where M and N are not necessarily the same number.
        * حجم نسخه ران‌تایمش زیاده برای همین در راست از این روش استفاده نمی‌شود و از روش ۱:۱ استفاده میشه. البته کریت‌هایی برای این روش هم در راست وجود داره

## ساخت ترید جدید
    ```rust
    use std::thread;
    use std::time::Duration;

    fn main() {
        let handle = thread::spawn(|| {
            for i in 1..10 {
                println!("hi number {} from the spawned thread!", i);
                thread::sleep(Duration::from_millis(1));
            }
        });

        for i in 1..5 {
            println!("hi number {} from the main thread!", i);
            thread::sleep(Duration::from_millis(1));
        }

        handle.join().unwrap(); 
        // A JoinHandle is an owned value that, 
        //      when we call the join method on it, will wait for its thread to finish
        // اگر این جوین هندل را اینجا نزاریم همینکه ترید مِین تمام بشه،‌تریدی که ساختیم هم تمام میشه قبل از اینکه تمام اون لوپ یک تا ۱۰ را انجام بده
        // با وجود این جوین هندل، خروجی بصورت زیره، اما در سیستم ممکنه کمی متفاوت باشه، مثلا ترتیبشون
        // hi number 1 from the main thread!
        // hi number 2 from the main thread!
        // hi number 1 from the spawned thread!
        // hi number 3 from the main thread!
        // hi number 2 from the spawned thread!
        // hi number 4 from the main thread!
        // hi number 3 from the spawned thread!
        // hi number 4 from the spawned thread!
        // hi number 5 from the spawned thread!
        // hi number 6 from the spawned thread!
        // hi number 7 from the spawned thread!
        // hi number 8 from the spawned thread!
        // hi number 9 from the spawned thread!

        // اگر اون جوین هندل را به قبل از لوپ مِین بزاریم، ترید مِین را بلاک میکنه تا کار تریدی که ساختیم تمام بشه،
        // بعدش میره سراغ ترید مِین و لوپ اون رو انجام میده
    }
    ```

* استفاده از متغیرها در ترید جدید
    ```rust
    use std::thread;

    fn main() {
        let v = vec![1, 2, 3];

        let handle = thread::spawn(|| {
            println!("Here's a vector: {:?}", v); // اینجا از وی در ترید جدید استفاده کردیم ولی خطا میده
            // error[E0373]: closure may outlive the current function, but it borrows `v`, which is owned by the current function
            // چرا این خطا رخ میده؟ چون ممکنه بعد از این ترید، در ترید اصلی با اون وی کاری داشته باشیم و
            // راست متوجه نشه . مثلا وی را دراپ کنیم که دیگه نمیشه در ترید جدید استفاده ش کرد
            // در نتیجه باید مووش می‌کردیم، اینجوری
            // یعنی آونر وی را به کلوژر ترید جدید می‌دادیم تا بتانه خطاهای ممکن را از پیش حدس بزنه
            // let handle = thread::spawn(move || {
        });

        handle.join().unwrap();
    }
    ```
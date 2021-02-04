
* Shared memory concurrency is like multiple ownership: multiple threads can access the same memory location at the same time.

## Mutex
* Mutex is an abbreviation for mutual exclusion. A mutex allows only one thread to access some data at any given time
* The lock is a data structure that is part of the mutex that keeps track of who currently has exclusive access to the data. Therefore, the mutex is described as guarding the data it holds via the locking system.
* در میوتکس، برای دسترسی به دیتا ابتدا باید آنرا لاک کنیم تا کس دیگه‌ای دسترسی نداشته باشه و بعد که کارمان تمام شد لاک را از رویش برداریم
    ```rust
    use std::sync::Mutex;

    fn main() {
        let m = Mutex::new(5); // تایپ Mutex<T>

        {
            let mut num = m.lock().unwrap();
            // Mutex<T> is a smart pointer. More accurately, the call to lock returns a smart pointer called MutexGuard, wrapped in a LockResult
            *num = 6;
        }   // اینجا چون عمر نام تمام میشه و دراپ میشه خودش لاک را از روی میتوکس برمی‌داره

        println!("m = {:?}", m);    // 6
    }
    ```
* Atomic Reference Counting with `Arc<T>`:
    * اگر بخواهیم از یک میتوکس در چندین ترید استفاده کنیم، از آنجایی‌که آورنرشیپش موو می‌شود،
      در نتیجه باید از اسمارت‌پوینتر آرسی استفاده کنیم که اجازه داشتن چند آونر را به ما می‌داد
      ولی نمی‌شود از آن استفاده کنیم چون در تریدها ایمن نیست و به جایش از آرک استفاده می‌کنیم
    * `Arc<T>` is a type like `Rc<T>` that is safe to use in concurrent situations. The a stands for atomic, meaning it’s an `atomically reference counted` type.
    * Atomics are an additional kind of concurrency primitive. For more info about them see https://doc.rust-lang.org/std/sync/atomic/index.html (std::sync::atomic)
    * You might then wonder why all primitive types aren’t atomic and why standard library types aren’t implemented to use Arc<T> by default. The reason is that thread safety comes with a performance penalty that you only want to pay when you really need to. If you’re just performing operations on values within a single thread, your code can run faster if it doesn’t have to enforce the guarantees atomics provide.

    ```rust
    use std::sync::{Arc, Mutex};
    use std::thread;

    fn main() {
        let counter = Arc::new(Mutex::new(0));
        let mut handles = vec![];

        for _ in 0..10 {
            let counter = Arc::clone(&counter); // اینجا کلون کردیم تا میوتکس کانتر اصلی بتواند چندین آونر داشته باشد
            let handle = thread::spawn(move || {
                let mut num = counter.lock().unwrap();

                *num += 1;
            });
            handles.push(handle);
        }

        for handle in handles {
            handle.join().unwrap();
        }

        println!("Result: {}", *counter.lock().unwrap());   // 10
    }
    ```

* Similarities Between RefCell<T>/Rc<T> and Mutex<T>/Arc<T>
    * You might have noticed that counter is immutable but we could get a mutable reference to the value inside it; this means Mutex<T> provides interior mutability, as the Cell family does. In the same way we used RefCell<T> to allow us to mutate contents inside an Rc<T>, we use Mutex<T> to mutate contents inside an Arc<T>.
    * هنگام استفاده از تایپ میوتکس راست نمی‌تواند از ما در برابر تمام خطاهای لاجیکی محافظت کند. همانطور که استفاده از آرسی نیز ممکن است به یک حلقه رفرنسی بیانجامد و باعث مموری لیک شود. به همین روش هم میتوکس‌ها می‌توانند باعث ددلاک شوند. 

* Allowing Transference of Ownership Between Threads with Send
    * The `Send` marker trait indicates that ownership of the type implementing `Send` can be transferred between threads. Almost every Rust type is `Send`, but there are some exceptions, including `Rc<T>`

* Allowing Access from Multiple Threads with Sync
    * The Sync marker trait indicates that it is safe for the type implementing Sync to be referenced from multiple threads. In other words, any type T is Sync if &T (a reference to T) is Send, meaning the reference can be sent safely to another thread. Similar to Send, primitive types are Sync, and types composed entirely of types that are Sync are also Sync
    * The smart pointer Rc<T> is also not Sync for the same reasons that it’s not Send. The RefCell<T> type and the family of related Cell<T> types are not Sync. The implementation of borrow checking that RefCell<T> does at runtime is not thread-safe. The smart pointer Mutex<T> is Sync and can be used to share access with multiple threads as you saw in the “Sharing a Mutex<T> Between Multiple Threads” section.

* Implementing Send and Sync Manually Is Unsafe
    * Because types that are made up of Send and Sync traits are automatically also Send and Sync, we don’t have to implement those traits manually. As marker traits, they don’t even have any methods to implement. They’re just useful for enforcing invariants related to concurrency.

    * Manually implementing these traits involves implementing unsafe Rust code. Building new concurrent types not made up of Send and Sync parts requires careful thought to uphold the safety guarantees. “The Rustonomicon” has more information about these guarantees and how to uphold them.

# Function pointers
* بعضی از روش‌های افزایش توان وب سرور در پردازش درخواست‌ها
    * using a thread pool
    * Or using the fork/join model 
    * Or using the single-threaded async I/O model
        * فکر کنم اکتیکس وب از این نوعه

* A `thread pool` is a group of spawned threads that are waiting and ready to handle a task. When the program receives a new task, it assigns one of the threads in the pool to the task, and that thread will process the task. The remaining threads in the pool are available to handle any other tasks that come in while the first thread is processing. When the first thread is done processing its task, it’s returned to the pool of idle threads, ready to handle a new task. A thread pool allows you to process connections concurrently, increasing the throughput of your server.
    * We’ll limit the number of threads in the pool to a small number to protect us from `Denial of Service (DoS) attacks`; if we had our program create a new thread for each request as it came in, someone making 10 million requests to our server could create havoc by using up all our server’s resources and grinding the processing of requests to a halt.

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
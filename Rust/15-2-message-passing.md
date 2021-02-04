
* `Message passing`, یکی از راه‌های محبوب اطمینان از کنکارنسی ایمن استفاده از تبادل پیام است. در راست ابزاری پیاده‌سازی آن، چنل‌ها هستند که ورودی و خروجی دارند. پیام را میزاری روی ورودی و میره و از خروجی میگیری. همزمان میتانی چند تا خروجی و یکی ورودی داشته باشی
* `mpsc` stands for multiple producer, single consumer
    ```rust
    use std::sync::mpsc;
    use std::thread;

    fn main() {
        let (tx, rx) = mpsc::channel();

        thread::spawn(move || {
            let val = String::from("hi");
            tx.send(val).unwrap();
            // اینجا دیگه نمیشه از وَل استفاده کرد چون آونرشیپش به تی‌ایکس منتقل شده و
            // اینجا دیگه وجود نداره
        });

        let received = rx.recv().unwrap();
        println!("Got: {}", received);
    }
    ```
    * دو متد برای دریافت داریم:
        * `recv`: block the main thread’s execution and wait until a value is sent down the channel. Once a value is sent, recv will return it in a `Result<T, E>`
        * `try_recv`: doesn’t block, but will instead return a `Result<T, E>` immediately: an Ok value holding a message if one is available and an `Err` value if there aren’t any messages this time.
            * میتونیم این رو در یه لوپ بزاریم که هر چند وقت یه بار اجرا بشه ببینیم پیامی اومده یا نه و بقیه کارهامون رو 
            بکنیم

* Creating Multiple Producers by Cloning the Transmitter
    ```rust
    use std::sync::mpsc;
    use std::thread;
    use std::time::Duration;

    fn main() {

        let (tx, rx) = mpsc::channel();

        let tx1 = mpsc::Sender::clone(&tx); // اینطوری کلون از نی‌ایکس می‌گیریم تا در ترید دوممان ازش استفاده کنیم
        thread::spawn(move || {
            let vals = vec![
                String::from("hi"),
                String::from("from"),
                String::from("the"),
                String::from("thread"),
            ];

            for val in vals {
                tx1.send(val).unwrap();
                thread::sleep(Duration::from_secs(1));
            }
        });

        thread::spawn(move || {
            let vals = vec![
                String::from("more"),
                String::from("messages"),
                String::from("for"),
                String::from("you"),
            ];

            for val in vals {
                tx.send(val).unwrap();
                thread::sleep(Duration::from_secs(1));
            }
        });

        for received in rx {
            println!("Got: {}", received);
        }
    }
    ```

* چند روش مختلف استفاده از پترن و مچ
    ```rust
    fn main() {
        let favorite_color: Option<&str> = None;
        let is_tuesday = false;
        let age: Result<u8, _> = "34".parse();

        if let Some(color) = favorite_color {
            println!("Using your favorite color, {}, as the background", color);
        } else if is_tuesday {
            println!("Tuesday is green day!");
        } else if let Ok(age) = age {
            if age > 30 {
                println!("Using purple as the background color");   // با داده‌های فعلی این انجام میشه
            } else {
                println!("Using orange as the background color");
            }
        } else {
            println!("Using blue as the background color");
        }
    }
    ```
* while let Conditional Loops
    ```rust
    let mut stack = Vec::new();

    stack.push(1);
    stack.push(2);
    stack.push(3);

    while let Some(top) = stack.pop() {
        println!("{}", top);
    }
    ```

* for Loops
    ```rust
    let v = vec!['a', 'b', 'c'];

    // از انیومریت استفاده کردیم تا خروجی را بصورت توپلی که ایندکس داشته باشد به ما بده
    for (index, value) in v.iter().enumerate() {
        println!("{} is at index {}", value, index);
    }
    ```

* let Statements
    * لت هم پترنه
        * ‍`let x = 5;`
        * `let PATTERN = EXPRESSION;`
        * `let (x, y, z) = (1, 2, 3);`

* Ignoring Remaining Parts of a Value with ..
    * `let (x, y, z, ..) = (1, 2, 3, 4, 5, 6);` // به غیر از ایکس و وای و زد، بقیه را نمی‌خوایم

* Ignoring an Unused Variable by Starting Its Name with _
    * `let (x, y, z, _, _) = (1, 2, 3, 4, 5);`  // به غیر از ایکس و وای و زد، اون یکی دو تا را نمی‌خوایم

* انواع  پترن: دو نوع دارند
    * irrefutable: Will match for any possible value passed are: Function parameters, let statements, and for loops
    * refutable: Can fail to match for some possible value: Some(x)
        * `if let` و `while let`: این دو تا می‌تونن هم رفیوتبل باشن و هم ایرفیوتبل

## Pattern Syntax

* مچ یه اسکوپ جدا داره و باید به این مسئله دقت کرد
    ```rust
        let x = Some(5);
        let y = 10;

        match x {
            Some(50) => println!("Got 50"),
            //  این شاخه اجرا میشه چون این ایگرگ با ایگرگ اول کد فرق داره
            Some(y) => println!("Matched, y = {:?}", y),  // Matched, y = 5
            _ => println!("Default case, x = {:?}", x),
        } // اینجا عمر ایگرگ داخلی تمام میشه

        println!("at the end: x = {:?}, y = {:?}", x, y); // at the end: x = Some(5), y = 10
    ```

* میشود از رنج یا اُر استفاده کرد
    ```rust
    // Using multiple or range in match branches
    fn main() {
        let x = 4;
        let y = 6;

        match x {
            1 | 2 => println!("one or two"),
            4 if y == 5 => println!("This wont run because y is 6"), // اینجا اگر ایگرگ را با ۶ مقایسه می‌کردیم این شاخه اجرا می‌شد
            3..=8 => println!("three through eight"), // این شاخه اجرا میشه
            _ => println!("anything"),
        }
    }

    let y = 'c';

    match y {
        'a'..='j' => println!("early ASCII letter"),
        'k'..='z' => println!("late ASCII letter"),
        _ => println!("something else"),
    }
    ```

* destructive for strucs or enums
    ```rust
    enum Color {
        Rgb(i32, i32, i32),
        Hsv(i32, i32, i32),
    }

    enum Message {
        Quit,
        Move { x: i32, y: i32 },
        Write(String),
        ChangeColor(Color),
    }

    fn main() {
        let msg = Message::ChangeColor(Color::Hsv(0, 160, 255));

        match msg {
            Message::ChangeColor(Color::Rgb(r, g, b)) => println!(
                "Change the color to red {}, green {}, and blue {}",
                r, g, b
            ),
            Message::ChangeColor(Color::Hsv(h, s, v)) => println!(
                "Change the color to hue {}, saturation {}, and value {}",
                h, s, v
            ),
            _ => (),
        }
    }
    ```

* Extra Conditionals with Match Guards
    ```rust
    let x = 4;
    let y = false;

    match x {
        4 | 5 | 6 if y => println!("yes"), // درسته که ایکس چهاره ولی این چاپ نمیشه چون ایگرگ برابر فالسه
        _ => println!("no"),    // اینجا نو چاپ میشه
    }
    ```
    * @ Bindings: برای ذخیره کردن مقدار در یک متغیر
        ```rust
        enum Message {
            Hello { id: i32 },
        }

        let msg = Message::Hello { id: 5 };

        match msg {
            Message::Hello {
                id: id_variable @ 3..=7,
            } => println!("Found an id in range: {}", id_variable), // این چاپ میشه
            // Found an id in range: 5
            Message::Hello { id: 10..=12 } => {
                println!("Found an id in another range")
            }
            Message::Hello { id } => println!("Found some other id: {}", id),
        }
        ```

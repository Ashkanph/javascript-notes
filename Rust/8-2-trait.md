## Trait

* A trait tells the Rust compiler about functionality a particular type has and can share with other types. We can use traits to define shared behavior in an abstract way.s
    * Traits are similar to a feature often called interfaces in other languages, although with some differences.
    * One restriction to note with trait implementations is that we can implement a trait on a type only if either the trait or the type is local to our crate. یعنی تنها در صورتی میتوانیم یک تریت را بر یک تایپ اعمال کنیم که یا تریت یا تایپ در کریت فعلی و لوکال ما تعریف شده باشند. یکیشان کفایت می‌کنه
        * This restriction is part of a property of programs called coherence, and more specifically the orphan rule, so named because the parent type is not present. This rule ensures that other people’s code can’t break your code and vice versa. Without the rule, two crates could implement the same trait for the same type, and Rust wouldn’t know which implementation to use.


* Example: Summary for tweets and articles
    * در این مثال، تمام اینها در یک فایل بودند. اگر مثلا در فایلی دیگر در همین کریت بودند از :: به همراه نام کریت استفاده می‌کردیم
    ```rust
    pub trait Summary { // A Summary trait that consists of the behavior provided by a summarize method
        fn summarize(&self) -> String; 
        fn summarize_author(&self) -> String; // این فقط سیگنیچر متد این تریت است و تعریفش باید در تایپی باشد که از این تریت استفاده می‌کند. باید آخرش به سمی‌کولن منتهی شود

        fn summarize(&self) -> String { // متد می‌تواند خروجی دیفالت داشته باشد تا در صورت تعریف نشدن برای یک تایپ، این را برگرداند
            format!("(Read more from {}...)", self.summarize_author())
        }
    }

    pub struct NewsArticle {
        pub headline: String,
        pub location: String,
        pub author: String,
        pub content: String,
    }

    impl Summary for NewsArticle {
        fn summarize(&self) -> String { // اینجا متد سامارایز اوررایت شده و این استفاده میشه نه خروجی دیفالتش
            format!("{}, by {} ({})", self.headline, self.author, self.location)
        }
    }

    pub struct Tweet {
        pub username: String,
        pub content: String,
        pub reply: bool,
        pub retweet: bool,
    }

    impl Summary for Tweet {
        fn summarize_author(&self) -> String {
            format!("@{}", self.username)
        }
    }

    // و برای استفاده:
    let tweet = Tweet {
        username: String::from("horse_ebooks"),
        content: String::from(
            "of course, as you probably already know, people",
        ),
        reply: false,
        retweet: false,
    };

    println!("1 new tweet: {}", tweet.summarize()); // 1 new tweet: (Read more from @horse_ebooks...)
    ```

### Traits as Parameters

```rust
// در این مثال، آیتم‌ها از انواع مختلف هستند و هر دو ساماری را پیاده‌سازی کرده‌اند
pub fn notify(item1: &impl Summary, item2: &impl Summary) { // دقت کنید که تایپ را از نوع impl گذاشتیم
    println!("Breaking news! {}, {}", item1.summarize(), item2.summarize());
}

pub fn notify<T: Summary>(item1: &T, item2: &T) { // اینطور هم میشه استفاده کرد. در اینجا هر دو آیتم‌ها از یک تایپ هستند
 // ...
}
```

* Specifying Multiple Trait Bounds with the + Syntax: اگر بیشتر از یک تریت باند شده باشد می‌توانیم چنین عمل کنیم:
    ```rust
    pub fn notify(item: &(impl Summary + Display)) { ... }
    // or
    pub fn notify<T: Summary + Display>(item: &T)  { ... }
    ```

* اگر چند تایپ جنریک با چند تریت باشد اینطور می‌توانیم عمل کنیم تا اندازه کد کمتر شده و خوانایی‌اش بیشتر شود:
    ```rust
    fn some_function<T: Display + Clone, U: Clone + Debug>(t: &T, u: &U) -> i32 { ... }
    // is better to be written like this: 
    fn some_function<T, U>(t: &T, u: &U) -> i32
        where T: Display + Clone,
            U: Clone + Debug
    { ... }
    ```

* Returning Types that Implement Traits
We can also use the impl Trait syntax in the return position to return a value of some type that implements a trait, as shown here:
    ```rust
    fn returns_summarizable() -> impl Summary {
        Tweet {
            username: String::from("horse_ebooks"),
            content: String::from(
                "of course, as you probably already know, people",
            ),
            reply: false,
            retweet: false,
        }
    }
    ```

* Using Trait Bounds to Conditionally Implement Methods:

```rust
use std::fmt::Display;

struct Pair<T> {
    x: T,
    y: T,
}

impl<T> Pair<T> { // در اینجا تایپ تی همیشه متد نیو رو ایمپلنت می‌کنه
    fn new(x: T, y: T) -> Self {
        Self { x, y }
    }
}

impl<T: Display + PartialOrd> Pair<T> {  // در اینجا تایپ تی فقط در صورتی متد سی‌ام‌دی‌دیسپلی رو ایمپلنت می‌کنه که اون تایپ داخلی تی، دیسپلی و پارتیال اوردر رو پیاده‌سازی کرده باشه (دومیش برای اینکه بشه مقایسه کرد)
    fn cmp_display(&self) {
        if self.x >= self.y {
            println!("The largest member is x = {}", self.x);
        } else {
            println!("The largest member is y = {}", self.y);
        }
    }
}
```

* `Blanket implementations`: We can also conditionally implement a trait for any type that implements another trait. Implementations of a trait on any type that satisfies the trait bounds are called blanket implementations and are extensively used in the Rust standard library. For example, the standard library implements the ToString trait on any type that implements the Display trait. The impl block in the standard library looks similar to this code:
    ```rust
    impl<T: Display> ToString for T {  // این ایمپلمنت فقط روی تایپ‌هایی از تی که دیسپلی رو پیاده‌سازی کردن قابل اجراست
        // --snip--
    }
    ```


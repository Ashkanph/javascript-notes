* In Rust, iterators are `lazy`, meaning they have no effect until you call methods that consume the iterator to use it up.
* iterators, although a high-level abstraction, get compiled down to roughly the same code as if you’d written the lower-level code yourself. Iterators are one of Rust’s zero-cost abstractions, by which we mean using the abstraction imposes no additional runtime overhead.
    * در نتیجه بهتر است از ایتریتورها استفاده کنیم تا لوپ
    * You can use iterators and closures without fear! They make code seem like it’s higher level but don’t impose a runtime performance penalty for doing so.
* All iterators implement a trait named `Iterator` that is defined in the standard library.
    ```rust
    pub trait Iterator {
        type Item; // Item is an associated type with this trait

        fn next(&mut self) -> Option<Self::Item>;

        // methods with default implementations elided
    }
    ```

    ```rust
    #[test]
    fn iterator_demonstration() {
        let v1 = vec![1, 2, 3];

        let mut v1_iter = v1.iter(); // میوتیبل تعریفش کرده‌ایم چون استفاده از نکست باعث تغییر ساختارهای داخلی ایتریتور می‌شود

        assert_eq!(v1_iter.next(), Some(&1));
        assert_eq!(v1_iter.next(), Some(&2));
        assert_eq!(v1_iter.next(), Some(&3));
        assert_eq!(v1_iter.next(), None);
    }
    ```

* The `iter` method produces an iterator over immutable references. 
* If we want to create an iterator that takes ownership of v1 and returns owned values, we can call `into_iter` instead of iter. 
    ```rust
    #[derive(PartialEq, Debug)]
    struct Shoe {
        size: u32,
        style: String,
    }

    fn shoes_in_my_size(shoes: Vec<Shoe>, shoe_size: u32) -> Vec<Shoe> {
        shoes.into_iter().filter(|s| s.size == shoe_size).collect()
    }
    ```
* Similarly, if we want to iterate over mutable references, we can call `iter_mut` instead of iter.
* Read this from stackoverflow to understand their differences: https://stackoverflow.com/questions/34733811/what-is-the-difference-between-iter-and-into-iter

* Methods that call next are called `consuming adaptors`, because calling them uses up the iterator.
    ```rust
    #[test]
    fn iterator_sum() {
        let v1 = vec![1, 2, 3];

        let v1_iter = v1.iter();

        let total: i32 = v1_iter.sum(); // ما نمی توانیم بعد از این از وی۱ایتر استفاده کنیم چون در اینجا آونرشیپش به سام رفته و دیگر قابل استفاده نیست

        assert_eq!(total, 6);
    }
    ```

* Other methods defined on the Iterator trait, known as iterator adaptors, allow you to change iterators into different kinds of iterators. You can chain multiple calls to iterator adaptors to perform complex actions in a readable way. But because all iterators are lazy, you have to call one of the consuming adaptor methods to get results from calls to iterator adaptors.
    ```rust
    let v1: Vec<i32> = vec![1, 2, 3];

    v1.iter().map(|x| x + 1); // این خط هیچ کاری نمی‌کند و اون کلوژر استفاده نمیشه چون ایتریتورها لیزی هستند و اینجا کانسیوم نشده
    let v2: Vec<_> = v1.iter().map(|x| x + 1).collect();

    assert_eq!(v2, vec![2, 3, 4]);
    ```

* برای اینکه ایتریتور را برای تایپ خودمان تعریف کنیم تنها کافی است که ایتریتور را برای تایپمان ایمپلمنت کنیم و فقط متد نکست را برایش تعریف کنیم
    ```rust
    struct Counter {
        count: u32,
    }

    impl Counter {
        fn new() -> Counter {
            Counter { count: 0 }
        }
    }

    impl Iterator for Counter {
        type Item = u32;

        fn next(&mut self) -> Option<Self::Item> {
            if self.count < 5 {
                self.count += 1;
                Some(self.count)
            } else {
                None
            }
        }
    }

    #[test]
    fn calling_next_directly() {
        let mut counter = Counter::new();

        assert_eq!(counter.next(), Some(1));
        assert_eq!(counter.next(), Some(2));
        assert_eq!(counter.next(), Some(3));
        assert_eq!(counter.next(), Some(4));
        assert_eq!(counter.next(), Some(5));
        assert_eq!(counter.next(), None);
    }

    #[test]
    fn using_other_iterator_trait_methods() {
        let sum: u32 = Counter::new()
            .zip(Counter::new().skip(1)) // pair them with values produced by another Counter instance after skipping the first value
            .map(|(a, b)| a * b)
            .filter(|x| x % 3 == 0)
            .sum();
        assert_eq!(18, sum);
        // Note that zip produces only four pairs; the theoretical fifth pair (5, None) is never produced because zip returns None when either of its input iterators return None.
        // استفاده از تمام این متدها به این دلیل امکان پذیر بوده که ما متد نکست را برای تایپمان تعریف کرده بودیم
    }
    ```
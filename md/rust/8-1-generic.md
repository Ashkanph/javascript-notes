

### Generic

* Generics are abstract stand-ins for concrete types or other properties.
* Rust implements generics in such a way that your code doesn’t run any slower using generic types than it would with concrete types. Rust accomplishes this by performing monomorphization of the code that is using generics at compile time. Monomorphization is the process of turning generic code into specific code by filling in the concrete types that are used when compiled.

```rust
    fn largest<T>(list: &[T]) -> &T {} // در توابع
    // -------------
    struct Point<T, U> {               // در استراکت‌ها
        x: T,
        y: U,
    }
    // -------------
    enum Result<T, E> {                // در اینام‌ها
        Ok(T),
        Err(E),
    }
    enum Option<T> {
        Some(T),
        None,
    }
```

* Generic for methods

```rust
        
    #[derive(Debug)]
    struct Point<T, U> {
        x: T,
        y: U,
    }

    impl<T, U> Point<T, U> {           // در متدها             
        fn mixup<V, W>(self, other: Point<V, W>) -> Point<T, W> {
            Point {
                x: self.x,
                y: other.y,
            }
        }
    }

    impl Point<f32, f32> {  // این فقط برای پوینت‌هایی تعریف می‌شود که ایکس و ایگرگش فلوت ۳۲ باشند
        fn distance_from_origin(&self) -> f32 {
            (self.x.powi(2) + self.y.powi(2)).sqrt()
        }
    }

    fn main() {
        let both_integer = Point { x: 5, y: 10 }; 
        let both_float = Point { x: 1.0, y: 4.0 }; // هم اینتجر می‌گیرد و هم اعشاری
        let integer_and_float = Point { x: 5, y: 4.0 };

        println!("{:#?}", both_float.distance_from_origin());  // 4.1231055 
        println!("{:#?}", both_integer.mixup(integer_and_float));
        // Point {
        //     x: 5,
        //     y: 4.0,
        // }
        println!("{:#?}", both_float);
        // Point {
        //     x: 1.0,
        //     y: 4.0,
        // }
    }
```
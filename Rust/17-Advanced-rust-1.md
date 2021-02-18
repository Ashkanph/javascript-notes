
# Unsafe Rust
* قرار دادن کد مورد نظر در بلاک آنسیف
* You can take five actions in unsafe Rust, called `unsafe superpowers`, that you can’t in safe Rust. Those superpowers include the ability to:
    * Dereference a raw pointer
    * Call an unsafe function or method
    * Access or modify a mutable static variable
    * Implement an unsafe trait
    * Access fields of unions

# Default Generic Type Parameters and Operator Overloading
* Associated types connect a type placeholder with a trait such that the trait method definitions can use these placeholder types in their signatures. The implementor of a trait will specify the concrete type to be used in this type’s place for the particular implementation. That way, we can define a trait that uses some types without needing to know exactly what those types are until the trait is implemented.
* پیاده‌سازی اپراتور بعلاوه برای استراکت پوینت
    ```rust
    use std::ops::Add;

    #[derive(Debug, PartialEq)]
    struct Point {
        x: i32,
        y: i32,
    }

    impl Add for Point {
        type Output = Point; // An associated type that determines the type returned from the add method.

        fn add(self, other: Point) -> Point {
            Point {
                x: self.x + other.x,
                y: self.y + other.y,
            }
        }
    }

    fn main() {
        assert_eq!(
            Point { x: 1, y: 0 } + Point { x: 2, y: 3 },
            Point { x: 3, y: 3 }
        );
    }
    ```
* پیاده‌سازی اپراتور بعلاوه برای جمع کردن میلیمتر با متر
    ```rust
    use std::ops::Add;

    struct Millimeters(u32);
    struct Meters(u32);

    impl Add<Meters> for Millimeters {
        type Output = Millimeters;

        fn add(self, other: Meters) -> Millimeters {
            Millimeters(self.0 + (other.0 * 1000))
        }
    }
    ```

# Fully Qualified Syntax for Disambiguation: Calling Methods with the Same Name
* وقتی تریت‌های اعمال‌شده برای یک استراکت دارای متدهای با نام‌های یکسان باشند هم می‌شود از آنها استفاده کرد
    ```rust
    trait Pilot {
        fn fly(&self);
    }

    trait Wizard {
        fn fly(&self);
    }

    struct Human;

    impl Pilot for Human {
        fn fly(&self) {
            println!("This is your captain speaking.");
        }
    }

    impl Wizard for Human {
        fn fly(&self) {
            println!("Up!");
        }
    }

    impl Human {
        fn fly(&self) {
            println!("*waving arms furiously*");
        }
    }

    fn main() {
        let person = Human;
        Pilot::fly(&person);
        Wizard::fly(&person);
        person.fly();
    }
    ```

* `associated functions` that are part of traits don’t have a self parameter. When two types in the same scope implement that trait, Rust can’t figure out which type you mean unless you use fully qualified syntax.
    ```rust
    trait Animal {
        fn baby_name() -> String;
    }

    struct Dog;

    impl Dog {
        fn baby_name() -> String {
            String::from("Spot")
        }
    }

    impl Animal for Dog {
        fn baby_name() -> String {
            String::from("puppy")
        }
    }

    fn main() {
        println!("A baby dog is called a {}", Dog::baby_name()); // A baby dog is called a Spot

        // println!("A baby dog is called a {}", Animal::baby_name()); 
        // اما خطا بالا کار نمی‌کند چون بیبی‌-نیم برای انیمال یک اسوشیتد فانکشن است نه یک متد و در نتیجه یک پارامتر سلف ندارد
        println!("A baby dog is called a {}", <Dog as Animal>::baby_name()); // A baby dog is called a puppy
    }
    ```

* Using Supertraits to Require One Trait’s Functionality Within Another Trait
    ```rust
    trait OutlinePrint: fmt::Display {
        fn outline_print(&self) {
            let output = self.to_string();
            let len = output.len();
            println!("{}", "*".repeat(len + 4));
            println!("*{}*", " ".repeat(len + 2));
            println!("* {} *", output);
            println!("*{}*", " ".repeat(len + 2));
            println!("{}", "*".repeat(len + 4));
        }
    }

    struct Point {
        x: i32,
        y: i32,
    }

    impl OutlinePrint for Point {}

    use std::fmt;

    impl fmt::Display for Point {
        fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
            write!(f, "({}, {})", self.x, self.y)
        }
    }

    fn main() {
        let p = Point { x: 1, y: 3 };
        p.outline_print();
        /*
        **********
        *        *
        * (1, 3) *
        *        *
        **********
        */
    }
    ```

* Using the Newtype Pattern to Implement External Traits on External Types
    ```rust
    use std::fmt;

    struct Wrapper(Vec<String>);

    impl fmt::Display for Wrapper {
        fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
            write!(f, "[{}]", self.0.join(", "))
        }
    }

    fn main() {
        let w = Wrapper(vec![String::from("hello"), String::from("world")]);
        println!("w = {}", w);  // w = [hello, world]
    }
    ```
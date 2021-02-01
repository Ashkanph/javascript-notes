* `unwinding`: Means Rust walks back up the stack and cleans up the data from each function it encounters.

* `abort`, which ends the program without cleaning up. Memory that the program was using will then need to be cleaned up by the operating system. (Read more in error handling md file)

* `buffer overread`: In C, attempting to read beyond the end of a data structure is undefined behavior. You might get whatever is at the location in memory that would correspond to that element in the data structure, even though the memory doesn’t belong to that structure.

* `backtrace`: is a list of all the functions that have been called to get to this point. 

* `Propagating Errors`: When you’re writing a function whose implementation calls something that might fail, instead of handling the error within this function, you can return the error to the calling code so that it can decide what to do.

* `Monomorphization`: The process of turning generic code into specific code by filling in the concrete types that are used when compiled.

* `The orphan rule`, so named because the parent type is not present. This rule ensures that other people’s code can’t break your code and vice versa. Without the rule, two crates could implement the same trait for the same type, and Rust wouldn’t know which implementation to use.

* `Blanket implementations`: We can also conditionally implement a trait for any type that implements another trait. Implementations of a trait on any type that satisfies the trait bounds are called blanket implementations and are extensively used in the Rust standard library. For example, the standard library implements the ToString trait on any type that implements the Display trait. The impl block in the standard library looks similar to this code:
    ```rust
    impl<T: Display> ToString for T {  // این ایمپلمنت فقط روی تایپ‌هایی از تی که دیسپلی رو پیاده‌سازی کردن قابل اجراست
        // --snip--
    }
    ```

* ‍‍‍‍`lifetime`, which is the scope for which that reference is valid

* `borrow checker`: The Rust compiler has a borrow checker that compares scopes to determine whether all borrows are valid.

* `Lifetime elision rules`: الگوهایی هستند که به مرور فهمیدند در آنها همیشه باید لایف‌تایم برای رفرنس‌ها بکار رود و آنها را در کامپایلر گذاشتند تا دیگر نیازی به ذکر لایف‌تایم نباشد و خود کامپایلر حتی با نگذاشتن لایف‌تایم‌ها برایشان لایف‌تایم بگذارد

* `primitive obsession`: Using primitive values when a complex type would be more appropriate is an anti-pattern known as `primitive obsession`.

* `closure’s environment`: The enclosing scope of the closure

* In Rust, iterators are `lazy`, meaning they have no effect until you call methods that consume the iterator to use it up.

* Iterators are one of `Rust’s zero-cost abstractions`, by which we mean using the abstraction imposes no additional runtime overhead.

* `recursive type`, where a value can have as part of itself another value of the same type.

* `cons function`:
    * “to cons x onto y” informally means to construct a new container instance by putting the element x at the start of this new container, followed by the container y.
    * A `cons list` is produced by recursively calling the cons function.
        * Each item: the value of the current item and the next item. 
        * The last item in the list contains only a value called Nil without a next item

* `interior mutability pattern`: Mutating the value inside an immutable value. (See `RefCell<T>`)

* The concept of `duck typing` in dynamically typed languages: if it walks like a duck and quacks like a duck, then it must be a duck!

* The code that results from monomorphization is doing `static dispatch`, which is when the compiler knows what method you’re calling at compile time. This is opposed to `dynamic dispatch`, which is when the compiler can’t tell at compile time which method you’re calling. In `dynamic dispatch` cases, the compiler emits code that at runtime will figure out which method to call.

[Rustonomicon Book (The art of unsafe Rust)](https://doc.rust-lang.org/stable/nomicon/index.html)

* collections can contain multiple values. Unlike the built-in array and tuple types, the data these collections point to is stored on the heap, which means the amount of data does not need to be known at compile time and can grow or shrink as the program runs. ([More about collections in the document](https://doc.rust-lang.org/stable/std/collections/index.html))


### Vector

* Vectors allow you to store more than one value in a single data structure that puts all the values next to each other in memory. Vectors can only store values of the same type. They are useful when you have a list of items, such as the lines of text in a file or the prices of items in a shopping cart. ([For more information about vectors](https://doc.rust-lang.org/stable/nomicon/vec.html))

```rust
let v: Vec<i32> = Vec::new();

let v1 = vec![1, 2, 3];  // vec! is a macro for convenience. Creates a new Vec<i32> because the default integer type is i32

let does_not_exist = &v[100];       // will cause the program to panic and crash
let does_not_exist = v.get(100);    // it returns None without panicking. Your code must have logic to handle having either Some(&element) or None
```

* Like any other struct, a vector is freed when it goes out of scope

* You can’t have mutable and immutable references in the same scope

```rust
    let mut v = vec![1, 2, 3, 4, 5];

    let first = &v[0];

    v.push(6);  // error[E0502]: cannot borrow `v` as mutable because it is also borrowed as immutable

    println!("The first element is: {}", first);
```

* Iterating over the Values in a Vector

```rust
    let v = vec![100, 32, 57];
    for i in &v {
        println!("{}", i);
    }
```

```rust
    let mut v = vec![100, 32, 57];
    for i in &mut v {
        *i += 50;  // we have to use the dereference operator (*) to get to the value in i before we can use the += operator
    }
```

*  اگر بخواهیم داده‌هایی با نوع گوناگون در وکتور‌مان ذخیره کنیم ولی نوع داده‌ها را می ‌دانیم می‌توانیم از اینام‌ها کمک بگیریم. اینطوری راست می‌دونه که هر کدام از اعضای وکتور چه تایپی دارند و فضای تخصیص داده شده در هیپ هم مشخصه. اگر نوع داده‌هامون رو ندونیم می‌تونیم از تریت‌ها استفاده کنیم.

```rust
    enum SpreadsheetCell {
        Int(i32),
        Float(f64),
        Text(String),
    }

    let row = vec![
        SpreadsheetCell::Int(3),
        SpreadsheetCell::Text(String::from("blue")),
        SpreadsheetCell::Float(10.12),
    ];
```


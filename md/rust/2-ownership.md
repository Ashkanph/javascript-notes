
Ownership: 
 همه متغیرهای تعریف شده راست در استک تعریف می‌شوند چون مقدار و سایز ثابتی دارند. 

 ولی مثلا یک String اینطور نیست.


```rust
let s1 = String::from("hello"); // "hello" is string literal
let s2 = s1; // آونر اس۱ را به اس۲ می دهیم. در نتیجه دیگر اس۱ وجود نداره
println!("{}, world!", s2);
// or let mut s1 = String::from("hello");
// تا اینجای کار هیچ مشکلی نداره. اس ۲ به مقدار اس۱ اشاره میکنه و اس۱ هم از بین رفته

println!("{}, world!", s1); //error[E0382]: borrow of moved value: `s1`
// اررور به این دلیل است که دیگر اس۱ وجود نداره و فقط اس۲ داریم
```

اینجا چون در هیپ ذخیره میشن، اس۲ یک پوینتر به اس۱ هست , و اشاره‌گر اس۱ از بین می‌رود تا هزینه ها کاهش پیدا کنه. اینجا میگیم اس ۱ موو میشه به اس۲
s1 moves to s2
و اس۱ از بین میره

* ``Rust will never automatically create “deep” copies of your data. Therefore, any automatic copying can be assumed to be inexpensive in terms of runtime performance.``

برای اینکه اس۲ پوینتری به اس۱ نباشه و متفاوت باشه باید از متد کلون استفاده کنیم که یک دیپ کلون میسازه:

```rust
let s1 = String::from("hello");
let s2 = s1.clone();
println!("{}, world!", s1);
println!("{}, world!", s2);
// هیچ مشکلی وجود نداره
```

```rust
let x = 5;
let y = x;
println!("{}, world!", x);
println!("{}, world!", y);
// This is correct. there’s no difference between deep and shallow copying here. Because they are both in the stack.
// x copies to y
```
در اینجا چون هر دو اینتیجر هستند و سایز ثابتی دارند و در استک کپی کردن یک مقدار راحت است ایگرگ هم یک داده جدید خواهد بود نه پوینتر به ایکس. یعنی خود به خود یک دیپ کلون است. به اینها تایپهای کپی میگن:

    * All the integer types, such as u32.
    * The Boolean type, bool, with values true and false.
    * All the floating point types, such as f64.
    * The character type, char.
    * Tuples, if they only contain types that are also Copy. For example, (i32, i32) is Copy, but (i32, String) is not.


* برای توابع هم همین مسيله برقراره. و اگر پارامتری به آنها پاس داده بشه در همان جا به پایان میرسه و دراپ میشه و بعد از اون نمیشه ازش استفاده کرد


```rust
fn main() {
    let s1 = gives_ownership();         // gives_ownership moves its return  value into s1

    let s2 = String::from("hello");     // s2 comes into scope

    let s3 = takes_and_gives_back(s2);  // s2 is moved into  takes_and_gives_back, which also  moves its return value into s3
    println!("s2 is {}",s2); // این ایراد میگیره، چون اس۲ به اون تابع موو شده و دیگه در این اسکوپ وجود نداره
} // Here, s3 goes out of scope and is dropped. s2 goes out of scope but was
  // moved, so nothing happens. s1 goes out of scope and is dropped.

fn gives_ownership() -> String {    // gives_ownership will move its return value into the function that calls it

    let some_string = String::from("hello"); // some_string comes into scope

    some_string                              // some_string is returned and moves out to the calling function
}

// takes_and_gives_back will take a String and return one
fn takes_and_gives_back(a_string: String) -> String { // a_string comes into
                                                      // scope
    a_string  // a_string is returned and moves out to the calling function
}
```

* برای اینکه با فرستادن متغیر به تابع، آن متغیر موو نشود از رفرنس استفاده می‌کنیم. اگر بخواهیم متغیر را در داخل تابع هم تغییر بدهیم از رفرنس میوتیبل استفاده می‌کنیم.
* We call having references as function parameters borrowing

```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
    println!("{}", s) // hello, world
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```
اما باید بدانیم در هر اسکوپ تنها یک بار برای هر متغیر میتوانیم رفرنس میوتیبل بسازیم.


```rust
let mut s = String::from("hello");
let r1 = &mut s;
let r2 = &mut s; // error[E0499]: cannot borrow `s` as mutable more than once at a time
println!("{}, {}", r1, r2);
```

* We also cannot have a mutable reference while we have an immutable one.

```rust
let mut s = String::from("hello");
let r1 = &s; // no problem
let r2 = &s; // no problem
let r3 = &mut s; // error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable

println!("{}, {}, and {}", r1, r2, r3);
```

* We call having references as function parameters borrowing.

* Note that a reference’s scope starts from where it is introduced and continues through the last time that reference is used. For instance, this code will compile because the last usage of the immutable references occurs before the mutable reference is introduced:

```rust
let mut s = String::from("hello");
let r1 = &s; // no problem
let r2 = &s; // no problem
println!("{} and {}", r1, r2);

// r1 and r2 are no longer used after this point
let r3 = &mut s; // no problem
println!("{}", r3);
```

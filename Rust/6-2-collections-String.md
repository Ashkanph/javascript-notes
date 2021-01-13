
* The String type, which is provided by Rust’s standard library rather than coded into the core language, is a growable, mutable, owned, UTF-8 encoded string type. 

* A String is a wrapper over a Vec<u8>. 

* Rust’s standard library also includes a number of other string types, such as OsString, OsStr, CString, and CStr. 

    ```rust 
    fn main() {
        let mut s1 = String::new();
        s1 = "سلام".to_string();    // Strings are UTF-8 encoded

        let s2 = String::from("بر");

        let s3 = "اشکان".to_string();


        print!("{} {} {}", s1, s2, s3);
    }
    ```

### Updating a String

```rust 
    let s1 = String::from("tic");
    let s2 = String::from("tac");
    let s3 = String::from("toe");

    let s = s1 + "-" + &s2 + "-" + &s3;
    let ss = format!("{}-{}-{}", s1, s2, s3);  // format! works like println! but instead of printing the string, it return it

    // ---------

    let mut s4 = String::from("foo");
    let s5 = "bar";
    s4.push_str(s5);
    println!("s5 is {}", s5);   // The push_str method takes a string slice because we don’t necessarily want to take ownership of the parameter. If the push_str method took ownership of s2, we wouldn’t be able to print its value on the last line.

    // ---------

    let mut s6 = String::from("lo");
    s6.push('l');   // takes a single character as a parameter
```

* You can't access to the charachters of the String by indexing. so, `let s1 = String::from("hello"); let h = s1[0];` will get an error.

    * Another point about UTF-8 is that there are actually three relevant ways to look at strings from Rust’s perspective: as bytes, scalar values, and grapheme clusters (the closest thing to what we would call letters).
        * the Hindi word “नमस्ते” as bytes: `[224, 164, 168, 224, 164, 174, 224, 164, 184, 224, 165, 141, 224, 164, 164, 224, 165, 135]`
        * as scalar values (char): `['न', 'म', 'स', '्', 'त', 'े']`
        * as grapheme clusters: `["न", "म", "स्", "ते"]`

    * One of the reasons that Rust doesn’t allow us to index into a String to get a character is that indexing operations are expected to always take constant time (O(1)). But it isn’t possible to guarantee that performance with a String, because Rust would have to walk through the contents from the beginning to the index to determine how many valid characters there were.

    * You can use `let hello = "Здравствуйте"; let s = &hello[0..4];` but must be careful in using it. And you can't do `&hello[0..1]` (It will return error).

* Methods for Iterating Over Strings:
    * Getting grapheme clusters from strings is not provided by the standard library (Crates are available on crates.io if this is the functionality you need).

    ```rust
    for c in "नमस्ते".chars() {
        println!("{}", c);
    }

    for b in "नमस्ते".bytes() {
        println!("{}", b);
    }
    ```
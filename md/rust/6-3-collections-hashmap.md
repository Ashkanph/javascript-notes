
* The type HashMap<K, V> stores a mapping of keys of type K to values of type V. It does this via a hashing function.

* Just like vectors, hash maps store their data on the heap. Like vectors, hash maps are homogeneous: all of the keys must have the same type, and all of the values must have the same type.

* By default, HashMap uses a “cryptographically strong”1 hashing function that can provide resistance to Denial of Service (DoS) attacks. This is not the fastest hashing algorithm available, but the trade-off for better security that comes with the drop in performance is worth it. اگر کدمان کند بود و امنیت هم زیاد مهم نبود می‌توانیم از هشرهای آماده و سریعتری که در کریتس.آی‌او هستن استفاده کنیم

```rust 
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);
```

* Another way of constructing a hash map is by using iterators and the collect method on a vector of tuples, where each tuple consists of a key and its value. 
 
```rust 
    use std::collections::HashMap;

    let teams = vec![String::from("Blue"), String::from("Yellow")];
    let initial_scores = vec![10, 50];

    let mut scores: HashMap<_, _> =
        teams.into_iter().zip(initial_scores.into_iter()).collect();
```

* Hashmaps and ownership

```rust
fn main() {
    use std::collections::HashMap;

    let field_name = String::from("favorite color");
    let field_value = String::from("Orange");

    let mut map = HashMap::new();
    map.insert(field_name, field_value);
    map.insert(String::from("favorite place"), String::from("Nawdar Surize"));
    map.insert(String::from("best friend"), String::from("Maman"));
    // field_name and field_value are invalid at this point, because they’ve been moved into the hash map with the call to insert.

    // To access a value
    let f_color = String::from("favorite color");
    println!("{:?}", map.get(&f_color)); // Some("Blue")
    // If there is no result, the return would be None

    // To access values with an iterator
    for (key, value) in &map {  // فقط به این دلیل رفرنس گذاشتم که بعدش دوباره میخوام از مپ استفاده کنم. اگر نمیکردم خطا میداد چون آونرشیپش رفته بود برای ایتریت کردن
        println!("my {} is {}", key, value);
    }

    // برای آپدیت یک مقدار اگر از قبل کلیدش وجود نداشته باشد
    map.entry(String::from("best friend")).or_insert(String::from("Mamoosh"));
    map.entry(String::from("favorite instrument")).or_insert(String::from("Ney"));
    println!("{:?}", map);
    // {"favorite place": "Nawdar Surize", "best friend": "Maman", "favorite instrument": "Ney", "favorite color": "Orange"}
}
  
``

   * If we insert references to values into the hash map, the values won’t be moved into the hash map. The values that the references point to must be valid for at least as long as the hash map is valid.








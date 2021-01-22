
* let expressions in Rust always accept a pattern, which lets you do things like:
	```rust
		let foo = (1, (2, 3));
		let (a, (b, _)) = foo;
	```

* "Downgrading" a mutable binding to immutable is quite common in Rust. It can be done by a block expression:
	```rust
		let fields = {
		    let mut fields = …;
		    fields.sort_by_key(…);
		    fields
		};

		// or simpky by

		let mut fields = …;
		fields.sort_by_key(…);
		let fields = fields;
	```

* To cast a float to an integer, you can use as. For example: `let b = (a / 100_000.0) as i64;`

* The assignment operator = in Rust can be chained: `a = b = 10;`

* #[no_mangle] : برای اینکه نام تابع در بیلد تغییر نکند

* Since a return statement makes the program jump somewhere, else, it's value can be anything, for example the following code cause no error:
	```rust
		fn main() {
            let _a: String = return;
        }
	```
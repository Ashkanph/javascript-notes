* A crate is a collection of Rust source code files.

* The `cargo doc --open` command, will build documentation provided by all of your dependencies locally and open it in your browser.

* The `let mut guess = String::new();` line has created a mutable variable that is currently bound to a new, empty instance of a String.
  * The & indicates that this argument is a reference, which gives you a way to let multiple parts of your code access one piece of data without needing to copy that data into memory multiple times. References are immutable by default. Hence, you need to write `&mut guess` rather than `&guess` to make it mutable.

* const vs let:
  * Constants can be declared in any scope, including the global scope (Constants are valid for the entire time a program runs, within the scope they were declared in).
  * Constants may be set only to a constant expression, not the result of a function call or any other value that could only be computed at runtime.
    * `const MAX_POINTS: u32 = 100_000;`

* Shadowing:
  * We can mutate the value with `let`
  * Can change the type of the value
  
  ```rust
  fn main() {
    let x = 5;
    let x = x + 1;
    let x = x * 2;
    println!("The value of x is: {}", x); // 12

    let spaces = "   ";
    let spaces = spaces.len();
  }
  ```


```rust
// Guessing game
//
// [dependencies]
// rand = "0.6.0"
//
use rand::Rng;
use std::cmp::Ordering;
use std::io;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}

```

* در فصل نهم (هندل کردن خطاها) می‌خوانیم که میشه برای این بازی، یک استراکت ساخت تا خطاها بهتر هندل شوند

  ```rust
  pub struct Guess {
      value: i32,
  }

  impl Guess {
      pub fn new(value: i32) -> Guess {
          if value < 1 || value > 100 {
              panic!("Guess value must be between 1 and 100, got {}.", value);
          }

          Guess { value }
      }

      pub fn value(&self) -> i32 {
          self.value
      }
  }
  ```

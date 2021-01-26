
* یک پروژه مینی‌گرپ. در فلگ‌اش یک نام فایل تکست و یک کوئری میگیرد و تمام آن خط‌هایی که آن کوئری درش باشد را برمی‌گرداند. یک متغیر محیطی هم می‌گیرد که اگر درست باشد کوچک و بزرگی حروف را در سرچ در نظر می‌گیرد

* Filename: src/main.rs

```rust
// Filename: src/main.rs

use std::env;
use std::process;

use minigrep::Config; // چون اسم پروژه مینی‌گرپ است (در کارگو.توام‌ال) و در لیب.آراس قرار گرفته پس اینطوری استفاده‌اش می‌کنیم

fn main() {
    /**
    *  std::env::args will panic if any argument contains invalid Unicode. If your program needs to accept arguments containing invalid Unicode, use std::env::args_os instead
    */

    // let args: Vec<String> = env::args().collect();           // before refactor and using iterators
    // let config = Config::new(&args).unwrap_or_else(|err| {   // before refactor and using iterators
    let config = Config::new(env::args()).unwrap_or_else(|err| {
        // We didn't use println! for printing the error here. برای دیدن دلیلش آخر همین فایل رو ببینم
        eprintln!("Problem parsing arguments: {}", err);
        process::exit(1);
    });

    if let Err(e) = minigrep::run(config) {
        // We didn't use println! for printing the error here. برای دیدن دلیلش آخر همین فایل رو ببینم
        eprintln!("Application error: {}", e);
        process::exit(1);
    }
}
```

* Filename: src/lib.rs

```rust
// Filename: src/lib.rs

use std::error::Error;
use std::fs;
use std::env;

pub struct Config {
    pub query: String,
    pub filename: String,
    pub case_sensitive: bool,
}

impl Config {
    // pub fn new(args: &[String]) -> Result<Config, &'static str> {    // before refactor and using iterators
    pub fn new(mut args: env::Args) -> Result<Config, &'static str> {

        /*
         * Before refactor and using iterators
        if args.len() < 3 {
            return Err("not enough arguments");
        }
        // This will make a full copy of the data for the Config instance to own, which takes more time and memory than storing a reference to the string data
        // It is a trade-off: giving up a little performance to gain simplicity
        let query = args[1].clone();
        let filename = args[2].clone();
        */

        args.next();    // رد کردن نام تابع که اولین گزینه آرگومان‌هاست

        let query = match args.next() {
            Some(arg) => args,
            None => return Err("Didn't get a query string"),
        }

        let filename = match args.next() {

            Some(arg) => args,
            None => return Err("Didn't get a file name"),
        }

        let case_sensitive = env::var("CASE_INSENSITIVE").is_err();

        Ok(Config {
            query,
            filename,
            case_sensitive,
        })
    }
}

/// Box<dyn Error> means the function will return a type that implements the Error trait, but we don’t have to specify what particular type the return value will be. This gives us flexibility to return error values that may be of different types in different error cases. The dyn keyword is short for “dynamic.”
pub fn run(config: Config) -> Result<(), Box<dyn Error>> {
    let contents = fs::read_to_string(config.filename)?; //the ? operator, Rather than panic! on an error, will return the error value from the current function for the caller to handle

    let results = if config.case_sensitive {
        search(&config.query, &contents)
    } else {
        search_case_insensitive(&config.query, &contents)
    };

    for line in results {
        println!("{}", line);
    }

    // We’ve declared the run function’s success type as () in the signature, which means we need to wrap the unit type value in the Ok value. This Ok(()) syntax might look a bit strange at first, but using () like this is the idiomatic way to indicate that we’re calling run for its side effects only; it doesn’t return a value we need.
    Ok(())
}

// خروجی ما وکتوری از اسلایس‌های کانتنت است،‌به همین دلیل است که لایف‌تایم دارد و لایف‌تایمش برابر لایف‌تایم کانتنت است  
pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> { 
    /*
     * Before refactor and using iterators
    let mut results = Vec::new();
    for line in contents.lines() {
        if line.contains(query) {
            results.push(line);
        }
    }
    results
    */

    contents
        .lines()
        .filter(|line| line.contains(query))
        .collect()
}

/// برای سرچ با در نظر گرفتن کوچک و بزرگی حروف
pub fn search_case_insensitive<'a>(
    query: &str,
    contents: &'a str,
) -> Vec<&'a str> {
    let query = query.to_lowercase();

    /*
     * Before refactor and using iterators
    let mut results = Vec::new();
    for line in contents.lines() {
        if line.to_lowercase().contains(&query) {
            results.push(line);
        }
    }
    results
    */

    contents
        .lines()
        .filter(|line| line.to_lowercase().contains(&query))
        .collect()
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn case_sensitive() {
        let query = "duct";
        let contents = "\
Rust:
safe, fast, productive.
Pick three.
Duct tape.";

        assert_eq!(vec!["safe, fast, productive."], search(query, contents));
    }

    #[test]
    fn case_insensitive() {
        let query = "rUsT";
        let contents = "\
Rust:
safe, fast, productive.
Pick three.
Trust me.";

        assert_eq!(
            vec!["Rust:", "Trust me."],
            search_case_insensitive(query, contents)
        );
    }
}
```

* اگر کد بالا را بصورت زیر اجرا کنیم:

    ```rust
    /*
    اگر در مِین به جای پرینت‌ال‌ان از ای‌پرینت‌ال‌ان استفاده کنیم نتیجه این میشه که خروجی درست به خروجی استاندارد میره
    و خطا در خروجی خطا نمایش داده میشه
    # with using eprintln! instead of println!
    */
    cargo run > output.txt
    Problem parsing arguments: not enough arguments
    // و فایل اوتپوت.تکست هم خالی است

    /* 
    ولی اگر در مِین به جای ای‌پرینت‌ال‌ان از پرینت‌ال‌ان استفاده کنیم
    ،با اجرای برنامه با این روش، باید خطا بدهد چون آرگومانها وجود ندارند، ولی خطایی در خروجی نمی‌بینیم
    چون خطا را در خروجی استاندارد نمایش می‌دهد و نه خروجی ارور، پس با باز کردن اوتپوت.تکست خطا را می‌بینیم:
    Problem parsing arguments: not enough arguments
    */
    cargo run > output.txt
    ```

* `Raw identifiers` are the syntax that lets you use keywords where they wouldn’t normally be allowed. You use a raw identifier by prefixing a keyword with `r#`
    ```rust
    fn r#match(needle: &str, haystack: &str) -> bool {
        haystack.contains(needle)
    }

    fn main() {
        assert!(r#match("foo", "foobar"));
    }
    ```

* Derivable Traits
    * Debug
    * PartialEq and Eq
    * PartialOrd and Ord
    * Clone and Copy
    * Hash
    * Default

* Useful Development Tools
    * Automatic Formatting with rustfmt: 
        * Install it with: `rustup component add rustfmt`
        * To use it: `cargo fmt`
    * Fix Your Code with rustfix: `cargo fix`
    * More Lints with Clippy:
        * Install it with: `rustup component add clippy`
        * To use it: `cargo clippy`

* Stable, Beta and Nightly
    * برای نصب نسخه نایتلی: `rustup toolchain install nightly`
    * برای دیدن نسخه‌های فعلی نصب‌شده در سیستم: `rustup toolchain list`
    * برای استفاده از نسخه نایتلی در یک پروژه خاص
        ```rust
        cd ~/projects/needs-nightly
        rustup override set nightly
        ```

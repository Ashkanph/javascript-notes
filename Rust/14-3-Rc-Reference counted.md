# Rc<T>, the Reference Counted Smart Pointer
* To enable multiple ownership, Rust has a type called Rc<T>, which is an abbreviation for reference counting
    * به عنوان مثال برای پیاده‌سازی گراف‌ها که هر گره میتونه چند گره اشاره‌گر دیگه داشته باشه
* keeps track of the number of references to a value which determines whether or not a value is still in use. اگر این مقدار صفر باشه میشه پاکش کرد
* We use the Rc<T> type when we want to allocate some data on the heap for multiple parts of our program to read and we can’t determine at compile time which part will finish using the data last. If we knew which part would finish last, we could just make that part the data’s owner, and the normal ownership rules enforced at compile time would take effect.
* Rc<T> is only for use in single-threaded scenarios

* مثال: فرض کنید همان کانس‌لیست قبلی را داشته باشیم. در اینجا اگر بخواهیم دو مقدار بی و سی به مقدار آ اشاره کنند اگر با روش قبلی باکس پیش بریم به مشکل می‌خوریم:
    ```rust
    enum List {
        Cons(i32, Box<List>),
        Nil,
    }

    use crate::List::{Cons, Nil};

    fn main() {
        let a = Cons(5, Box::new(Cons(10, Box::new(Nil))));
        let b = Cons(3, Box::new(a));
        let c = Cons(4, Box::new(a)); // error[E0382]: use of moved value: `a`
        // برای حل مشکل می‌توان مقدار باکس را رفرنس گذاشت اما مشکل جدیدی پیش می‌آید، اینکه آخرین آیتم یعنی رفرنس نیل را چطور بنویسیم؟ پس امکانش نیست
    }

    // پس به جای این کار از تایپ آرسی استفاده می‌کنیم
    enum List {
        Cons(i32, Rc<List>),
        Nil,
    }

    use crate::List::{Cons, Nil};
    use std::rc::Rc;

    fn main() {
        let a = Rc::new(Cons(5, Rc::new(Cons(10, Rc::new(Nil)))));
        let b = Cons(3, Rc::clone(&a));     // استفاده از این کلون با کلون خود آ تفاوت داره و دیپ‌کلون نیست و باعث سربار به برنامه نمیشه
        // این کلون تنها یک شماره به کانتر رفرنس اضافه میکنه. برای همین برای ریفکتور و امپروومنت مشکلی پیش نمیاره
        let c = Cons(4, Rc::clone(&a));
    }
    ```
* اگر بخوایم تعداد رفرنس‌ها به یک اینستنس از آرسی را ببینیم می‌توانیم اینطور عمل کنیم: `Rc::strong_count(&a)`
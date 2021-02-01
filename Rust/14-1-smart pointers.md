* رفرنس‌ها، پوینترهای ساده‌ای هستند که آدرسی را نگه می‌دارند که داده در آن ذخیره شده ولی اسمارت پوینترها، دیتا استراکچرهایی هستند که به غیر از آدرس جایی که بهش اشاره می‌کنند، متادیتاها و قابلیت‌های دیگری هم دارند. در زبان سی پلاس پلاس هم وجود دارند
    * `String` and `Vec<T>` are smart pointers
* تفاوت دیگرشان با رفرنس‌ها: References are pointers that only borrow data; in contrast, in many cases, smart pointers own the data they point to
* معمولا با استراکت پیاده‌سازی می‌شوند. تفاوتشان با استراکت‌های ساده اینه که تریت‌های درف و دراپ را پیاده‌سازی می‌کنند
    * The `Deref` trait allows an instance of the smart pointer struct to behave like a reference so you can write code that works with either references or smart pointers.
    * The `Drop` trait allows you to customize the code that is run when an instance of the smart pointer goes out of scope. 
* Most common smart pointers in the standard library:
    * `Box<T>` for allocating values on the heap
    * `Rc<T>`, a reference counting type that enables multiple ownership
    * `Ref<T>` and `RefMut<T>`, accessed through `RefCell<T>`, a type that enforces the borrowing rules at runtime instead of compile time
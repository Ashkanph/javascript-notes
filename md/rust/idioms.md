* `unwinding`: Means Rust walks back up the stack and cleans up the data from each function it encounters.

* `abort`, which ends the program without cleaning up. Memory that the program was using will then need to be cleaned up by the operating system. (Read more in error handling md file)

* `buffer overread`: In C, attempting to read beyond the end of a data structure is undefined behavior. You might get whatever is at the location in memory that would correspond to that element in the data structure, even though the memory doesn’t belong to that structure.

* `backtrace`: is a list of all the functions that have been called to get to this point. 

* `Propagating Errors`: When you’re writing a function whose implementation calls something that might fail, instead of handling the error within this function, you can return the error to the calling code so that it can decide what to do.

* `Monomorphization`: The process of turning generic code into specific code by filling in the concrete types that are used when compiled.

* `The orphan rule`, so named because the parent type is not present. This rule ensures that other people’s code can’t break your code and vice versa. Without the rule, two crates could implement the same trait for the same type, and Rust wouldn’t know which implementation to use.
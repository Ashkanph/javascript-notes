
## Overview



۱ -  گوروتین یک ترید سبک است که بدست 

    ```Go runtime``` 
    
    مدیریت می‌شود. دستور 
    
    go f(x, y, z)
    
     در گوروتین فعلی ارزیابی می‌شود ولی اجرای آن در یک گوروتین جدید انجام می‌شود.  

Goroutines run in the same address space, so access to shared memory must be synchronized. The sync package provides useful primitives, although you won't need them much in Go as there are other primitives.

۲ - ```Channel``` یک مجرای دارای تایپ است که می‌توان مقادیر را در آن با استفاده از عملگر ```->``` ریخت و بازیابی کرد. کانال‌ها را نیز می‌بایست همچون مپ‌ها و اسلایس‌ها پیش از استفاده ساخت: ([مثال گوروتین](#goroutine))

```go
ch := make(chan int)
```
 - <p style="direction: ltr;">By default, sends and receives block until the other side is ready. This allows goroutines to synchronize without explicit locks or condition variables.</p>

 - کانال‌ها می‌توانند بافر داشته باشند. اگر بافر پر شده باشد ریختن در کانال باعث خطا می‌شود. خواندن از کانال خالی هم باعث خطا می‌شود.
	```go
	ch := make(chan int, 100) // Buffer size: 100
	```
 - یک فرستنده می‌توان کانال را ببندد و با این کار به دریافت‌کننده بفهماند که دیگر داده‌ای در این کانال قرار نمی‌گیرد. دریافت‌کننده با گرفتن یک پارامتر دوم از کانال می‌تواند بفهمد کانال باز (ترو) یا بسته (فالس) است:
	```go
	v, ok := <-ch
	```

	- فقط یک فرستنده می‌تواند کانال را ببندد. فرستادن مقدار روی کانال بسته‌شده باعث پنیک می‌شود.
	- کانال‌ها مانند فایل‌ها نیستند و الزامی در بستن آن‌ها نیست. تنها در مواقعی آنها را می‌ببندیم که به خواهیم به دریافت‌کننده بفهمانیم که دیگر مقداری بر روی کانال قرار نمی‌گیرد.
	- با استفاده از حلقه
    
        ```for i := range ch``` 
     
        می‌توان مقادیر یک کانال را تا هنگام بسته‌شدنش گرفت. ([مثال حلقه کانال](#channel)) 

<p style="direction: ltr;">3 - The select statement lets a goroutine wait on multiple communication operations. 
A select blocks until one of its cases can run, then it executes that case. It chooses one at random if multiple are ready. ([مثال select](#select))</p>

<p style="direction: ltr;"> - The default case in a select is run if no other case is ready. ([مثال defaultSelect](#defaultselect))</p>

۴ - ([مثال پیمایش یک درخت دودویی و مقایسه دو درخت دودویی با کانال](#binarytree))</p>

![Binary tree](/home/gmtii/Desktop/tree.png)

۵ - از ```sync.Mutex``` یا همان ```mutual exclusion``` برای قفل گذاشتن روی متغیرها و جلوگیری از کانفلیکت برنامه‌ها استفاده می‌شود. ([مثال mutex](#mutex))




----

---
## goRoutine
([برگشت به بالا](#overview))

```go
package main
import "fmt"
func sum(s []int, c chan int) {
	sum := 0
	for _, v := range s {
		sum += v
	}
	c <- sum // send sum to c
	fmt.Println(s)
}
func main() {
	s := []int{7, 2, 8, -9, 4, 0}

	c := make(chan int)
	go sum(s[:len(s)/2], c)		// آرایه اس را تقسیم کرده‌ایم و بدست آوردن مجموع عناصر هر یک از دو بخش را به یک گوروتین سپرده‌ایم و در آخر دو نتیجه را با هم جمع می‌کنیم
	go sum(s[len(s)/2:], c)
	x, y := <-c, <-c // receive from c

	fmt.Println(x, y, x+y)
	/*
		[-9 4 0]
		[7 2 8]
		-5 17 12
	*/
}
```

---
## Channel
([برگشت به بالا](#overview))

```go
package main
import (
	"fmt"
)
func fibonacci(n int, c chan int) {
	x, y := 0, 1
	for i := 0; i < n; i++ {
		c <- x
		x, y = y, x+y
	}
	close(c)	// فرستنده در اینجا کانال را می‌بندد، بدین معنی که دیگر قرار نیست مقداری در آن قرار گیرد.
}
func main() {
	c := make(chan int, 10)		// ساخت یک کانال با اندازه بافر ۱۰
	go fibonacci(cap(c), c)
	for i := range c {		// دریافت تمام مقادیر کانال به ترتیب با این حلقه تا هنگام بسته‌شدنش
		fmt.Println(i)
	}
	/*
		0
		1
		1
		2
		3
		5
		8
		13
		21
		34
	*/
}
```
---
## Select
([برگشت به بالا](#overview))

```go
package main
import "fmt"
func fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
		case c <- x:
			x, y = y, x+y
		case <-quit:
			fmt.Println("quit")
			return
		}
	}
}
func main() {
	c := make(chan int)
	quit := make(chan int)
	go func() {
		for i := 0; i < 10; i++ {
			fmt.Println(<-c)
		}
		quit <- 0
	}()
	fibonacci(c, quit)
	/*
		0
		1
		1
		2
		3
		5
		8
		13
		21
		34
		quit
	*/
}
```
---
## defaultSelect
([برگشت به بالا](#overview))

```go
package main
import (
	"fmt"
	"time"
)
func main() {
	tick := time.Tick(100 * time.Millisecond)
	boom := time.After(500 * time.Millisecond)
	for {
		select {
		case <-tick:
			fmt.Println("tick.")
		case <-boom:
			fmt.Println("BOOM!")
			return
		default:
			fmt.Println("    .")
			time.Sleep(50 * time.Millisecond)
		}
	}
	/*
			.
			.
		tick.
			.
			.
		tick.
			.
			.
		tick.
			.
			.
		tick.
			.
			.
		BOOM!
	*/
}
```
---

## binaryTree
([برگشت به بالا](#overview))

```go
package main
import (
	"fmt"
	"golang.org/x/tour/tree"
)

func walkImpl(t *tree.Tree, ch chan int) {// تابعی برای پیمایش درخت دودویی
	if t == nil {
		return
	}
	walkImpl(t.Left, ch)
	ch <- t.Value
	walkImpl(t.Right, ch)
}

// Walk walks the tree t sending all values
// from the tree to the channel ch.
func Walk(t *tree.Tree, ch chan int) {
	walkImpl(t, ch)
	// Need to close the channel here
	close(ch)		// بستن کانال
}

// Same determines whether the trees
// t1 and t2 contain the same values.
// NOTE: The implementation leaks goroutines when trees are different.
// See binarytrees_quit.go for a better solution.
func Same(t1, t2 *tree.Tree) bool {
	w1, w2 := make(chan int), make(chan int)

	go Walk(t1, w1)
	go Walk(t2, w2)

	for {
		v1, ok1 := <-w1
		v2, ok2 := <-w2
		if !ok1 || !ok2 {
			return ok1 == ok2
		}
		if v1 != v2 {
			return false
		}
	}
}

func main() {
	// The function tree.New(k) constructs a randomly-structured (but always sorted) binary tree holding the values k, 2k, 3k, ..., 10k

	fmt.Print("tree.New(1) == tree.New(1): ")
	if Same(tree.New(1), tree.New(1)) {
		fmt.Println("PASSED")
	} else {
		fmt.Println("FAILED")
	}

	fmt.Print("tree.New(1) != tree.New(2): ")
	if !Same(tree.New(1), tree.New(2)) {
		fmt.Println("PASSED")
	} else {
		fmt.Println("FAILED")
	}

	// -------------

	go Walk(tree.New(1), ch)
	for i := range ch {  // دریافت تمام مقادیر کانال به ترتیب با این حلقه تا هنگام بسته‌شدنش
        fmt.Println(i)
    }
}
```
---


## mutex
([برگشت به بالا](#overview))

```go
package main
import (
	"fmt"
	"sync"
	"time"
)

// SafeCounter is safe to use concurrently.
type SafeCounter struct {
	v   map[string]int
	mux sync.Mutex
}

// Inc increments the counter for the given key.
func (c *SafeCounter) Inc(key string) {
	c.mux.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	c.v[key]++
	c.mux.Unlock()
}

// Value returns the current value of the counter for the given key.
func (c *SafeCounter) Value(key string) int {
	c.mux.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	defer c.mux.Unlock()
	return c.v[key]
}

func main() {
	c := SafeCounter{v: make(map[string]int)}
	for i := 0; i < 1000; i++ {
		go c.Inc("somekey")
	}

	time.Sleep(time.Second)
	fmt.Println(c.Value("somekey"))
	// 1000
}
```

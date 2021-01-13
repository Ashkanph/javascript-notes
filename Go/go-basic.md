
## Overview

۱-  در پکیج‌های گو، فقط آن‌هایی را که اکسپورت شده‌اند می‌توان خارج از پکیج فراخوانی کرد. برای اینکه یک متد، متغیر یا اینترفیس را اکسپورت کنیم فقط کافی است که حرف اول آن‌را بزرگ بنویسیم.

۲- از ```=:``` تنها در داخل توابع می‌توان استفاده کرد (برای مواقعی است که متغیر مورد نظر را با وَر از پیش تعریف نکرده باشیم).

var

۳- تایپ‌های اولیه در گو:

- bool
- string
- int  int8  int16  int32  int64
- uint uint8 uint16 uint32 uint64 uintptr
- byte // alias for uint8
- rune // alias for int32, represents a Unicode code point
- float32 float64
- complex64 complex128
- دقت داشته باشید که مقادیر زیر در سیستم‌های ۳۲ بیتی برابر ۳۲ بیت و در سیستم‌های ۶۴ بیتی برابر ۶۴ بیت می‌باشند 
    - ```int```, ```uint```, ```uintptr```

۴- در گو، فقط یک حلقه داریم و آن هم فور است.

۵- در دستور سوییچ، بطور اتوماتیک بریک را در انتهای هر کیس می‌آورد. 

۶- دستور دیفر بعد از اتمام تابع دربرگیرنده اجرا می‌شود. دستور داخل آن در یک پشته ذخیره می‌شود.
  - از کاربردهای دستور دبفر در استفاده از میوتکس (برای آنلاک کردن فایل لاک شده)، بستن فایل باز شده ([مثالش در بخش کدها](#def>er)) و برای بکارگیری همراه دستورات پنیک (متوقف کردن اجرای یک تابع) و ریکاور (از سرگیری مجدد اجرای تابع) است.

۷- طول آرایه‌ها را نمی‌توان تغییر داد و همیشه ثابت هستند. ([مثال آرایه‌ها](#array))

۸ - از اسلایس برای انتخاب بخشی از یک آرایه می‌توان استفاده کرد و مثل یک رفرنس به عناصر آرایه عمل می‌کند یعنی اگر مقدار آن تغییر کند مقدار مورد نظر در آرایه نیز تغییر می‌کند. دقت کنید که در گرفتن [عدد اول، عدد دوم]، عدد اول جزیی از اسلایس است اما عدد دوم در اسلایس نمی‌آید. می‌توان از اسلایس برای تعریف یک آرایه بدون مشخص کردن آن سایز آن هم استفاده کرد. ([مثال slice](#slice))
- طول اسلایس <bdi>```len(s)```</bdi> برابر تعداد عناصر داخل آن

-  ظرفیت آن <bdi>```cap(s)```</bdi> برابر تعداد عناصر آرایه اصلی از عنصر شروع اسلایس تا آخر آرایه اصلی است.
- از ```:``` هم می‌توان برای انتخاب اسلایس استفاده کرد. بصورت زیر:

```go	
var a [10]int
a[0:10] ≡ a[:10] ≡ a[0:] ≡ a[:]
```

- مقدار پیش‌فرض یک اسلایس برابر نیل است.
<!--- The zero value of a slice is nil. --->

```go	
var s []int
fmt.Println(s == nil)
// true
```

۹ - از دستور مِیک می‌توان برای ساختن یک آرایه (اسلایس) با اندازه داینامیک استفاده کرد. ([مثال make](#make))
<!--- We can use the built-in make function to create dynamically-sized arrays. --->

۱۰ - از دستور اپند می‌توان برای افزودن عنصر یا عناصر جدید به اسلایس استفاده کرد. اگر طول اسلایس کم باشد اسلایسی جدید با اندازه بزرگتر را به آن اختصاص می‌دهد.

```go	
var f = []int{5, 6}
f = append(f, 7, 8, 9, 10)
fmt.Println(f)
// [5 6 7 8 9 10]
```

۱۱ - از دستور رنج می‌توان به عنوان یک ایتریتور اسلایس یا مپ استفاده کرد. دو مقدار برمی‌گرداند که مقدار اول بیانگر ایندکس و مقدار دوم کپی مقدار عنصر آن ایندکس است. برای اسکیپ کردن مقدار یا ایندکس، می‌توان آن را به _ اختصاص داد ([مثال range](#range))

۱۲ - از مپ برای نگاشت کلیدها به مقادیر استفاده می‌شود و مقدار صفر آن برابر با نیل است. با تابع مِیک می‌توان یک مپ حاضر از تایپ مورد نظر را ساخت. ([مثال map](#map))
  - ([مثالی برای شمردن تعداد کلمات در یک رشته و برگرداندن نتیجه بصورت مپ](#wordcountwithmap))

۱۳ - از توابع گو می‌توان بصورت کلوژر هم استفاده کرد. ([مثال کلوژر و توضیحاتش](#closure))

---

## کدها
([برگشت به بالا](#overview))

```go
package main
import (
	"fmt"
	"math"
)
func main() {
	fmt.Printf("Now you %s have %g problems.", "salam", math.Sqrt(4))
    // Now you salam have 2 problems.
	// %t: نوع آن, ٪v: مقدار آن
}
```
------
```go
package main
import "fmt"

func main() {
	fmt.Println(func (x, y int) (int, string) {
				return x + y, "Ashkan"
			}(10, 13))
	// 23 Ashkan
}

// -------- or ---------

func main() {
	fmt.Println(func (x, y int) (sum int, name string) {
				sum  = x + y
				name = "Ashkan"
				return
			}(10, 13))
	// 23 Ashkan
	// نباید در توابع طولانی از این روش برای برگرداندن خروجی‌ها استفاده شود چون خوانایی کد را کم می‌کند
}
```
------

```go
const (
	/* Create a huge number by shifting a 1 bit left 100 places. */
	// In other words, the binary number that is 1 followed by 100 zeroes.
	Big = 1 << 100
	/* Shift it right again 99 places, so we end up with 1<<1, or 2. */
	Small = Big >> 99
)
```

------
```go
func main() {
	for {		 // حلقه بی‌نهایت
 	}
}
```
------
```go
func main() {
	sum := 1
	for sum < 1000 { // while loop!!!!
		sum += sum
	}
	fmt.Println(sum)
}
```
------
```go
package main
import (
	"fmt"
	"math"
)
func pow(x, n, lim float64) float64 {
	// The scope of v is in the if and else statement 
	if v := math.Pow(x, n); v < lim {
		return v
	} else {
		fmt.Printf("%g >= %g\n", v, lim)
	}
	// can't use v here, though
	return lim
}
func main() {
	fmt.Println(
		pow(3, 2, 10),
		pow(3, 3, 20),
	)
	/* 27 >= 20  دقت کنید که ابتدا هر دو تابع اجرا می‌شوند و سپس خروجی‌ها به تابع پرینت منتقل می‌شوند. به همین دلیل این متن قبل از خروجی‌ها نمایش داده شده‌است
	   9 20   */
	
}
```
------
```go
package main
import (
	"fmt"
	"runtime"
)
func main() {
	fmt.Print("Go runs on ")
	switch os := runtime.GOOS; os { // GOOS is an environmental variable
	case "darwin":
		fmt.Println("OS X.")
	case "linux":
		fmt.Println("Linux.")
	default:
		// freebsd, openbsd,
		// plan9, windows...
		fmt.Printf("%s.", os)
	}
}
```
------
```go
package main
import (
	"fmt"
	"time"
)
func main() {
	t := time.Now()  // پکیج تایم برای زمان و تاریخ
	fmt.Println(t)
	fmt.Println(t.Hour())
	fmt.Println(t.Weekday())
	/*
		2018-01-23 15:37:48.561322379 +0330 +0330 m=+0.000129528
		15
		Tuesday
	*/
}
```
------

## defer
([برگشت به بالا](#overview))

```go
package main  
import "fmt"
func main() {
	fmt.Println("counting")
	for i := 0; i < 4; i++ {
		defer fmt.Println(i)    // مثال کاربرد دستور دفر
	}
	fmt.Println("done")  
	/* 	
		counting 
		done
		3
		2
		1
		0
	*/
}  
```
------
```go
func c() (i int) {  // خروجی این تابع همیشه ۲ است
    defer func() { i++ }()
    return 1
}
```
------
```go
func CopyFile(dstName, srcName string) (written int64, err error) {
    src, err := os.Open(srcName)
    if err != nil {
        return
    }

    dst, err := os.Create(dstName)
    if err != nil {
        return
    }

    written, err = io.Copy(dst, src)
    dst.Close()
    src.Close()
    return
}

/*
کد بالا برای کپی یک فایل از آدرس مبدا به آدرس مقصد یک ایراد دارد و
 ایراد آن این است که اگر در ساختن فایل مقصد مشکلی پیش بیاید فایل مبدا باز باقی می‌ماند.
  برای حل آن از دستور دفر بصورت زیر استفاده می‌کنیم:
*/

func CopyFile(dstName, srcName string) (written int64, err error) {
    src, err := os.Open(srcName)
    if err != nil {
        return
    }
    defer src.Close()

    dst, err := os.Create(dstName)
    if err != nil {
        return
    }
    defer dst.Close()

    return io.Copy(dst, src)
} 
```
------

## struct
([برگشت به بالا](#overview))

```go
package main
import "fmt"
type Vertex struct {
	X, Y int
}
var (
	v1 = Vertex{1, 2}  // has type Vertex
	v2 = Vertex{X: 1}  // Y:0 is implicit
	v3 = Vertex{}      // X:0 and Y:0
	p  = &Vertex{1, 2} // has type *Vertex
)
func main() {
	fmt.Println(v1, v2, v3, p, *p)
	// {1 2} {1 0} {0 0} &{1 2} {1 2}
}
```
------

## array
([برگشت به بالا](#overview))

```go
package main
import "fmt"
func main() {
	var a [2]string
	a[0] = "Hello"
	a[1] = "World"
	fmt.Println(a[0], a[1])
	fmt.Println(a)

	primes := [6]int{2, 3, 5, 7, 11, 13}
	fmt.Println(primes)
	/*
		Hello World
		[Hello World]
		[2 3 5 7 11 13]
	*/
}
```
------

## slice
([برگشت به بالا](#overview))

```go
package main
import "fmt"
func main() {
	numbers := [6]int{1, 2, 3, 4, 5, 6}

	var x []int = numbers[1:4] // یعنی عنصر یکم و دوم و سوم از آرایه نامبر را بگیر
	var y []int = numbers[2:4]
	fmt.Println(numbers)
	fmt.Println(x)
	fmt.Println(y)
	y[0] = 17
	fmt.Println("--------")
	fmt.Println(x)
	fmt.Println(y)
	fmt.Println(numbers)
	/*
		[1 2 3 4 5 6]
		[2 3 4]
		[3 4]
		--------
		[2 17 4]
		[17 4]
		[1 2 17 4 5 6]
	*/
	// -------------------------------
	s := []struct { // یک اسلایس لیترال (آرایه بدون سایز) با مقادیر از نوع استراکت که داده‌هایش را هم بعدش بهش داده‌ایم
		i int
		b bool
	}{
		{2, true},
		{3, false},
		{5, true},
	}
	fmt.Println(s)
	// [{2 true} {3 false} {5 true}]
	// -------------------------------
	s := []int{2, 3, 5, 7, 11, 13}
	printSlice(s)
	// len=6 cap=6 [2 3 5 7 11 13]

	// Slice the slice to give it zero length.
	s = s[:0]
	printSlice(s)
	// len=0 cap=6 []

	// Extend its length.
	s = s[:4]
	printSlice(s)
	// len=4 cap=6 [2 3 5 7]

	// Drop its first two values.
	s = s[2:]
	printSlice(s)
	// len=2 cap=4 [5 7]
	
	s = s[:]
	printSlice(s)
	// len=2 cap=4 [5 7]
}

func printSlice(s []int) {
	fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
}
```
------

## make
([برگشت به بالا](#overview))

```go
package main
import "fmt"
func main() {
	a := make([]int, 5)
	printSlice("a", a)
	// a len=5 cap=5 [0 0 0 0 0]

	b := make([]int, 0, 5) // پارامتر دوم برابر طول اسلایس و پارامتر سوم برابر ظرفیت آن است.
	printSlice("b", b)
	// b len=0 cap=5 []

	c := b[:2]
	printSlice("c", c)
	// c len=2 cap=5 [0 0]

	d := c[2:5]
	printSlice("d", d)
	// d len=3 cap=3 [0 0 0]
}

func printSlice(s string, x []int) {
	fmt.Printf("%s len=%d cap=%d %v\n",
		s, len(x), cap(x), x)
}
```
------

## range
([برگشت به بالا](#overview))

```go
package main
import "fmt"
var pow = []int{1, 2, 4, 8}
func main() {
	for i, v := range pow {
		fmt.Printf("2**%d = %d\n", i, v)
	}
	/*
		2**0 = 1
		2**1 = 2
		2**2 = 4
		2**3 = 8
	*/
	//----------- skipping ------------
	pow2 := make([]int, 4)
	for i := range pow2 {			// skip value
		pow2[i] = 1 << uint(i) 		// == 2**i
	}
	for _, value := range pow2 { 	// skip index
		fmt.Printf("%d\n", value)
	}
	/*
		1
		2
		4
		8
	*/
}
```
------

## map
([برگشت به بالا](#overview))

```go
package main
import "fmt"
type Vertex struct {
	Lat, Long float64
}
var m map[string]Vertex
func main() {
	m = make(map[string]Vertex)
	m["Bell Labs"] = Vertex{
		40.68433, -74.39967,
	}
	fmt.Println(m["Bell Labs"])
	// {40.68433 -74.39967}

	// -------- or -----------

	var m2 = map[string]Vertex{
		"Bell Labs": {40.68433, -74.39967},
		"Google":    {37.42202, -122.08408},
	}
	fmt.Println(m2)
	// map[Bell Labs:{40.68433 -74.39967} Google:{37.42202 -122.08408}]
}
```
## wordCountWithMap
([برگشت به بالا](#overview))

```go
package main
import (
	"strings"
	"fmt"
)
func WordCount(s string) map[string]int {
	m := make(map[string]int)
	for _, f := range strings.Fields(s) {	// strings.Field تبدیل رشته به آرایه‌ای از کلماتی که با اسپیس از هم جدا شده‌اند
		m[f]++
	}
	return m
}
func main() {
	fmt.Println(WordCount("Salam Ashkan, How do you do"))
}
```
## closure
([برگشت به بالا](#overview))

```go
package main
import "fmt"
/*
‍Go functions may be closures. A closure is a function value that references variables from outside its body. The function may access and assign to the referenced variables; in this sense the function is "bound" to the variables.

For example, the adder function returns a closure. Each closure is bound to its own sum variable.
*/
func adder() func(int) int {
	sum := 0		// متغیری که از این تابع ساخته شود برای خودش یک متغیر سام مجزا دارد.
	return func(x int) int {
		sum += x
		return sum
	}
}
func main() {
	pos, neg := adder(), adder()
	for i := 0; i < 4; i++ {
		fmt.Println(
			pos(i),
			neg(-2*i),
		)
	}
	/*
		0 0
		1 -2
		3 -6
		6 -12
	*/
}

// ------------- Fibonacci function ---------------

// fibonacci is a function that returns
// a function that returns an int.
func fibonacci() func() int {
	f, g := 1, 0
	return func() int {		// A closure function
		f, g = g, f+g
		return f
	}
}
func main() {
	f := fibonacci()
	for i := 0; i < 6; i++ {
		fmt.Println(f())
	}
	/*
		0
		1
		1
		2
		3
		5
	*/
}
```

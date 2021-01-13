
## Overview



۱ - زبان گو دارای کلاس نیست ولی می‌توان متدهایی برای یک تایپ تعریف کرد. تعریف متد باید در همان پکیجی باشد که آن تایپ تعریف شده (یعنی برای تایپ‌های داخلی مثل اینتجر هم نمی‌شود در داخل پکیج‌های خودمان متد تعریف کنیم). تایپ موردنظر می‌تواند غیر استراکت هم باشد (با این کار می‌شود محدودیت گفته را برطرف کرد و مثلا برای تایپ فلوت64 خودمان یک تایپ تعریف کنیم و متد موردنظر را روی این تایپ جدید بسازیم)

 * برای تعریف تایپ فلوت64 خودمان ```type MyFloat float64‍‍‍‍‍```

- تایپ موردنظر را می‌توانیم از نوع اشاره‌گر بگیریم که در این‌صورت به جای اعمال تغییرات روی یک کپی از ورودی، خود مقداری که روی آن فراخوانی صورت گرفته تغییر می‌کند. ([مثالی برای تفاوت کاربرد تابع و متد با اشاره‌گر](#pointerreceiver))

- به دو دلیل بهتر است که از پوینتر رسیور به جای ولیو رسیور استفاده کنیم: دلیل اول اینکه با تغییر دادن آن مقدار اصلی هم تغییر می‌کند و دلیل دوم اینکه با هربار فراخوانی متد، کپی داده‌ها را نمی‌فرستد که این برای داده‌های بزرگ همچون استراکت‌های بزرگ بسیار مفید است. (بخواهیم نتیجه را بدون تغییر بگیریم آن را ریترن می‌کنیم.)

- اینترفیس یک تایپ است که به عنوان مجموعه‌ای از متد سیگنیچرها تعریف می‌شود. ([مثال اینترفیس](#interface)) 
 - یک اینترفیس خالی می‌تواند هر تایپی با هر مقداری را بگیرد. ([مثال اینترفیس خالی](#emptyinterface))
 - ```Type assertion``` مشخص می‌کند که یک اینترفیس یک تایپ خاص را بپذیرد. اگر تایپ مورد نظر اشتباه بود یک پنیک برمی‌گرداند. برای پیشگیری از پنیک می‌توانیم آن را به دو مقدار نسبت بدهیم که مقدار برابر ولیو آن (صفر در حالت خطا) و مقدار دوم یک بولین به معنی درست یا غلط بودن نوع تایپ است. ([مثال type assertion](#typeassertion))
 - ```Type switch``` یک ساختار است که امکان چندین اسسرشن تایپ سری‌شده را به ما می‌دهد (با این روش می‌توان تایپ‌های مختلف را به عنوان ورودی یک تابع داد). ([مثال Type switch](#typeswitch))

```go
switch v := i.(type) {
	case T:
		// here v has type T
	case S:
		// here v has type S
	default:
		// no match; here v has the same type as i
}
```

 - یکی از پرکاربردترین اینترفیس‌ها، استرینگر است که در پکیج اف‌ام‌تی تعریف شده‌است. از استرینگر یک تایپ است که به عنوان استرینگ معرفی شده و پکیج اف‌ام‌تی (و بسیاری دیگر) در هنگام پرینت خروجی به آن رجوع می‌کنند. ([مثال استرینگر](#stringer))

```go
type Stringer interface {
	String() string
}
```
 - یکی دیگر از پرکاربردترین اینترفیس‌ها، ارور است که در پکیج اف‌ام‌تی تعریف شده‌است. برای نمایش خطاها از آن استفاده می‌شودرجوع می‌کنند. ([مثال Error](#error))

```go
type error interface {
	Error() string
}
```
 - یکی دیگر از پرکاربردترین اینترفیس‌ها، ریدر است که در پکیج‌های مختلفی تعریف شده‌است و برای خواندن 
 
    files، network connections، (de)compressors، ciphers, image, JSON codecs 
 
    و غیره استفاده می‌شود. اینترفیس ریدر شامل متد رید است که بصورت زیر تعریف می‌شود: ([مثال Reader](#reader))

	```go
	func (T) Read(b []byte) (n int, err error)
	```
	- می‌توان رید را به گونه‌ای نوشت که خود نیز دوباره تابع را فراخوانی کند. (برای مثال وضعیتی را تصور کنید که رشته‌ای رمزگذاری‌شده را با رید می‌خوانیم و در خود متد رید دوباره متد رید فراخوانی شده و این بار کاراکترهای آن را رمزگشایی می‌کنیم. [مثال ریذ تودرتو](#read))

	- اینترفیس رایتر را هم داریم که برای نوشتن است. ```Writer``` 


۲ - پکیج ایمیج یک اینترفیس تصویر است.
 - ```color.Model``` و ```color.Color``` هرکدام یک اینترفیس هستند اما می‌توان به جای تعریف متد برای آنها از متدهای از پیش‌تعریف‌شده همچون ```color.RGBA``` و ```color.RGBAModel``` استفاده کرد.

```go
package image

type Image interface {
    ColorModel() color.Model 
    Bounds() Rectangle
    At(x, y int) color.Color
}
```

----

---
## pointerReceiver
([برگشت به بالا](#overview))

```go
package main
import "fmt"
type Vertex struct {
	X, Y float64
}
func (v *Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}
func ScaleFunc(v *Vertex, f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}
func main() {
	v := Vertex{3, 4}
	v.Scale(2)
	ScaleFunc(&v, 10)

	p := &Vertex{3, 4}
	p.Scale(2)
	ScaleFunc(p, 10)

	fmt.Println(v, p)
    // {60 80} &{60 80}
}
```

## Interface
([برگشت به بالا](#overview))

------
```go
package main
import "fmt"

type I interface {
	M()
}
type myInt int
func (ii *myInt) M(){
	if ii == nil {
		fmt.Println("<nil>")
		return
	}
	fmt.Println(ii)
}
func main() {
	var i I
	var m *myInt
	i = m
	describe(i)
	i.M()
    /*
        (<nil>, *main.myInt)
        <nil>
    */
}

func describe(i I) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```
------

## emptyInterface
([برگشت به بالا](#overview))

```go
package main
import "fmt"
func main() {
	var i interface{}
	describe(i)
    // (<nil>, <nil>)

	i = 42
	describe(i)
    // (42, int)

	i = "hello"
	describe(i)
    // (hello, string)
}
func describe(i interface{}) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```
------

## typeAssertion
([برگشت به بالا](#overview))

```go
package main
import "fmt"
func main() {
	var i interface{} = "hello"

	s := i.(string)
	fmt.Println(s)
	//hello

	s, ok := i.(string)
	fmt.Println(s, ok)
	//hello true

	f, ok := i.(float64)
	fmt.Println(f, ok)
	//0 false

	f = i.(float64) // panic
	fmt.Println(f)
	/*
		panic: interface conversion: interface {} is string, not float64

		goroutine 1 [running]:
		main.main()
			/tmp/compile2.go:17 +0x238
	*/
}
```
------

## typeSwitch
([برگشت به بالا](#overview))

```go
package main
import "fmt"
func do(i interface{}) {  // تایپ‌های مختلف ورودی را می‌توان با این روش به این تابع داد
	switch v := i.(type) {
	case int:
		fmt.Printf("Twice %v is %v\n", v, v*2)
	case string:
		fmt.Printf("%q is %v bytes long\n", v, len(v))
	default:
		fmt.Printf("I don't know about type %T!\n", v)
	}
}
func main() {
	do(21)
	//Twice 21 is 42
	do("hello")
	//"hello" is 5 bytes long
	do(true)
	//I don't know about type bool!
}
```
------

## Stringer
([برگشت به بالا](#overview))

```go
package main
import "fmt"
type IPAddr [4]byte

func (q IPAddr) String() string{
	// با تعریف این استرینگر، می‌توان خروجی را برای این تایپ، به گونه‌ای که ما دوست داریم پرینت کرد
	return fmt.Sprintf("%v.%v.%v.%v", q[0], q[1], q[2], q[3])
}

func main() {
	hosts := map[string]IPAddr{
		"loopback":  {127, 0, 0, 1},
		"googleDNS": {8, 8, 8, 8},
	}
	for name, ip := range hosts {
		fmt.Printf("%v: %v\n", name, ip)
	}
	// googleDNS: 8.8.8.8
	// loopback: 127.0.0.1
}
```
------

## Error
([برگشت به بالا](#overview))

```go
package main
import (
	"fmt"
	"math"
	"time"
)

type ErrNegativeSqrt struct { // در صورتی که مقدار منفی به تابع ما ارسال شود این تایپ را به عنوان خطا برمی‌گردانیم
	When time.Time
	number float64
}

func (e ErrNegativeSqrt) Error() string {
	return fmt.Sprintf("At %v, You send me a negative number: %f",
					   e.When, e.number)
}
func mySqrt(f float64) (float64, error) {
	if f < 0 {
		return 0, ErrNegativeSqrt{
						time.Now(),
						f,
					}
	}
	return math.Sqrt(f), nil
}

func main() {
	fmt.Println(mySqrt(16))
	//4 <nil>
	fmt.Println(mySqrt(-2))
	//0 At 2018-01-27 16:04:01.98479978 +0330 +0330 m=+0.000219700, You send me a negative number: -2.000000
}
```
------

## Reader
([برگشت به بالا](#overview))

```go
package main
import (
	"fmt"
	"io"
	"strings"
)
func main() {
	r := strings.NewReader("Hello, Reader!") // ریدر یک اینترفیس است که در پکیج استرینگز تعریف شده‌است
	b := make([]byte, 8)
	for {
		n, err := r.Read(b)		// با استفاده از متد رید از اینترفیس ریدر هشت بایت هشت بایت می‌خوانیم
		fmt.Printf("n = %v err = %v b = %v\n", n, err, b)
		fmt.Printf("b[:n] = %q\n", b[:n])
		if err == io.EOF {
			break
		}
	}
	/*
		n = 8 err = <nil> b = [72 101 108 108 111 44 32 82]
		b[:n] = "Hello, R"
		n = 6 err = <nil> b = [101 97 100 101 114 33 32 82]
		b[:n] = "eader!"
		n = 0 err = EOF b = [101 97 100 101 114 33 32 82]
		b[:n] = ""
	*/
}
```
------

## Read
([برگشت به بالا](#overview))

```go
package main
import (
	"io"
	"os"
	"strings"
)
func rot13(b byte) byte {	// رمزگذاری روت۱۳ که در آن حروف الفبا سیزده تا به جلو منتقل می‌شوند. تابع رمزگذاری و رمزگشایی آن یکسان است
	var a, z byte
	switch {
	case 'a' <= b && b <= 'z':
		a, z = 'a', 'z'
	case 'A' <= b && b <= 'Z':
		a, z = 'A', 'Z'
	default:
		return b
	}
	return (b-a+13)%(z-a+1) + a
}
type rot13Reader struct {
	r io.Reader
}
func (r rot13Reader) Read(p []byte) (n int, err error) {
	n, err = r.r.Read(p)	// یک رید دیگر در داخل متد رید فراخوانی می‌شود
	for i := 0; i < n; i++ {
		p[i] = rot13(p[i])
	}
	return
}
func main() {
	s := strings.NewReader(
		"Lbh penpxrq gur pbqr!")
	r := rot13Reader{s}
	io.Copy(os.Stdout, &r)
}
```
------

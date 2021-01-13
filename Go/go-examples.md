
## list

1- [My Age](#myage)

2- [Decoding deeply nested JSON data](#nestedstructs)

3- [iota and Enums](#iotaandenums)

---

## MyAge

```go
package main
import(
	"fmt"
	"time"
	"github.com/hako/durafmt"
)
func main() {
	p 	:= fmt.Printf
	pln := fmt.Println
	
	birthday, _ := time.Parse("Jan 2 2006", "May 16 1986") // 1st: layout, 2nd: given datetime
	
	// راه اول
    age := time.Since(birthday)                            // time.Duration
	p("Ashkan is %d days old\n", age/(time.Hour*24))
	// Ashkan is 11581 days old
	accurateAge := durafmt.Parse(age).String()
	pln(accurateAge)
	// 31 years 38 weeks 7 hours 46 minutes 21 seconds 238 milliseconds
	
	// راه دوم
	l := time.Now().Sub(birthday)
	pln(durafmt.Parse(l).String())
	// 31 years 38 weeks 7 hours 46 minutes 21 seconds 238 milliseconds
}
```

---

## NestedStructs
([Return to top](#list))

```go
{"data": {"children": [
  {"data": {
    "title": "The Go homepage",
    "url": "http://golang.org/"
  }},
  ...
]}}

// ----- You can write the above JSON like this: -----

type Item struct {
    Title string
    URL   string
}

type Response struct {
    Data struct {
        Children []struct {
            Data Item
        }
    }
}
```

---

## iotaAndEnums
([Return to top](#list))

* Go's ```iota``` identifier is used in ```const``` declarations to simplify definitions of incrementing numbers. Because it can be used in expressions, it provides a generality beyond that of simple enumerations.

```go
package main

import "fmt"

type ByteSize float64
const (
	_           = iota // ignore first value by assigning to blank identifier
	KB ByteSize = 1 << (10 * iota)
	MB
	GB
	TB
	PB
	EB
	ZB
	YB
)

type Weekday int
const (
	Sunday Weekday = iota + 1
	Monday
	Tuesday
	Wednesday
	Thursday
	Friday
	Saturday
)

func main() {
	fmt.Println("KB: ", KB)
	fmt.Println("MB: ", MB)
	fmt.Println("GB: ", GB)
	fmt.Println("TB: ", TB)
	fmt.Println("PB: ", PB)
	// KB:  1024
	// MB:  1.048576e+06
	// GB:  1.073741824e+09
	// TB:  1.099511627776e+12
	// PB:  1.125899906842624e+15
	
	fmt.Println("Sunday: ", Sunday)
	fmt.Println("Monday: ", Monday)
	fmt.Println("Tuesday: ", Tuesday)
	fmt.Println("Friday: ", Friday)
	fmt.Println("Saturday: ", Saturday)
	// Sunday:  1
	// Monday:  2
	// Tuesday:  3
	// Friday:  6
	// Saturday:  7
}
```

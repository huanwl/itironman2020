大家好，今天是鐵人賽第三天，昨天介紹了開發環境和第一支Go程式，而今天我要來講Go的基本程式結構，以及變數的用法。



## 程式結構

我們先來回顧一下昨天的第一支程式hello.go:

``` go
package main

import "fmt"

func main() {
	fmt.Printf("hello, world\n")
}
```

有幾個重點以下分別說明:

1. 所有的go程式是由package組成的，package這個字通常在台灣翻譯成套件，在大陸翻譯為包，我個人是覺得包比較好理解，而且只有一個字。
2. go的主程式必須是由名為main的包裡面的main方法(func)作為進入點。
3. package關鍵字表示這支程式是屬於哪個包，一個包可以有多支程式檔案，但通常是在同一個目錄下。
4. import關鍵字表示匯入其他的包，可以透過引入的包名呼叫其他包的函式。



一個包裡面可以匯入多個包，如下:

``` go 
package main

import (
	"fmt"
	"math"
)
// 也可以分開寫
// import "fmt"
// import "math"

func main() {
	fmt.Printf("%f\n", math.Pow(3, 2))
}
```



其他比較詳細的package用法會在之後說明，目前談到的基本語法都可以在這個package main結構下實現。



## 變數宣告

go變數宣告使用var關鍵字，基本的宣告方式如下:

``` go
// 宣告一個名叫a的整數變數
var a int

// 宣告一個名叫b的字串變數
var b string

// 宣告一個名叫c的浮點數陣列
var c []float32

// 宣告一個名叫d的方法變數並且回傳bool型別
var d func() bool
```

除了整數、字串、浮點數這些常見的基本資料型別之外，go也支援函式變數，可以把方法當成變數或參數在程式之中傳遞。其他還有比較特殊的型別，像是結構、介面、通道等，會在之後談到。

另外也有多重寫法，用一個var關鍵字宣告多個變數:

``` go
// 若型別相同，可以這樣寫
var a, b, c int

// 若型別不同，可以這樣寫
var (
	a int
	b string
	c []float32
	d func() bool
)
```

雖然說go風格很像c，但也是簡化了很多c的毛病，像是我以前很討厭c的變數宣告一定要放在函式開頭的規定，而go就可以這樣寫:

``` go
func main() {
	var a int
	fmt.Printf("%d \n", a) // int 預設值為 0
	var b string
	fmt.Println(b) // string 預設值為空字串
}
```

變數具有初始值，這點倒是和c一樣。

不過go有另一點很有趣，我們修改一下上面的程式碼，如下所示:

``` go
func main() {
	var a int
	var b string
	fmt.Println(b) 
}
```

會出現以下錯誤訊息:

``` bash
$  go run ./main.go
# command-line-arguments
./hello.go:10:6: a declared and not used
```

什麼！宣告的變數沒有用也會編譯錯誤，在其他語言通常是IDE會印出警告，但也不會編譯錯誤，可見go語言也是一個很嚴謹的編譯語言。



## 初始化變數

go變數初始化與大部分的程式語言相同，可以在宣告式右邊加上 = 與初始值:

``` go
var year int = 2020
var name string = "Iron man"
```

由於初始值100本身就是整數，所以在宣告變數時可以省略int型別，這是因為go變數有支援型別推導的功能，和C#的var一樣，從右側的初始值或是一個方法呼叫的回傳值推導出變數型別:

``` go
var year = 2020   // int 型別
var name = "Iron man"   // string 型別

// 多重寫法
var price, name = 100, "Iron man"
```

另外，go變數還有一個更精簡的寫法，可以同時宣告與初始化，稱為短變數宣告(short variable declarations)，使用 := 運算子，不是賦值時用的 = 運算子。

``` go
year := 2020
name := "Iron man"

// 多重寫法
price, name := 100, "Iron man"
```

還有要注意一件事，短變數宣告只能用在函式內，因為函式外的程式必須以關鍵字開頭(package, var, func...)，因此在函式外宣告變數仍然要用var。



## 賦值(assignment)

go變數賦值方式與大部份程式語言相同，使用 = 運算子:

``` go
var year int
var name string

year = 2020
name = "Iron man"

// 多重寫法
price, name = 100, "Iron man"
```

多重賦值寫法對於變數交換(swap)有很大的幫助:

``` go 
var a, b = 1, 2

// 傳統swap寫法
var temp int
temp = a
a = b
b = temp

// 多重賦值swap寫法
a, b = b, a
```

程式碼從四行降到一行，而且不用宣告temp，多重賦值實在太威了。



## 匿名佔位符(anonymous placeholder) 

placeholder這個字好難翻譯，大致上的意思就是用一個符號( _ )佔據原本放變數的位置，目的就是想要忽略等號右側的賦值。

``` go
// 單一賦值雖然不會編譯錯誤，但這一行是沒有意義的
_ = "Iron man"

// 多重賦值下才會有意義，可以忽略不想要的值
var name string
name, _ = "Iron man", "Dr.strange"
```

通常在等號的右側是方法呼叫時，匿名佔位符會更常被用到，我們可以只接受部份回傳值。

``` go
// go方法可以有多個回傳值
func getHeroName() (string, string) {
	return "Iron man", "Dr.strange"
}

func main() {
	var name, _ = getHeroName()
	fmt.Println("I like " + name)
}
```


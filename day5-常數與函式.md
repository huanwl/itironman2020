大家好，今天是鐵人賽第五天，要來介紹常數與函式。這兩個東西看起來沒什麼關聯，不過它們都和前兩天的內容有關。常數就是一個相對於變數的東西，它不會改變，也不能被改變；而函式在go語言裡也是一種型別，可以宣告函式變數，並能在程式之中相互傳遞。

# 常數(constant)

常數是指在開發或編譯期間就定義好的值，在執行期間不能被修改。

## 常數宣告

go常數的用法就和變數差不多，只是把關鍵字 *var* 換 *const* 。

```go
// 單一宣告
const pi = 3.141592
const e = 2.718281

// 多重宣告 
const (
    pi = 3.141592
	e = 2.718281
)
```

常數可以用表達式指定內容，以及在陣列宣告時指定大小:

```go
const pageSize = 5
const totalPage = 20
const totalSize = pageSize * totalPage

var arr [totalSize]int

fmt.Println(len(arr))
//印出: 100
```

## 列舉常數

go語言中沒有定義列舉(enum)的功能，但是可以用常數和iota關鍵字達到相似的效果。

```go
// 先定義一個int別名的型別Hero
type Hero int

const (
    IronMan Hero = iota
    DrStrange
    Thor
    Hulk
)

// 使用列舉常數賦值
man := IronMan

fmt.Println(IronMan, DrStrange, Thor, Hulk, man)
// 印出: 0 1 2 3 1
```

iota關鍵字是一個常數計數器，第一個常數從 0 開始，往下逐漸的累加。而iota也可以加入表達式，不一定都要從0開始，或是每次只加一，如下所示:

```go
type Hero int

const (
    IronMan Hero = iota*2 + 1
    DrStrange
    Thor
    Hulk
)

fmt.Println(IronMan, DrStrange, Thor, Hulk)
// 印出: 1 3 5 7
```



# 函式(function)

函式(也稱作函數)是將一行或多行程式碼組合在一起，提供給其他函式呼叫，讓程式碼可以被重複使用。

## 函式宣告

一般來說，函式具有4個部份，分為函式名稱、參數、回傳值，以及主體，go語言的函式宣告以func關鍵字起始，範例如下:

``` go
// 宣告一個名叫foo的函式，接受名叫a的整數和名叫b的字串，並回傳一個字串
func foo(a int, b string) string {
	return fmt.Sprintf("%d %s", a, b)
}

func main() {
	s := foo(2020, "Iron Man")
	fmt.Println(s)
    // 印出: 2020 Iron Man
}
```

從上面的範例可以看到，作為程式進入點的main也是一個函式，我們稱作為主函式，它通常是在程式啟動時被runtime(執行環境)呼叫。而當程式進入主函式之後，我們呼叫了foo函式，取得一段回傳的字串，然後印出。

go函式的回傳值可宣告多個，但要用小括號( )包起來，在函式呼叫的左側就必須放上相同數量的變數:

``` go
func foo(a int, b string) (string, int) {
	return b + "2", a + 2
}

func main() {
	b, a := foo(2020, "Iron Man")
	fmt.Printf("%d %s\n", a, b)
    // 印出: 2022 Iron Man2
}
```

不想全部都要的話，也是可以忽略掉部份回傳值，用底線( _ )替代變數:

``` go
_, a := foo(2020, "Iron Man")
```

當有多個參數，而且是相同型別的話，可以簡化參數寫法:

``` go 
// 原寫法: func add(a int, b int) int { ... }
func add(a, b int) int {
	return a + b
}

func main() {
	c := add(1, 2)
	fmt.Printf("%d\n", c)
    // 印出: 3
}
```

另外，go函式還有一個特色，就是回傳值可以命名，被命名的回傳值就等同於宣告一個變數，在函式結束時用return可以自動回傳變數裡的內容:

``` go
func add(a, b int) (c int) {
	c = a + b
	return
	// 或 return c
}

func addAndMinus(a, b int) (c int, d int) {
	c = a + b
	d = a - b
	return
	// 或 return c, d
}

func main() {
	a := add(1, 2)
	b, c := addAndMinus(1, 2)
	fmt.Printf("%d %d %d\n", a, b, c)
    // 印出:  3 3 -1
}
```

在命名回傳值的模式下，可以選擇是否在return後指定回傳內容，不指定就會取出被命名的回傳變數內容，如果在函式中變數沒被賦值，那就會是預設值。

## 函式變數

go函式屬於一級公民(first class)，意思就是說函式也是一種資料型別，可以被當作值相互傳遞。以下舉個例子來說明:

``` go
func add(a, b int) int {
	return a + b
}

func minus(a, b int) int {
	return a - b
}

func addAndMinus(a, b int) (int, int) {
	return a + b, a - b
}

func main() {
	var foo func(a, b int) int

	foo = add
	foo = minus
	// foo = addAndMinus 這一行會錯誤

	a := foo(1, 2)
	fmt.Printf("%d\n", a)
	// 印出: 3
}
```

看到上面範例宣告一個叫做foo的函式變數，而函式型別之間是用參數與回傳值的不同來做區分。因此，我們可以看到foo是一個有2個整數參數和一個整數回傳值的函式變數，只要是符合這樣條件的函式都可以被賦值給它，像是上面的add和minus，而若是賦值addAndMinus就會錯誤。

當foo函式變數被賦值後，就可以像一般函式那樣直接呼叫它，ex: foo(1, 2)。但是如果沒有賦值，函式變數預設值為nil，呼叫它會發生錯誤:

``` bash
panic: runtime error: invalid memory address or nil pointer dereference
[signal SIGSEGV: segmentation violation code=0x1 addr=0x0 pc=0x48e554]
```

這個錯誤簡單來說，就是使用的記憶體位置(變數)沒有定義預期的資料。

## 匿名函式

有時候我們會覺得定義函式名稱是一件麻煩的事，特別是在臨時要宣告一個函式的時候。我們可以直接定義一個匿名函式，然後丟給一個函式變數，或是一個接受函式變數的函式。

``` go
// foo函式接收2個整數和一個函式變數
func foo(a, b int, f func(a, b int) int) int {
	return f(a, b)
}

func main() {
	var add = func(a, b int) int {
		return a + b
	}

	a := foo(1, 2, add)
	fmt.Printf("%d\n", a)
	// 印出: 3
}
```



# 小結

今天大致上說明了常數與函式的基本用法，go的函式是一種型別，可以當作變數使用，因此可以支援函式程式設計(functional programing)。不過函式其實還有更多的用法，像是錯誤處理這一部份，我打算在之後找一天來特別說明。


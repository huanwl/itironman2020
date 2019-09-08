大家好，今天是鐵人賽第四天，要講的東西是延續昨天的變數，關於go語言的資料型別，以及常數的用法。



## 基本型別







## 型別初始值







## 型別的別名(Type Alias)

go可以對任何一個型別定義別名，而別名就像是一個外號，本質上還是同一型別。別名只存在於編譯期間，進入執行期間就會回到原始型別。

格式:  type  [別名]  =  [型別] 

``` go
// 定義一個 int 的別名叫做 myInt
type  myInt = int
```

而go語言本身也有定義的型別別名:

``` go
type byte = uint8
type rune = int32
```

從上面可以看到其實byte是uint8、rune是int32，通過定義別名方式獲得新的型別，這樣可以用於一些特別的需求，提高程式碼可讀性以及型別安全。

我們可以用格式化輸出型別(%T)來看看別名的原始型別:

``` go
type IntAlias = int

var a byte
fmt.Printf("%T \n", a)

var b rune
fmt.Printf("%T \n", b)

var c IntAlias
fmt.Printf("%T \n", c)
```

上面程式碼會印出:

``` bash
uint8 
int32 
int 
```

因此，我們可以看到在程式的執行期間，別名就會被改成原始型別。

另外要注意的是，千萬不要少加一個等號，意義完全不同，會變成定義一個新的型別:

``` go
type NewInt int

var a NewInt

fmt.Printf("%T \n", a)
// 印出: main.NewInt 
```

因為type關鍵字本身就是用於定義型別，像是結構、介面也是要用type定義，別名的語法必須加上等號( = )來區分這是在定義別名，而不是新的型別。



## 常數宣告

常數是指在開發或編譯期間就定義好的值，在執行期間不能被修改。go常數的用法就和變數差不多，只是把關鍵字 *var* 換 *const* 。

格式:  const  [常數名稱]  =  [常數值] 

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

``` go
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

``` go
type Hero int

const (
    IronMan Hero = iota + 1
    DrStrange
    Thor
    Hulk
)

fmt.Println(IronMan, DrStrange, Thor, Hulk)
// 印出: 1 2 3 4
```



## 小結




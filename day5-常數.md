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
    IronMan Hero = iota + 1
    DrStrange
    Thor
    Hulk
)

fmt.Println(IronMan, DrStrange, Thor, Hulk)
// 印出: 1 2 3 4
```



## 
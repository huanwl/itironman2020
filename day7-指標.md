大家好，今天是鐵人賽第七天，終於要完成一週了，因為比賽的關係，最近每天都晚睡，而且常常最後幾分鐘才寫完發文，所以今天也是特別趕。

今天我要來講一個比較屬於觀念的東西，那就是指標。Go語言保留了C語言的指標，因此如果要在程式裡，想要動態配置記憶體的話，都要使用指標來存放位置。



# 指標

指標(Pointer)主要是用來操作記憶體空間，可以透過記憶體位址直接存取資料，取代資料搬移和複製的動作，使程式擁有較高的執行效能。

## 指標變數

指標通常指的是，一個指向記憶體位址(memory address)的變數，而這也意味著指標是一種型別，就和函式變數一樣，可以當作變數使用的都是一種型別，因此我們可以宣告一個指標變數，如下:

``` go
var ptr *string  // 宣告一個指向字串的指標變數ptr

fmt.Printf("%p \n", ptr)  // 印出: 0x0 (尚未指派記憶體位置)

if ptr == nil {
	fmt.Println("ptr is nil")
}
```

指標變數的初始值一律是nil，表示尚未指派記憶體位址，指標可以透過格式化輸出(%p)印出內容。

``` go
str := "Iron Man"  // 宣告一個字串變數str

var ptr *string  // 宣告一個指向字串的指標變數ptr

ptr = &str  //取出str的記憶體位址給ptr

fmt.Printf("%p \n", ptr)   // 印出: 0xc000044740 (str變數記憶體位置，以16進位表示)

fmt.Printf("%s \n", *ptr)  // 印出: Iron Man
```

上面範例中，在完成宣告變數後，透過取址符號 `&` 取出字串str的記憶體位址，然後賦值給指標ptr。再來透過格式化輸出(%p)印出指標內容，也就是16進位的記憶體位址。如果想要取出記憶體位址中的實際內容，就必須使用取值符號 `*` 來取出指標指向的變數內容。  

而指標變數的型別是以指向的型別來做區分:

``` go
str := "Iron Man" // 宣告一個字串變數str

var ptr *int // 宣告一個指向整數的指標變數ptr

ptr = &str //取出str的記憶體位址給ptr
```

這樣寫就會出錯:

``` bash
$  go run ./main.go 
# command-line-arguments
./main.go:14:6: cannot use &str (type *string) as type *int in assignment
```

最後，因為考量安全問題，go語言不支援指標運算，詳細說法可以參考go官網的FAQ:

[Why is there no pointer arithmetic?](https://golang.org/doc/faq#no_pointer_arithmetic)。



## 指標與函式

依照前幾天講的變數與函式，我們可以設計一個變數交換的函式:

``` go
func swap(a, b int) (int, int) {
	temp := a
	a = b
	b = temp
	return a, b
}
```

傳入兩個整數後，做完交換就回傳給呼叫者，看似很合理。但如果我們的函式不想有要回傳值的話，有辦法做的到嗎？又該如何把結果交給呼叫者？

這時候我們就可以使用指標的概念來設計這個函式:

``` go
func swap(a, b *int) {
	temp := *a
	*a = *b
	*b = temp
}

func main() {
	a, b := 1, 2
	swap(&a, &b)
	println(a, b)
}
// 執行結果: 2 1
```

上面範例用指標改寫了swap函式，取值符號 * 可以讀取指標資料，也可以修改指標資料。這樣一來，我們不必回傳值，就可以把函式中的計算結果交給呼叫者。



## new( )

Go語言提供了一種方法，可以動態產生一個變數空間，如下:

``` go
ptr := new(string)  // 動態配置一個字串型別的的變數

*ptr = "Iron Man"

fmt.Printf("%s \n", *ptr)  // 印出: Iron Man
```

這結果和原本的範例一樣，只差在有沒有先宣告str變數。new()這種方式稱作動態配置記憶體，而原本我們常用的 `var` 和 `:=` 宣告變數就稱作靜態配置記憶體。動態配置適合用於操作大量且不固定的記憶體空間。



# 小結

今天簡單說明了指標的概念，以及go語言的指標用法。從我離開學校後就再也碰過指標了，現在要開始回想以前學過的東西。明天預計會說明陣列的部份，因為陣列其實也是一種指標，比較特別的是它指向一段連續的記憶體空間。



# 參考

1. https://stackoverflow.com/questions/32700999/pointer-arithmetic-in-go
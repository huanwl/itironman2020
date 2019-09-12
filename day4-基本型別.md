大家好，今天是鐵人賽第四天，我要來講go語言的基本型別。由於go是一個強型別的語言，因此了解型別是一件很重要的事。go型別大致上可以區分為基本型別、指標型別，以及其他特殊型別，像是切片、結構、通道..等等。今天主要是說明基本型別的部份，其餘的型別會在未來的天數中一一談到。



# 基本型別(basic types)

基本型別是用於存放最基礎的數據資料，像是一個整數、一段文字，而go語言的基本型別可以分為以下四種:

- 整數 (int)
- 浮點數
- 布林 (boolean)
- 字串 (string)

## 整數

用來表達沒有小數的數字，初始值為0，可分為兩大類:

- 有符號: int8, int16, int32, int64
- 無符號: uint8, uint16, uint32, uint64

也可以使用int, uint，這兩個型別會依據平台自動對應上面的其中一個，例如在32位元系統和64位元系統上使用int的長度就會不同。

在C語言有sizeof可以取得變數佔用的記憶體大小，而在go語言也可以透過unsafe.Sizeof()取得記憶體大小，我們來看看以下範例:

``` go 
// 將 1 左移 7 位元再 - 1 (不 - 1 會錯誤)
var a int8 = 1<<7 - 1
fmt.Printf("int8 長度= %d byte, 最大值= %d\n", unsafe.Sizeof(a), a)

// 將 1 左移 8 位元再 - 1 (不 - 1 會錯誤)
var b uint8 = 1<<8 - 1
fmt.Printf("uint8 長度= %d byte, 最大值= %d\n", unsafe.Sizeof(b), b)
```

印出:

``` bash
int8 長度= 1 byte, 最大值= 127
uint8 長度= 1 byte, 最大值= 255
```

通常一般作業系統都是1byte=8bits，因此int8和uint8都是佔1byte大小，差別是在於int8可接受負整數，並用最左邊的位元來代表正負號，所以說int8的最大值會比uint8小(因為少一個bit)。

整數的最大最小值在標準庫的math包中有提供常數值，像是*math.MinUint8*或*math.MaxUint8*，可以參考[math包官方文件](https://golang.org/pkg/math/#pkg-constants)。

## 浮點數

用來表達帶有小數的數字，初始值為0。

go提供兩種浮點數型別: float32, float64，與int32和int64一樣是用4 bytes和8 bytes大小儲存，但浮點數卻可以表示一個非常大或非常小的數。因為浮點數都是採用IEEE 754標準，以科學記數法來儲存數值，但是這樣就會有精準度的問題。

浮點數的儲存方式非常複雜，有興趣可以看這篇[浮點數的記憶體儲存](https://www.itread01.com/content/1543589824.html)，我只看懂了一半，真的是以前大學計概課不用功的下場...

一樣來舉個簡單的範例，用unsafe.Sizeof()看看佔用的記憶體大小:

``` go
var a float32 = math.MaxFloat32
fmt.Printf("float32 長度= %d byte\n最大值(科學記號法)= %e\n最大值(十進制)= %f\n\n",
					 unsafe.Sizeof(a), a, a)

var b float64 = math.MaxFloat64
fmt.Printf("float64 長度= %d byte\n最大值(科學記號法)= %e\n最大值(十進制)= %f\n\n",
			 		  unsafe.Sizeof(b), b, b)
```

印出:

``` bash
float32 長度= 4 byte
最大值(科學記號法)= 3.402823e+38
最大值(十進制)= 340282346638528859811704183484516925440.000000

float64 長度= 8 byte
最大值(科學記號法)= 1.797693e+308
最大值(十進制)= 179769313486231570814527423731704356798070567525844996598917476803157260780028538760589558632766878171540458953514382464234321326889464182768467546703537516986049910576551282076245490090389328944075868508455133942304583236903222948165808559332123348274797826204144723168738177180919299881250404026184124858368.000000
```

這次的範例直接拿math包的float最大值來用，分別印出長度與大小值，而浮點數值除了使用一般十進制(%f)印出，也可以用科學記數法(%e)印出。

另外，go語言還有提供兩個複數型別complex64, complex128，但是複數在應用上是滿少見的，

## 布林

用來表達真假值true/false，初始值為false，使用bool來宣告:

``` go
var a bool
fmt.Println(a)
// 印出: false
```

可以透過邏輯運算子(&&, ||, !)計算多個布林值:

``` go
a := false
b := true
fmt.Println(a || b) 
// 印出: true
```

## 字串

用來表達一段文字，初始值為""(空字串)，以string來宣告。而go語言的字串內部是用UTF-8實作，因此可以直接輸入中文字:

``` go
s := "IronMan"
ch := "鋼鐵人"
fmt.Println(s, ch)
// 印出: IronMan 鋼鐵人
```

用反引號(`)定義多行文字:

``` go
s := `IronMan,
	his name is Tony Stark,
	and he is my favorite hero.
	`
fmt.Println(s)
```

印出:

``` go
IronMan,
	his name is Tony Stark,
	and he is my favorite hero.
```

因為go語言會把 ` 符號之間的所有符號(包含空白or換行)都當成字串內容，所以上面印出結果才會沒有對齊。

比較特別的是，go語言沒有字元的基本型別，反而有字串型別。go語言的字元是用整數來表達，雖然C語言的字元也可以轉換成整數(編碼)，但仍然有原生的字元型別。以下舉個範例，我們可以用格式化輸出(%T)變數的型別:

``` go
var ascii byte = 'I'
fmt.Printf("%d %T \n", ascii, ascii)

var utf8 rune = '鋼'
fmt.Printf("%d %T \n", utf8, utf8)
```

印出:

``` go 
73 uint8 
37628 int32
```

從上面的程式碼與輸出結果可以得知，go字元可以用byte或rune表達，視字元的編碼長度而定。然後，我們會發現byte和rune都是整數型別的一種。



# 型別的轉換

不同型別的資料可以相互轉換，但有些時候會錯誤，或是損失精準度，go語言的轉型方式比較特別:

格式:  [型別] ( [表達式] )

``` go
// 宣告一個16位元整數，轉成8位元時發生截斷，因為8位元整數最大值是127
var a int16 = 128
fmt.Printf("%d \n", int8(a))

// 將浮點數轉成整數，會損失小數點後的精度
var b float32 = math.Pi
fmt.Printf("%d \n", int(b))
```

印出:

``` go 
-128 
3 
```



# 型別的別名(Type Alias)

go可以對任何一個型別定義別名，而別名就像是一個外號，本質上還是同一型別。別名只存在於編譯期間，進入執行期間就會回到原始型別。

格式:  type  [別名]  =  [型別] 

``` go
// 定義一個 int 的別名叫做 myInt
type  myInt = int
```

而go語言本身也有內建的型別別名:

``` go
type byte = uint8
type rune = int32
```

從上面可以看到其實byte是uint8、rune是int32，通過定義別名方式獲得新的型別，這樣可以用於一些特別的需求，提高程式碼可讀性以及型別安全。

用格式化輸出(%T)型別，來看看別名的原始型別:

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



# 小結

今天這篇寫了很久...差點趕不出來，型別真是一件很瑣碎的事，難怪很多人喜歡動態語言XD，個人覺得go的型別系統算是比較難，因為這些型別之後還會牽扯到指標，其實就很像C語言。但讓我覺得很意外的是，C語言有字元沒有字串，go語言則是有字串沒有字元。而在Java, C#這些高階語言中，通常是用類別封裝的方式提供字串型別，能像go一樣提供原生字串型別的語言是比較少見。



# 參考來源

1. https://michaelchen.tech/golang-programming/data-type/
2. http://golang-zhtw.netdpi.net/03_types/03-01_numbers
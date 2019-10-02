大家好，今天是鐵人賽第二十七天，我們來談談Go語言的反射。雖然說使用反射會降低效能，但也要是很大量或頻繁的使用才會有感。如果要製作一個靈活的套件或是底層模組，反射絕對是不可缺少的。



# 反射(reflect)

反射是指在程式執行期間獲取實體的型別資訊，在多型的環境下就能用反射來識別實體型別，以及存取資料和方法。

Go語言的反射定義在 `reflect` 套件：

```go
import "reflect"
```

我們可以用 `TypeOf( T )` 函式獲得一個實體的型別對象，如下：

```go
package main

import (
	"fmt"
	"reflect"
)

// 自訂型別
type myInt int
type myIntSlice []int

func main() {
	var a int
	var b myInt
	var c myIntSlice

	typeOfA := reflect.TypeOf(a)
	typeOfB := reflect.TypeOf(b)
	typeOfC := reflect.TypeOf(c)

	fmt.Println("N:", typeOfA.Name(), "K:", typeOfA.Kind())
	fmt.Println("N:", typeOfB.Name(), "K:", typeOfB.Kind())
	fmt.Println("N:", typeOfC.Name(), "K:", typeOfC.Kind())
}

/* 執行結果：
N: int K: int
N: myInt K: int
N: myIntSlice K: slice
*/
```

從上面的範例可以發現，Go語言可以自訂型別，反射獲取的型別資訊可以找出 `Name` 和 `Kind`，`Name` 是自訂型別名稱，然而 `Kind` 是不會改變的型別種類。



## 種類(Kind)

相較 `Type` 而言，`Kind` 是固定的而且不能自訂新增，所以在 `reflect` 套件中是以[列舉常數](https://ithelp.ithome.com.tw/articles/10214694)來定義，如下：(https://golang.org/pkg/reflect/#Kind)

```go
type Kind uint

const (
	Invalid Kind = iota
	Bool
	Int
	Int8
	Int16
	Int32
	Int64
	Uint
	Uint8
	Uint16
	Uint32
	Uint64
	Uintptr
	Float32
	Float64
	Complex64
	Complex128
	Array
	Chan
	Func
	Interface
	Map
	Ptr
	Slice
	String
	Struct
	UnsafePointer
)
```

定義一個結構體試試看：

```go
type Hero struct {
	Name string
	Age  int
}

func main() {
	var tony = Hero{Name: "Iron Man", Age: 30}
	var tonyPtr = &tony

	typeOfTony := reflect.TypeOf(tony)
	typeOfTonyPtr := reflect.TypeOf(tonyPtr)

	fmt.Println("N:", typeOfTony.Name(), "K:", typeOfTony.Kind())
	fmt.Println("N:", typeOfTonyPtr.Name(), "K:", typeOfTonyPtr.Kind())
}

/* 執行結果：
N: Hero K: struct
N:  K: ptr
*/
```

要注意的是，任何型別的指標反射出來的種類都是 `ptr`，而且型別名稱都是空字串。

雖然指標反射的結果都一樣，但是指向的型別就不同，我們可以用 `Elem()` 方法獲取指標指向的型別對象：

```go
var tonyPtr = &Hero{Name: "Iron Man", Age: 30}
typeOfTonyPtr := reflect.TypeOf(tonyPtr)
typeOfTony := typeOfTonyPtr.Elem()
```

反射指標就像是一個包裹，包裹內的東西才是我們想要的。



## 反射結構體

結構體可以反射獲取其欄位和方法，如下：

```go
type Hero struct {
	Name string
	Age  int
}

func main() {
	var tony = Hero{Name: "Iron Man", Age: 30}

	typeOfTony := reflect.TypeOf(tony)

	for i := 0; i < typeOfTony.NumField(); i++ {
		fmt.Printf("%+v\n", typeOfTony.Field(i))
	}
}

/* 執行結果：
{Name:Name PkgPath: Type:string Tag: Offset:0 Index:[0] Anonymous:false}
{Name:Age PkgPath: Type:int Tag: Offset:16 Index:[1] Anonymous:false}
*/
```

反射欄位是 `StructField` 型別，可以參考這裡 [StructField](https://golang.org/pkg/reflect/#StructField)。 

在 `StructField` 中有一個欄位叫做 `StructTag`，這個有點像是結構的 meta data，可以在定義結構時一同定義，並且可以在反射時取出：

```go
type Hero struct {
	Name string `required: true`   // 必填欄位
	Age  int    `range: "0~100"`        // 範圍 0 到 100
}

func main() {
	var tony = Hero{Name: "Iron Man", Age: 30}

	typeOfTony := reflect.TypeOf(tony)

	for i := 0; i < typeOfTony.NumField(); i++ {
		fmt.Printf("%v %v\n", typeOfTony.Field(i).Name, typeOfTony.Field(i).Tag)
	}
}

/* 執行結果：
Name required: true
Age range: "0~100"
*/
```

Go語言的 `StructTag ` 就有點像是 C# 的 `Attribute` 。




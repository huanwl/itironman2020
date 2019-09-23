大家好，今天是鐵人賽第十八天，我要來介紹go語言的例外處理機制。當執行程式時，遇到作業系統或網路異常，我們可能會拋出例外讓程式中斷；而這時候，如果是網頁就會回應伺服器錯誤，如果是App就會閃退，如果是桌面程式就很可能會造成當機。這樣做雖然很可怕，因為使用者一定會不知所措，但有時候卻是必須的，像是「執行交易程式時，資料庫剛好斷線」。



# 例外處理(exception handling)

例外處理是指當程式發生意外事件時的處理程序，也可以稱為錯誤處理，因為在 Java/C# 中，是用 ` Exception` ，go語言則是用 `Error` 。

很多語言都有良好的例外處理機制，像是 Java/C# 語言就有提供 `try-catch-finally`；go語言雖然沒有這麼方便的用法，但也是有其他語法可以做到相似的效果，分別是 `defer` 、 `panic` 、 `recover` 。



## Go語言的錯誤語法

相較於 Java/C# 的  `try-catch-finally` ，屬於區塊型錯誤處理，可以包覆任一行可能出錯的程式；Go語言的錯誤處理則是屬於函式型，在程式設計上有以下特點:

- 一個可能造成錯誤的函式，必須回傳一個錯誤型別。如果函式呼叫成功，就回傳 nil，反之回傳錯誤。
- 在函式呼叫後，需要檢查錯誤，如果不是 nil，就必須進行錯誤處理。

範例如下:

```go
// 定義一個除法函式
func div(dividend, divisor int) (int, error) {
    // 當除數為 0 時回傳錯誤
	if divisor == 0 {
		return 0, errors.New("Can't divided by zero")
	}
    // 執行成功時，錯誤為nil
	return dividend / divisor, nil
}

func main() {
    // 呼叫函式同時回傳結果與錯誤
	value, error := div(1, 0)
    // 檢查錯誤
	if error == nil {
		fmt.Println(value)
	} else {
		fmt.Println(error)
	}
}
```



## 自訂錯誤

在物件導向的世界裡，通常也會把錯誤視為一個物件。在go語言中，最基本的錯誤是一個介面，只要實作錯誤介面就算是一個自訂的錯誤結構型別。go語言內建的錯誤介面如下:

```go
type error interface {
    Error() string
}
```

實作錯誤介面非常簡單，只要實作方法回傳錯誤訊息即可。

go語言也有提供基本的錯誤結構型別，定義在 `errors` 套件裡，並且有提供 `New` 方法讓我們快速建立一個錯誤實體，範例如下:

```go
package main

// 需要匯入 errors 套件
import (
	"errors"
	"fmt"
)

// 定義一個英雄結構型別
type Hero struct {
	name string
	age  int
}

func main() {
    // 宣告一個英雄型別變數
	var tony *Hero

    // 傳入未初始化的變數(nil)
	name, error := getHeroName(tony)
    
    // 檢查錯誤
	if error == nil {
		fmt.Println(name)
	} else {
		fmt.Println(error.Error())
	}
}

// 定義一個取出名稱的方法，當傳入 nil 時會回傳錯誤
func getHeroName(h *Hero) (string, error) {
	if h == nil {
		return "", errors.New("Nil Argument")
	}
	return h.name, nil
}
/* 執行結果：
Nil Argument
*/
```



# 小結

今天先簡單介紹go語言的錯誤語法，明天再來講 `defer` 、 `panic` 、 `recover` 的用法。


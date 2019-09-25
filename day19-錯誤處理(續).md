大家好，今天鐵人賽第十九天。昨天簡單說明了如何自訂錯誤，今天來講錯誤處理的相關語法，包含 `defer` 、 `panic` 、  `recover`。



## defer

defer 是延遲執行的關鍵字，可以將其後面的程式碼延後至函式結束時執行。

```go
func main() {
	fmt.Println("defer begin")

	defer fmt.Println(1)
	defer fmt.Println(2)
	defer fmt.Println(3)

	fmt.Println("defer end")
}

/* 執行結果：
defer begin
defer end
3
2
1
*/
```

多個 defer 的程式碼的執行順序是相反的，最先被 defer 的程式碼會在最後被執行。



## panic

使用 panic 會直接造成系統崩潰、服務中斷，就像是 Java/C# 中的 `throw exception` ，如下：

```go 
func main() {
	panic("crash")
	fmt.Println("hello,world")
}
```

執行後就會產生錯誤訊息，而且不會執行後面的程式碼：

```bash
panic: crash
```

但是如果在 panic 之前就 defer 的程式碼，會在崩潰前執行：

```go
func main() {
	defer fmt.Println("defer hello")
	panic("crash")
	fmt.Println("hello,world")
}
```

執行結果：

```bash
defer hello
panic: crash
```

我們可以在執行一個可能會出錯的函式時使用 panic，當這個函式回傳的錯誤不是 nil 時直接讓系統崩潰，防止之後造成更大的錯誤。

```go
func main() {
	// 呼叫函式同時回傳結果與錯誤
	value, error := foo()
    
	// 檢查錯誤
	if error == nil {
		fmt.Println(value)
	} else {
		panic(error.Error())  // 系統中斷，印出錯誤訊息
	}
    
    // 其他程式碼
}
```



## recover

recover 可以捕捉到系統或手動產生的 panic 錯誤，回復系統以避免程序崩潰。recover 必須和 defer 配合使用，如下：

```go
func main() {
	defer func() {
		err := recover()
		fmt.Println(err)
	}()

	panic("crash")

	fmt.Println("hello,world")
}

/* 執行結果：
crash
*/
```

執行後不最造成系統崩潰，而是繼續執行 recover 後面的程式碼，但是不會繼續執行 panic 後面的程式碼。

我們可以用 recover 模擬 `try-catch` 機制，由於go語言的錯誤處理是函式層級，因此我們需要定義一個 `try` 函式，代替主函式來執行可能出錯的函式，如下：

```go
// 定義一個接受函式參數的函式，在發生錯誤時會捕捉錯誤防止崩潰
func try(action func()) {
	defer func() {
		err := recover()
		fmt.Println(err)
	}()

    // 執行方法
	action()
}

func main() {
	// 嘗試執行可能出錯的函式
	try(func() {
		panic("crash")
	})

    // 即使 try 裡面函式出錯依然可以往下執行
	fmt.Println("hello,world")
}

/* 執行結果：
crash
hello,world
*/
```



# 小結

今天大致上說明了 `defer` 、 `panic` 、 `recover` 用法，透過這三個語法同樣也能做出良好的錯誤處理程序。


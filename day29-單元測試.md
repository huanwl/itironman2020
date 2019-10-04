大家好，今天是鐵人賽第二十九天。終於快結束啦，倒數一天就來談談單元測試吧。

我雖然是資訊相關科系畢業，但是學校沒有教寫單元測試，上一間公司也不寫單元測試，因為上間是結案導向公司，專案時程都超級趕，可能我的技術能力也不太足夠。

到了現在的公司我才開始學習寫單元測試，而公司也都要求開發專案一定要寫單元測試。因此，我們的專案就會有產品碼和測試碼，最後交件時候產品碼一定要完成需求，測試碼算是可多可少。

在.net專案裡有三個主要的單元測試框架(MSTest, NUnit, xUnit)，至於用那一款這就視團隊而定，我們公司都是用 MSTest。

在Go語言中，單元測試框架已經內建在工具包裡，不需要再選擇框架或是安裝其他套件，這也讓我十分的驚訝，不愧是二十一世紀的程式語言。



# 單元測試(Unit Test)

單元測試是指對軟體最小受測範圍的測試，以系統分析的角度來看，單元指的是功能單元；若以程式碼的角度來看，單元指的是函式或類別。

Go使用 `go test` 來執行測試碼，而測試碼會寫在以 `_test` 結尾的測試文件中，如：hello_test.go。 一個測試文件是由一或多個測試函式組成，每個測試函式必須以 `Test` 開頭。

我們來寫一個單元測試的 hello world，內容如下：

```go
package main

import "testing"

func TestHello(t *testing.T) {
	t.Log("hello world")
}

func TestWorld(t *testing.T) {
	t.Error("world of bug")
}
```

在終端機執行 go test -v ，就會執行所有測試函式：( `-v` 顯示詳細資訊 )

```bash
$ go test -v              
=== RUN   TestHello
--- PASS: TestHello (0.00s)
    hello_test.go:6: hello world
=== RUN   TestWorld
--- FAIL: TestWorld (0.00s)
    hello_test.go:10: world hi
FAIL
exit status 1
FAIL	hello	0.003s
```

也可以用 `-run` 參數指定執行的函式：

```bash
$ go test -v -run TestHello
=== RUN   TestHello
--- PASS: TestHello (0.00s)
    hello_test.go:6: hello world
PASS
ok  	hello	0.002s
```



## 加法函式的單元測試

剛剛的範例只是用測試碼印出訊息，現在要來寫一個真正有意義的單元測試，就是一個簡單的加法程式為例，如下：

```go
package main

import (
	"fmt"
)

func add(a, b int) int {
	return a + b
}

func main() {
	fmt.Println(add(1, 2))
}
```

測試碼如下：

```go
package main

import "testing"

func Test_add_1_2(t *testing.T) {
	if add(1, 2) != 3 {
		t.Error("wrong result")
	}
}

func Test_add_33_55(t *testing.T) {
	if add(33, 55) != 88 {
		t.Error("wrong result")
	}
}

func Test_add_110_99(t *testing.T) {
	if add(110, 99) != 209 {
		t.Error("wrong result")
	}
}
```

測試結果：

```bash
$ go test -v                
=== RUN   Test_add_1_2
--- PASS: Test_add_1_2 (0.00s)
=== RUN   Test_add_33_55
--- PASS: Test_add_33_55 (0.00s)
=== RUN   Test_add_110_99
--- PASS: Test_add_110_99 (0.00s)
PASS
ok  	hello	0.003s
```

為什麼這麼簡單的測試，還要分成三個函式來寫，怎麼不寫在一起呢？

因為測試案例(Test Case)應該是獨立的，每個測試函式都是一個測試案例，我們可以把測試的參數或情境寫在函式名稱上，以便在執行測試時會更清楚。

最後，我覺得單元測試是一門深奧的學問，實務上要寫好單元測試，除了要善用介面與DI之外，還要會用假物件技巧像是 `Mock`、`Stub` 之類的，目前我還沒時間研究Go的這些東西。
大家好，今天是鐵人賽第二十三天。今天來講go語言的併發，稱為goroutine。goroutine屬於多執行緒處理，用 go 關鍵字執行一個函式，就會建立一個新的goroutine。如下所示：

```go
package main

import (
    "fmt"
    "time"
)

func main() {
	go fmt.Println("hello")
	time.Sleep(1 * time.Second)
}
```

由於goroutine被建立時，main函式就已經結束了，還沒執行完的goroutine就會被強制終止。因此上面的範例就必須要加上 time.Sleep 才看得到結果。

其實go語言的main函式本身就是一個goroutine，我們可以在主函式或其他函式中建立新的goroutine，如下：

```go
package main

import (
	"fmt"
	"time"
)

func say(s string) {
	for i := 0; i < 5; i++ {
		time.Sleep(100 * time.Millisecond)
		fmt.Println(s)
	}
	go fmt.Println(s, "end")
}

func main() {
	go say("goroutine1")
	go say("goroutine2")
	say("main")
	time.Sleep(100 * time.Millisecond)
}
```

執行結果：

```go
goroutine1
main
goroutine2
goroutine2
goroutine1
main
main
goroutine1
goroutine2
goroutine2
goroutine1
main
main
goroutine1
goroutine1 end
goroutine2
goroutine2 end
main end
```

多執行幾次就會發現每次結果的順序會不同。

另外，還可以用 runtime.GOMAXPROCS(1) 來設定執行緒上限：

```go
package main

import (
	"fmt"
	"runtime"
	"time"
)

func main() {
	runtime.GOMAXPROCS(1)
	go fmt.Println("hello")
	time.Sleep(1 * time.Second)
}
```


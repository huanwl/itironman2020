大家好，今天是鐵人賽第二十六天。通道可以讓多個goroutine的資料同步，不過go語言其實還有其他的同步機制，像是鎖定(lock)。所以今天就來講go語言還有哪些同步機制；在一些情境下，我們可能不需要傳輸資料，但是會共用一些變數，這時候使用鎖定會比通道來的更有效率。



# 同步

同步是為了在併發環境下確保資料的正確性，就像是資料庫的交易鎖定。前幾天講的通道是利用消息佇列的概念達到同步，因為佇列一定是先進先出。

通道的用途是多個goroutine之間的資料溝通，當我們的情境不需要溝通時，就可以使用其他更合適的方法來達成同步。

Go語言工具有提供競爭檢測，只要在執行指令後面加上 `-race` ，執行程式時就會進行競爭檢測分析。

```bash
go run -race ./main.go  
```

我們先來看一個有競爭問題的程式碼：

```go
package main

import (
	"fmt"
	"time"
)

var count int

func addCount() {
	count++                                             // 變數加 1
}

func getGount() int {
	return count                                    // 讀取變數
}

func main() {
	for i := 0; i < 1000; i++ {
		go addCount()
	}
	time.Sleep(1 * time.Second)     // 延遲 1 秒
	fmt.Println("count:", getGount())
}
```

用競爭檢測執行程式會得到以下訊息：

```bash
==================
WARNING: DATA RACE
Read at 0x0000005f8138 by goroutine 8:
  main.addCount()
      /home/huanwen/go/src/race/main.go:11 +0x3a

Previous write at 0x0000005f8138 by goroutine 7:
  main.addCount()
      /home/huanwen/go/src/race/main.go:11 +0x56

Goroutine 8 (running) created at:
  main.main()
      /home/huanwen/go/src/race/main.go:21 +0x4f

Goroutine 7 (finished) created at:
  main.main()
      /home/huanwen/go/src/race/main.go:21 +0x4f
==================
count: 975
Found 1 data race(s)
exit status 66
```

執行結果count不是1000，表示運行過程中因為競爭導致資料不正確。



## 互斥鎖定

互斥鎖定就是常見的多執行緒鎖定，或者SQL多連線下的交易鎖定，其目的就是確保資料正確性。

使用方式為宣告 `sync.Mutex` 變數，我們來修改上面的範例，如下：

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

var (
	count int
	mux   sync.Mutex
)

func addCount() {
	mux.Lock()                                          // 互斥鎖定
	defer mux.Unlock()                         // 結束函式時解除鎖定
	count++
}

func getGount() int {
	mux.Lock()                                          // 互斥鎖定
	defer mux.Unlock()                         // 結束函式時解除鎖定
	return count
}

func main() {
	for i := 0; i < 1000; i++ {
		go addCount()
	}
	time.Sleep(1 * time.Second)        // 延遲 1 秒
	fmt.Println("count:", getGount())
}

/* 執行結果：
count: 1000
*/
```

這次執行就不會有競爭問題了。

另外，Go語言還有提供另一種互斥鎖定，叫做讀寫互斥鎖定，適合用在讀比寫多的情境下，具有更好的效能。使用方式一樣，改成宣告 `sync.RWMutex` 變數，如下：

```go
var (
	count int
	rwMux sync.RWMutex
)

func addCount() {
	rwMux.Lock()                        // 互斥鎖定
	defer rwMux.Unlock()       // 結束函式時解除鎖定
	count++
}

func getGount() int {
	rwMux.RLock()                      // 唯讀鎖定
	defer rwMux.RUnlock()     // 結束函式時解除鎖定
	return count
}
```

差別在於多了唯讀鎖定，多個goroutine同時讀取資料時不會造成阻塞。



## 原子操作

如果只是數字增減，其實還有更有效率的作法，就是 `sync/atomic` 目錄下的原子套件。

```go
package main

import (
	"fmt"
	"sync/atomic"
	"time"
)

var count int32

func addCount() {
	atomic.AddInt32(&count, 1)               // 增加數值
}

func getGount() int32 {
	return atomic.LoadInt32(&count)   // 讀取數值
}

func main() {
	for i := 0; i < 1000; i++ {
		go addCount()
	}
	time.Sleep(1 * time.Second)              // 延遲 1 秒
	fmt.Println("count:", getGount())
}
```

原子操作是go語言中最輕量的鎖，功能僅限於數值增減、讀取、寫入，如果想要做複雜的資料交易還是要用互斥鎖定。



## 等待群組

上面所有的範例其實都有一個問題，就是main函式可能會提早結束，尚未完成的goroutine就會全滅。因為範例都沒有使用通道，所以無法傳送停止訊號。

如果範例中for迴圈的迭代次數改成10，並且把 `time.Sleep` 拿掉，就會發現count還是0，因為新建的goroutine都還沒開始，主函式就結束了。

```go
func main() {
	for i := 0; i < 10; i++ {
		go addCount()
	}
	fmt.Println("count:", getGount())
}

/* 執行結果：
count: 0
*/
```

因此，Go語言就提供了一個等待群組的功能，內部有一個計數器可以等待所有任務完成，宣告 `sync.WaitGroup` 變數就可以使用：

```go
package main

import (
	"fmt"
	"sync"
	"sync/atomic"
)

var count int32

func addCount() {
	atomic.AddInt32(&count, 1)
}

func getGount() int32 {
	return atomic.LoadInt32(&count)
}

func main() {
	var wg sync.WaitGroup  // 宣告等待群組變數

	for i := 0; i < 10; i++ {
		wg.Add(1)  						 // 新增一個任務
		go func() {
			defer wg.Done()		   // 完成一個任務
			addCount()
		}()
	}
	wg.Wait()                              // 等待所有任務完成
	fmt.Println("count:", getGount())
}

/* 執行結果：
count: 10
*/
```

這樣一來就不需要 `time.Sleep` 了，所有任務完成就會往下繼續執行。
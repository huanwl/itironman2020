大家好，今天是鐵人賽第二十五天。昨天介紹了通道的基本用法，我覺得通道是一個很複雜的東西，用不好很容易 deadlock，而今天就繼續來講通道的其他特性。



## 有緩衝的通道

昨天講的通道都是無緩衝的通道，在發送和接收的時候都會發生阻塞，直到有另一方goroutine有接收或發送。

為了解決阻塞問題，Go語言提供一種帶有緩衝(Buffered)的通道，語法如下：

```go
func main() {
	ch := make(chan int, 2)  // 宣告一個長度為 2 的 buffer
    fmt.Println(len(ch))  // 用 len(ch) 取得 buffer 內有幾筆資料

	ch <- 1
	fmt.Println(len(ch))

	ch <- 1
	fmt.Println(len(ch))
}
```

當一個有緩衝通道被傳入資料時，不會發生錯誤，資料會先暫存 buffer 裡，直到被接收端取出。但如果 buffer 已經滿了，這時候又再傳入就會發生錯誤；反之，如果接收的通道是空 buffer，也會發生錯誤。



## 單向通道

在無緩衝通道下，goroutine是只能做發送或接收其中一種，必須要有另一個goroutine配合才不會阻塞。但是在有緩衝通道下，goroutine是可以自己發送以及接收，因此我們可以限制通道是只能發送或接收，這個稱為單向通道。

單向通道算是抽象型別，內部仍然是一個正常通道，用 `chan<-` 和 `<-chan` 來宣告：

```go
var 通道名稱 chan<- 資料型別  // 只能發送通道
var 通道名稱 <-chan 資料型別  // 只能接收通道
```

範例如下：

```go
// 傳入的單向通道只能用來發送資料
func square(s []int, chSendOnly chan<- int) {
	for _, v := range s {
		chSendOnly <- v * v  //發送資料
	}
	close(chSendOnly)  // 關閉通道
}

func main() {
	s := []int{1, 2, 3, 4, 5, 6}

	ch := make(chan int)
	var chSendOnly chan<- int = ch  // 建立單向通道
	go square(s, chSendOnly)

	for data := range ch {
		fmt.Println(data)  // 接收資料
	}
}

/* 執行結果：
1
4
9
16
25
36
*/
```



## 多通道處理

goroutine之間是可以用多個通道來溝通，只是這樣做會變得更複雜。Go語言提供一種專門處理多通道的語法 - `select` 關鍵字。

`select` 語法非常像 `switch`，執行時會選擇一個不會阻塞的 `case` 操作，如果都會阻塞就執行`default`，如果有多個 `case` 不會阻塞就會隨機執行其中一個：

```go
select {
    case 通道操作1:
    	// 對應的行為
    case 通道操作2:
    	// 對應的行為
    default:
    	// 通道阻塞的情況
}
```

下面範例是計算費式數列的程式，fibonaccis 函式會不停地計算並傳送結果，直到接收到停止訊號，如下：

```go
func fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
		case c <- x:         // 發送計算結果
			x, y = y, x+y
		case <-quit:       // 接收停止訊號
			fmt.Println("quit")
			return
		}
	}
}

func main() {
	c := make(chan int)
	quit := make(chan int)
	go func() {
		for i := 0; i < 10; i++ {
			fmt.Println(<-c)  // 接收計算結果
		}
		quit <- 0                     // 發送停止訊號
	}()
	fibonacci(c, quit)
}

/* 執行結果：
0
1
1
2
3
5
8
13
21
34
quit
*/
```

另外，如果有設定 `default` 就會在所有 `case` 都阻塞的情況下執行。

下面範例是設定兩個時間通道，一個是每100毫秒持續通知，另一個是500毫秒後通知一次，當兩個通道都沒回應時，會進入default：

```go
func main() {
	tick := time.Tick(100 * time.Millisecond)        // 建立一個每100毫秒持續通知的時間通道
	boom := time.After(500 * time.Millisecond)  // 建立一個500毫秒後通知一次的時間通道
	for {
		select {
		case <-tick:
			fmt.Println("tick.")
		case <-boom:
			fmt.Println("BOOM!")
			return                                                                     // 結束goroutine
		default:
			fmt.Println("    .")
			time.Sleep(100 * time.Millisecond)           // 延遲100毫秒
		}
	}
}

/* 執行結果：
    .
tick.
    .
tick.
    .
tick.
    .
tick.
    .
tick.
BOOM!
*/
```




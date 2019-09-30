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


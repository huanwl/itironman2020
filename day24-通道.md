大家好，今天是鐵人賽第二十四天。Go語言的goroutine可以併發，提高程式運算效能，但還需要配合通道的使用，才能發揮最大的效益。 今天就來講go語言的通道，可以在多個goroutine之間傳送資料。



# 通道(channel) 

通道是一個訊息佇列(Message Queue)的概念，遵守先進先出(First In First Out)的規則。goroutine之間可以透過通道來傳送或是等待訊息。

通道也是一種型別，預設值為nil，宣告時要用 `chan` 關鍵字，以及定義傳送資料的型別；在建立實體時要用 `make`，如下：

```go
func main() {
    var c chan int
    fmt.Println(c)

    c = make(chan int)
    fmt.Println(c)
}

/* 執行結果：
<nil>
0xc000082060
*/
```



## 傳送資料

建立通道後，就可以傳送和接收資料，這個動作在go語言中有特殊的運算子 `<-` ，格式如下：

```go
// 發送資料
通道  <-  值

// 接收資料
變數  <-  通道
```

當一個goroutine發送資料時，就必須有另一個goroutine接收資料，不然程式會有錯誤，如下：

```go
func main() {
	c := make(chan int)
	c <- 0
}
```

執行就會發生錯誤，因為發送端正在等待接收端取得資料，沒有接收端的話就會一直等下去，變成了死結，錯誤訊息如下：

```bash
fatal error: all goroutines are asleep - deadlock!
```

除了要加上接收端，還必須從另一個goroutine傳送資料才會成功，如下：

```go
func main() {
	c := make(chan int)

    // 用匿名函式創建一個goroutine
	go func() {
		c <- 0
	}()

	val := <-c
	fmt.Println(val)
}

/* 執行結果：
0
*/
```

如此可知，通道是必須在併發的條件下才能夠使用的。



## 多個goroutine

如果是在main函式裡建立多個goroutine，依然可以使用同一個通道。下面是 *A Tour of Go* 的範例，一個整數陣列加總的併發程式：

```go
// 定義一個整數陣列加總的函式
func sum(s []int, c chan int) {
	sum := 0
	for _, v := range s {
		sum += v
	}
	c <- sum // 傳送總和
}

func main() {
	s := []int{1, 2, 3, 4, 5, 6}

	c := make(chan int)
	go sum(s[:len(s)/2], c)
	go sum(s[len(s)/2:], c)
    
	x, y := <-c, <-c // 接收總和
	fmt.Println(x, y, x+y)
}

/* 執行結果：
15 6 21
*/
```

在通道接收資料時，一次只能接收一個值，如果有多個goroutine就要接收多次通道。

因此，也可以用 `for` 循環接收通道值：

```go
for data := range c {
    // do something
}
```

但是必須要自己判斷退出的條件，例如用一個 `count` 變數來紀錄收到多少資料。
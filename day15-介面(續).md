大家好，今天是鐵人賽第十五天。昨天介紹了介面的基本用法，了解go語言的介面該如何宣告及使用，而介面還有一些特性沒有提到，今天來把剩下的東西補完。



## 介面的內嵌

我記得在 Java/C# 語言裡，有一句很經典的話，叫做「類別繼承類別、類別實作介面、介面擴展介面」，這句話說明了類別與介面本身及彼此之間的依賴關係。

在 [day13-內嵌](https://ithelp.ithome.com.tw/articles/10217359) 中有提到go語言沒有繼承特性，但是有提供內嵌特性來補足繼承，而且不但結構有內嵌，介面也可以使用內嵌。因此，go語言的介面透過內嵌就可以作到介面擴展介面的效果了。

我們用昨天的範例繼續說明，先定義三個介面，如下:

```go
// 定義一個訊息發送者介面
type MessageSender interface {
	Send(content string)
}

// 定義一個Log紀錄介面
type Logger interface {
	Log(info string)
}

// 定義一個發行者介面，是用兩個介面擴展而成的
type Publisher interface {
	MessageSender
	Logger
    Connect()
}
```

上面範例中，Publisher介面擴展了MessageSender和Logger這兩個介面，獲得其中的方法簽章，以及本身也有定義一個 Connect() 方法簽章。因此，如果要實作Publisher介面的話，就必須實作三個方法，如下:

```go
// 定義一個Email發送者
type EmailSender struct{}

// 實作 Send
func (s *EmailSender) Send(content string) {}

// 實作 Log
func (s *EmailSender) Log(info string)     {}

// 實作 Connect
func (s *EmailSender) Connect()            {}
```

必須完整實作所有的介面方法，才能夠相容於該介面。以下是完整的範例:

```go
type MessageSender interface {
	Send(content string)
}

type Logger interface {
	Log(info string)
}

type Publisher interface {
	MessageSender
	Logger
	Connect()
}

type EmailSender struct{}

func (s *EmailSender) Send(content string) {}
func (s *EmailSender) Log(info string)     {}
func (s *EmailSender) Connect()            {}

func main() {
	var pub Publisher
	pub = &EmailSender{}
	describe(pub)
}

func describe(i interface{}) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```

執行結果:

```bash
(&{}, *main.EmailSender)
```



## 型別斷言(type assertion)

有時候我們會需要將介面轉成原來的型別；在Go語言中，可以使用型別斷言，將介面轉換成型別，或是另一個介面。斷言語法如下:

```go
t := i.(T)
```

- i 代表介面變數
- T 代表轉換後的目標型別
- t 代表轉換後的目標變數

範例:

```go
var pub Publisher
pub = &EmailSender{}
sender := pub.(*EmailSender)
```

當我們需要介面裡的結構實體中的資料時，就可以利用斷言將介面轉成結構型別:

```go
type MessageSender interface {
	Send(content string)
}

type EmailSender struct{ Address string }

func (s *EmailSender) Send(content string) {}

func main() {
	var sender MessageSender
	sender = &EmailSender{Address: "test@gmail.com"}
	emailSender := sender.(*EmailSender)
	fmt.Println(emailSender.Address)  // 轉換後就可以取得Address
}
// 執行結果: test@gmail.com
```

另外，如果介面裡的實體型別並非轉換的型別，這樣就會發生錯誤，如下面的範例:

```go
type MessageSender interface {
	Send(content string)
}

type EmailSender struct{ Address string }

func (s *EmailSender) Send(content string) {}

type SmsSender struct{ Mobile string }

func (s *SmsSender) Send(content string) {}

func main() {
	var sender MessageSender
	sender = &EmailSender{Address: "test@gmail.com"}
	smsSender := sender.(*SmsSender)
	fmt.Println(smsSender.Mobile)
}
```

上面範例中，介面裡是EmailSender，卻要轉成SmsSender，就會出現下面的錯誤:

```bash
panic: interface conversion: main.MessageSender is *main.EmailSender, not *main.SmsSender
```

go語言提供另一種寫法可以避免這樣的錯誤:

```go
smsSender, ok := sender.(*SmsSender)
if ok {
    fmt.Println(smsSender.Mobile)
}
```

當轉型失敗時，ok變數的值是false，而且不會報錯。



## 型別分支(type switch)

Go語言的 `switch` 還有一個特殊用法，可以判斷一個介面裡的實體型別。這需要搭配型別斷言，語法如下:

```go
switch v := i.(type) {
case T1:
    // 這裡的 v 是 T1 型別
case T2:
    // 這裡的 v 是 T2 型別
default:
    // 沒有符合的型別，這裡的 v 是 i 介面
}
```

用空介面來舉一個簡單的範例:

```go
func main() {
	printAnyType(2020)
	printAnyType("Iron Man")
	printAnyType(0.25)
}

// 定義一個函式，接收任何型別，並且格式化輸出值
func printAnyType(i interface{}) {
	switch v := i.(type) {
	case int:
		fmt.Printf("case int: %d \n", v)
	case string:
		fmt.Printf("case string: %s \n", v)
	default:
		fmt.Printf("default: %v \n", v)
	}
}
```

執行結果:

```bash
case int: 2020 
case string: Iron Man 
default: 0.25 
```

型別分支搭配空介面非常的實用，因為空介面可以相容於任何型別，就連基本型別也不例外。



# 小結

今天把介面剩下的用法講完了，主要還是圍繞在型別相關的用法，go語言在型別語法上算是設計的很細膩。終於到了比賽的一半，現在已經有點疲乏了，希望之後還能繼續稱下去。
大家好，今天是鐵人賽第十四天。今天要來講go語言的介面，和一般靜態型別語言中的介面不一樣，go語言不需要明確地定義實作介面，而是採用隱性實作，只要符合方法簽章即可，這一點倒是很像動態型別語言，只在意行為，而非實體。



# 介面(Interface)

介面是物件導向程式得以抽象化的最大關鍵，通常會用於程式與模組之間的解藕，我們先來看看go語言如何定義介面，語法很像結構，只改用 `interface` 關鍵字，如下:

```go
// 定義一個訊息發送者介面
type MessageSender interface {
	Send(content string)   // 方法簽章
}
```

一個介面是由一或多個方法簽章(method signature)所組成，能夠相容實作所有方法簽章的結構。因此，以上面範例來說，我們定義了一個訊息發送者介面，所有實作 Send() 方法的結構都能相容於 MessageSender。我們將範例加上實作結構:

```go
// 定義一個訊息發送者介面
type MessageSender interface {
	Send(content string)
}

// 定義一個Email發送者結構
type EmailSender struct {
	Address string
}

// 實作Send方法於EmailSender
func (s *EmailSender) Send(content string) {
	fmt.Println("Send:", content, "to", s.Address)
}

// 定義一個簡訊發送者結構
type SmsSender struct {
	Mobile string
}

// 實作Send方法於SmsSender
func (s *SmsSender) Send(content string) {
	fmt.Println("Send:", content, "to", s.Mobile)
}

func main() {
	var sender MessageSender

	sender = &EmailSender{Address: "test@gmail.com"}
	sender.Send("hello,email")

	sender = &SmsSender{Mobile: "0900000000"}
	sender.Send("hello,sms")
}
```

執行結果:

```go
Send: hello,email to test@gmail.com
Send: hello,sms to 0900000000
```

上面範例簡單地實作Email發送者和簡訊發送者，go語言不需要明確告知結構該實作哪個介面，只要實作介面中的方法即可，這就是隱性實作。



## 介面的值

介面可以相容於實作它的結構，意思是介面變數可以包含實作它的結構變數，這樣聽起來有點抽象。換句話說，介面的值其實就是它所包含的結構實體，當結構實體被介面包含時，只能呼叫介面中的方法。我們繼續用上面的範例說明，這次我們把內容與型別印出來:

```go
// 定義訊息發送者介面
type MessageSender interface {
	Send(content string)
}

// 定義Email發送者結構與實作訊息發送者介面
type EmailSender struct {}
func (s *EmailSender) Send(content string) {}

// 定義簡訊發送者結構與實作訊息發送者介面
type SmsSender struct {}
func (s *SmsSender) Send(content string) {}

func main() {
	var sender MessageSender

	sender = &EmailSender{}
	describe(sender)

	sender = &SmsSender{}
	describe(sender)
}

func describe(i MessageSender) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```

執行結果:

```bash
(&{}, *main.EmailSender)
(&{}, *main.SmsSender)
```

從執行結果可以看出，介面變數的型別會是包含的結構的型別。因此，我們可以認定介面是一種特別的變數，它的值和型別都是依據其包含的結構實體。



## 介面與nil

宣告的介面變數沒有指定實體的話，預設值是nil，如下:

```go
var sender MessageSender
fmt.Println(sender)
// 執行結果: <nil>
```

如果執行介面中的方法，ex: **sender.Send("hello")** ，就會報錯:

```bash
panic: runtime error: invalid memory address or nil pointer dereference
```

而當介面包含的結構實體是nil，情況就不一樣了，直接來看:

```go
func (s *EmailSender) Send(content string) {
	if s == nil {
		return // 傳入的是nil接受者，直接結束方法
	}
	fmt.Println("Send:", content, "to", s.Address)
}

func main() {
    var sender MessageSender

    var emailSender *EmailSender
    sender = emailSender

    sender.Send("hello")
}
```

這個範例省略了MessageSender和EmailSender的定義，因為重點在於實作的Send()方法。

在go語言中，當介面內的實體為nil時，依然可以正常呼叫方法，但是會產生 `nil接受者`，必須在方法裡判斷並且做處理，這樣就不會造成程式中斷。



## 百變的空介面

在Java/C#語言中，有一個東西叫做最上層物件，稱作Object。因為所有物件類別都是繼承它，所以它可以相容於任何物件，聽起來根本就是外掛。

在Go語言中，也有一個類似的作法，就叫空介面(Empty Interface)。剛剛我們有提到只要實作介面方法的結構都能相容該介面，那如果是一個沒有方法的介面呢？是不是代表不用實作就能相容介面？沒錯，空介面就是這個概念，而且我們可以直接用 `interface{}` 來宣告它:

```go
func describe(i interface{}) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```

這是剛剛用來印出介面值和型別的方法，我們把原本的 `MessageSender` 改成 `interface{}`，這樣以來 describe 方法就能夠接受所有的結構實體了。



# 小結

今天介紹了go語言的介面基本用法，我非常喜歡這種隱性實作的特性，寫起來會有動態程式語言感覺，這使得go語言的程式碼更加輕巧靈活。








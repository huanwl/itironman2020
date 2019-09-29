大家好，今天是鐵人賽第二十天。字串是程式設計中最常使用的資料型態，尤其是在處理 io 的時候，例如：檔案、網路、資料庫等等。今天我就來介紹一些go語言中常用的字串操作方式。



# 計算字串長度

ASCII編碼為主的字串，使用 `len()` 計算長度：

```go
str := "Iron Man 2020"
len := len(str)
println(len)

/* 執行結果
13
*/
```

如果要計算unicode字串，例如：中文字，就要用 `utf8.RuneCountInString()`：

```go
str := "鐵人賽"

lenASCII := len(str)
println(lenASCII)

lenUtf8 := utf8.RuneCountInString(str)
println(lenUtf8)

/* 執行結果
9
3
*/
```



# 巡覽字元

go語言的字串雖然是獨立型別，但也可以當作是一個字元陣列，可以使用 `for` 迴圈搭配索引子 `[ ]` 取得字串中的字元:

```go
str := "鐵人賽2020"

// 逐步印出每個字元的圖示、編碼、型別
for i := 0; i < len(str); i++ {
    fmt.Printf("%c\t%d\t%T\n", str[i], str[i], str[i])
}
```

執行結果：

```bash
é	233	uint8
	144	uint8
µ	181	uint8
ä	228	uint8
º	186	uint8
º	186	uint8
è	232	uint8
³	179	uint8
½	189	uint8
2	50	uint8
0	48	uint8
2	50	uint8
0	48	uint8
```

上面執行結果中，會發現中文字都印成亂碼了，我們可以改用 `for range`：

```go
str := "鐵人賽2020"

for _, s := range str {
    fmt.Printf("%c\t%d\t%T\n", s, s, s)
}
```

執行結果：

```go
鐵	37941	int32
人	20154	int32
賽	36093	int32
2	50	int32
0	48	int32
2	50	int32
0	48	int32
```



# 格式化

格式化可以將不同型別的資料用  `fmt.Printf()` 印出，或是用  `fmt.Sprintf()` 轉成一個字串，go語言提供的格式化動詞非常多，以下列出常用的動詞：



| 動詞 | 功能                                                         |
| :--- | ------------------------------------------------------------ |
| %v   | 輸出變數的值，通常用於結構                                   |
| %+v  | 輸出變數的值，通常用於結構，會將結構的欄位和值一起印出       |
| %#v  | 輸出變數的值，通常用於結構，會將結構的欄位、值和型別一起印出 |
| %T   | 輸出資料型別                                                 |
| %%   | 輸出 % 符號                                                  |
| %b   | 整數，二進位顯示                                             |
| %o   | 整數，八進位顯示                                             |
| %d   | 整數，十進位顯示                                             |
| %x   | 整數，十六進位顯示，字母小寫                                 |
| %X   | 整數，十六進位顯示，字母大寫                                 |
| %U   | Unicode字元                                                  |
| %f   | 浮點數                                                       |
| %p   | 指標，十六進位顯示                                           |

以下是對結構體輸出的範例：

```go
type Hero struct {
	name string
	age  int
}

func main() {
	tony := &Hero{"Iron Man", 30}

	fmt.Printf("%v\n", tony)
	fmt.Printf("%+v\n", tony)
	fmt.Printf("%#v\n", tony)
	fmt.Printf("%T\n", tony)
}
```

執行結果：

```bash
&{Iron Man 30}
&{name:Iron Man age:30}
&main.Hero{name:"Iron Man", age:30}
*main.Hero
```



# Base64編碼 

Base64 是一種常見的字元編碼方式，常用於網路傳輸，像是電子郵件。Go語言標準庫就有提供 base64 套件，在 `encoding/base64` 目錄下。範例如下：

```go
import (
	"encoding/base64"
	"fmt"
)

func main() {
	text := "Day 20th in Iron Man 2020"

	encodedText := base64.StdEncoding.EncodeToString([]byte(text))
	println("Encoded Text:", encodedText)

	decodedText, err := base64.StdEncoding.DecodeString(encodedText)
	if err == nil {
		fmt.Println("Decoded Text:", string(decodedText))
	} else {
		fmt.Println("Error:", err.Error())
	}
}
```

執行結果：

```bash
Encoded Text: RGF5IDIwdGggaW4gSXJvbiBNYW4gMjAyMA==
Decoded Text: Day 20th in Iron Man 2020
```



# JSON編碼

JSON是現在最流行的 Web API 資料交換格式，Go語言標準庫也有提供 json 套件，在 `encoding/json` 目錄下。範例如下：

```go
import (
	"encoding/json"
	"fmt"
)

type Hero struct {
	Name string
	Age  int
}

func main() {
	tony := &Hero{"Iron Man", 30}

	json, err := json.Marshal(*tony)

	if err != nil {
		fmt.Println("Error:", err.Error())
	} else {
		fmt.Println("JSON:", string(json))
	}
}
```

執行結果：

```bash
JSON: {"Name":"Iron Man","Age":30}
```

結構體轉成JSON字串要注意一件事，欄位名稱必須是開頭大寫才會被輸出。



# 正則表示式

字串處理最難的應該就是正則表達式，Go語言標準庫提供 regexp套件，在 `regexp` 目錄下。範例如下： 

```go
import (
	"fmt"
	"regexp"
)

func main() {
	match, err := regexp.MatchString("09[0-9]{8}", "0900000000")
	if err != nil {
		fmt.Println(err.Error())
	} else {
		fmt.Println(match)
	}
}
```

執行結果：

```bash
true
```

上面的範例是一個檢查手機號碼格式的程式，Go語言的 regexp 套件還有很多用法，可以直接參考官網文件 [regexp](https://golang.org/pkg/regexp/)。



# 小結

今天介紹了一些go語言常用的字串操作方法，在實務上用到字串的情境應該多到爆炸，其他的用法就等之後實際開發再慢慢研究了。

 
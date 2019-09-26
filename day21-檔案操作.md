大家好，今天是鐵人賽第二十一天。今天來介紹go語言的檔案操作方式。



# ioutil 套件

ioutil 套件是最簡單的檔案操作方式，定義在 `io/ioutil` 目錄下，範例如下：

```go
package main

import (
	"fmt"
	"io/ioutil"
)

func main() {
    // 宣告檔案路徑
	const fileName string = "/dir/filename"

    // 讀取檔案所有內容
	dat, err := ioutil.ReadFile(fileName)
	if err != nil {
		panic(err)
	}

	fmt.Println(string(dat))
}
```



# OS套件

如果想要對檔案做比較細部的操作，可以使用 os 套件來操作，它允許我們先開啟一個檔案，然後再執行一連串的操作，如下：

```go
import (
	"fmt"
	"os"
)

func main() {
    // 宣告檔案路徑
    const fileName string = "/dir/filename"
    
    // 開啟一個檔案
	f, err := os.Open(fileName)
	if err != nil {
		panic(err)
	}

    // 建立一個Buffer
	b := make([]byte, 2)
    
    // 開始讀檔
	n, _ := f.Read(b)
	for n > 0 {
		fmt.Printf("%d\n%s\n", n, b)
		n, _ = f.Read(b)
	}
}
```



# 參考

1. https://gobyexample.com/reading-files
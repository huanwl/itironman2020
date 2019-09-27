大家好，今天是鐵人賽第二十二天。今天要來介紹go語言的網路操作方式，網路其實也是 io 的一種，最常見的就是 HTTP 協定。



# HTTP Client

go語言標準庫的 `net/http` 目錄下的 `http` 套件提供了基本的存取網路位置的方法，我們常常會有串接第三方服務的需求，或是寫爬蟲，就可以用這個套件。下面的範例是抓取鐵人賽首頁的內容：

```go
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
)

func main() {
	resp, err := http.Get("https://ithelp.ithome.com.tw/")
	if err != nil {
		panic(err)
	}
    
	defer resp.Body.Close()

	fmt.Println("Response status:", resp.Status)

    body, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		panic(err)
	}

	fmt.Println(string(body))
}
```

由於網路也是 io 的一種，因此我們可以用 `ioutl` 讀檔的方式來取得網頁內容。



# HTTP Server

Go語言架設網路伺服器非常簡單，標準包就有內建路由，範例如下：

``` go
package main

import (
	"fmt"
	"net/http"
)

func hello(w http.ResponseWriter, req *http.Request) {
	fmt.Fprintf(w, "hello\n")
}

func headers(w http.ResponseWriter, req *http.Request) {
	for name, headers := range req.Header {
		for _, h := range headers {
			fmt.Fprintf(w, "%v: %v\n", name, h)
		}
	}
}

func main() {

	http.HandleFunc("/hello", hello)
	http.HandleFunc("/headers", headers)

	http.ListenAndServe(":5000", nil)
}
```

上面範例執行後，開啟瀏覽器輸入 http://localhost:5000/hello，會得到 hello 文字：

```bash
hello
```

如果改成輸入 http://localhost:5000/headers，會得到網頁標頭：

```go
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3
Sec-Fetch-Site: none
Connection: keep-alive
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36
Sec-Fetch-User: ?1
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-TW,zh;q=0.9,en-US;q=0.8,en;q=0.7
Upgrade-Insecure-Requests: 1
Sec-Fetch-Mode: navigate
```



# 參考

1. https://gobyexample.com/http-clients
2. https://gobyexample.com/http-servers
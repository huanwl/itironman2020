大家好，今天是鐵人賽第二十一天。今天來介紹go語言的檔案操作方式。



# ioutil 套件

ioutil 套件被意義在 `io/ioutil` 目錄下，下面的範例是讀取一個目錄下的所有檔案：

```go
package main

import (
	"fmt"
	"io/ioutil"
)

func check(e error) {
	if e != nil {
		panic(e)
	}
}

func main() {
	const dirName string = "/home/username/dir"

	fileInfos, err := ioutil.ReadDir(dirName)
	check(err)

	for _, item := range fileInfos {
		content := readFile(dirName + item.Name())
		fmt.Println(content)
	}

}

func readFile(fileName string) string {
	dat, err := ioutil.ReadFile(fileName)
	check(err)
    
	return string(dat)
}
```


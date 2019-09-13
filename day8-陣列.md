大家好，今天是鐵人賽第八天。昨天介紹了go語言的指標，今天開始我們要進入容器的世界，go語言提供許多好用的容器型別，像是陣列、切片等，使我們可以很有效率地操作集合資料。



# 陣列

陣列(Array)是指一段固定長度的連續空間，相對變數而言，一個整數變數只能放入一個整數，而一個整數陣列可以放入多個整數，視宣告的陣列長度而定。



## 陣列宣告

go語言宣告陣列就和變數一樣，陣列也是一種型別，而且指標變數很像。在宣告時需要用 `[ ]` 指定長度，表示需要多少的元素，如下:

``` go
var arr [3]string

fmt.Println(arr)
// 印出: [   ]
```

上面的範例會印出3個空字串，是因為陣列宣告後就會為每個元素產生初始值，我們可以想像陣列就是一群變數，當變數被宣告時就會有初始值。

我們可以對陣列用 `[ ]` 指定元素進行存取，`[ ]` 裡面放的是元素索引，從 0 開始:

``` go 
var arr [3]string

arr[0] = "Iron Man"
arr[1] = "Dr.Stange"
arr[2] = "Thor"

fmt.Println(arr)
// 印出: [Iron Man Dr.Stange Thor]
```



## 初始化

陣列在宣告時，可以直接指定初始值，如下:

``` go
var arr = [3]string{"Iron Man", "Dr.Stange", "Thor"}
// 或是 arr := [3]string{"Iron Man", "Dr.Stange", "Thor"}

fmt.Println(arr)
// 印出: [Iron Man Dr.Stange Thor]
```

初始化的數量不一定要和陣列長度一樣，陣列會從 0 開始初始化，沒指定初始值的元素就會是型別的初始值:

``` go 
arr := [3]string{"Iron Man"}

fmt.Println(arr)
// 印出: [Iron Man   ]
```

上面範例印出 "Iron Man" 和兩個空字串。

另外，如果陣列有設定初始值時，可以省略陣列長度，用 `...` 取代長度，go會依照初始值的數量，幫我們建立一個相同長度的陣列:

``` go 
arr := [...]string{"Iron Man", "Dr.Stange", "Thor"}

fmt.Println(arr)
// 印出: [Iron Man Dr.Stange Thor]
```

另外，我們可以用 `len( )` 函式來檢查一個陣列的長度:

``` go
arr := [...]string{"Iron Man", "Dr.Stange", "Thor"}

fmt.Println(len(arr))
// 印出: 3
```



## 巡覽陣列

巡覽(或稱作拜訪)對於容器來說，是一個十分常見的操作，我們時常需要一個一個檢查或是修改陣列的每個元素。在很多語言中(像是C#)，會提供 `foreach` 這種專門做巡覽的語法，而go雖然沒有 `foreach` ，但也是可以簡單地做到巡覽:

``` go
arr := [...]string{"Iron Man", "Dr.Stange", "Thor"}

// k為元素索引，v為元素值
for k, v := range arr {
    fmt.Println(k, v)
}
```

執行結果:

``` bash
0 Iron Man
1 Dr.Stange
2 Thor
```



# 小結

今天大概介紹了陣列的基本用法，明天會介紹和陣列很像的另一種容器 - 切片。

最後祝大家中秋節快樂，希望大家烤肉順利，比賽也順利。

 


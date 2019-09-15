大家好，今天是鐵人賽第九天。今天要來介紹go語言的切片，它是一個對於陣列的抽象介面。昨天介紹的陣列是一個固定長度的連續空間，而我們可以利用切片來操作陣列，動態配置不固定長度的連續空間。



# 切片

切片(slice)和陣列很像，用法也差不多，因為切片的底層就是一個陣列，我們可以想像切片是一個用來操作陣列的介面。因此透過切片，我們可以很簡單的切割陣列，取得我們想要的資料區間，如下:

``` go
// 宣告字串陣列，並且初始化 5 個英雄名稱
arr := [5]string{"Iron Man", "Dr.Strange", "Thor", "Captain America", "Hulk"}

// 建立字串切片，並且取得arr陣列的第 1 個到第 3 個元素
slice := arr[1:4]

// 印出切片元素
for k, v := range slice {
    fmt.Println(k, v)
}
```

執行結果:

``` bash
0 Dr.Strange
1 Thor
2 Captain America
```

上面範例是切片最基本用法，利用 `[ 開始位置 : 結束位置 ]` 對現有陣列來產生一個新的切片，這個過程中並不會產生新的資料，因為切片只是一個介面，所以透過 `for` 印出的切片元素也就是原本陣列的元素。

當我們對切片修改資料時，也會修改原始陣列的資料，因為資料是同一份。下面的範例中，我們嘗試把切片裡的幾個漫威英雄改成DC英雄，然後印出原始陣列:

``` go
arr := [5]string{"Iron Man", "Dr.Strange", "Thor", "Captain America", "Hulk"}

slice := arr[1:4]
slice[0] = "Bat Man"
slice[1] = "Super Man"
slice[2] = "Wonder Woman"

for k, v := range arr {
    fmt.Println(k, v)
}
```

執行結果:

``` go 
0 Iron Man
1 Bat Man
2 Super Man
3 Wonder Woman
4 Hulk
```



## 切片切法

切片的主要語法是 `[ M : N ]` ，M 為開始位置，N 為結束位置。實際執行時，會從陣列第 M 個元素切到第 N-1 個元素，如上面範例中的 `arr[1:4]` 只會切出位於陣列第 1 ~ 3 個元素。

我們也可以省略語法中的開始位置或結束位置，這樣就會用預設值來切陣列，開始位置預設值為 0，結束位置預設為陣列長度，如下:

``` go
func main() {
	arr := [5]string{"Iron Man", "Dr.Stange", "Thor", "Captain America", "Hulk"}

	// arr[:] 相同於 arr[0:len(arr)]
	slice := arr[:]
	printHeros(slice)

	// arr[:2] 相同於 arr[0:2]
	slice = arr[:2]
	printHeros(slice)

	// arr[2:] 相同於 arr[2:len(arr)]
	slice = arr[2:]
	printHeros(slice)
}

func printHeros(slice []string) {
	for k, v := range slice {
		fmt.Printf("[%d]%s, ", k, v)
	}
	fmt.Println()
}
```

執行結果:

``` bash
[0]Iron Man, [1]Dr.Strange, [2]Thor, [3]Captain America, [4]Hulk, 
[0]Iron Man, [1]Dr.Strange, 
[0]Thor, [1]Captain America, [2]Hulk, 
```

上面範例在每次切陣列的時候，go都會產生出新的切片，因此每次印出的結果都不相同。



## 切面內部

切片的內部包含 `開始位置`、`長度(length)`、`容量(capacity)`。切片會從開始位置找到連續空間中的一個記憶體位址，然後用長度存取固定資料，而容量則表示還能再切多少元素。

- 長度定義: 從 **切片開頭位置** 到 **切片結束位置** 的長度。 
- 容量定義: 從 **切片開頭位置** 到 **原始陣列結束位置** 的長度。

我們可以用 `len( )` 取得切片長度，以及 `cap( )` 取得切片容量，如下:

``` go 
func main() {
	arr := [5]string{"Iron Man", "Dr.Strange", "Thor", "Captain America", "Hulk"}

    // 從arr切出[1:]，長度少1，容量少1
	slice := arr[1:]
	printHeros(slice)

    // 從slice切出[1:]，長度少1，容量少1
	slice = slice[1:]
	printHeros(slice)

    // 從slice切出[1:]，長度少1，容量少1
	slice = slice[1:]
	printHeros(slice)

    // 從slice切出[:1]，長度為1，容量不變
	slice = slice[:1]
	printHeros(slice)
}

func printHeros(slice []string) {
	fmt.Printf("len=%d, cap=%d, value=", len(slice), cap(slice))

	for k, v := range slice {
		fmt.Printf("[%d]%s, ", k, v)
	}
	fmt.Println()
}
```

執行結果:

``` bash
len=4, cap=4, value=[0]Dr.Strange, [1]Thor, [2]Captain America, [3]Hulk, 
len=3, cap=3, value=[0]Thor, [1]Captain America, [2]Hulk, 
len=2, cap=2, value=[0]Captain America, [1]Hulk, 
len=1, cap=2, value=[0]Captain America, 
```

上面範例除了第一個切片是從陣列生成外，其餘的三個切片都是由切片生成。由此可知，切片是可以再被切成更小，當使用 `[ : 1 ]` 生成切片時，新切片容量會變小，因為開始位置往後移動一格。



## 切片宣告

切片除了可以從現有的陣列或切片生成，也可以直接宣告一個新的切片，預設值為 `nil`。若宣告有指定初始值，就會產生一個新的陣列，位於切片底層，陣列長度取決於初始化內容，如下:

```go
// 宣告一個字串切片
var strSlice []string
fmt.Printf("len=%d, cap=%d, values=%v \n", len(strSlice), cap(strSlice), strSlice)

// 宣告一個整數切片
var intSlice []int
fmt.Printf("len=%d, cap=%d, values=%v \n", len(intSlice), cap(intSlice), strSlice)

// 宣告與初始化一個字串切片
strSlice2 := []string{"Iron Man"}
fmt.Printf("len=%d, cap=%d, values=%v \n", len(strSlice2), cap(strSlice2), strSlice2)

// 宣告與初始化一個整數切片
intSlice2 := []int{2019, 2020}
fmt.Printf("len=%d, cap=%d, values=%v \n", len(intSlice2), cap(intSlice2), intSlice2)
```

執行結果:

``` bash
len=0, cap=0, values=[] 
len=0, cap=0, values=[] 
len=1, cap=1, values=[Iron Man] 
len=2, cap=2, values=[2019 2020]
```



## 動態建立切片

如果不想要宣告切片，我們也可以用 `make( )` 動態建立一個切片，建立時可以指定長度和容量，格式有以下兩種:

- 長度和容量相同時:  make( `[]T`, `len and cap` ) 
- 長度與容量不同時: make( `[]T`, `len`, `cap` ) 

``` go
// 建立一個長度為 2 容量為 2 的切片
slice1 := make([]string, 2) 
slice1[1] = "Bat Man"
printHeros(slice1)

// 建立一個長度為 2 容量為 5 的切片
slice2 := make([]string, 2, 5)
slice2[1] = "Super Man"
printHeros(slice2)
```

執行結果:

``` go
len=2, cap=2, value=[0], [1]Bat Man, 
len=2, cap=5, value=[0], [1]Super Man, 
```



## 切片擴增

由於切片的底層是一個固定長度的陣列，當我們要新增資料時，一定會受限於原始陣列的長度。因此，當我們要動態增加資料時，可以用 `append( )` 來附加一個新元素，如果現有空間不足時，切片就會自動擴增容量: 

``` go
func main() {
    // 宣告一個長度和容量皆為 0 的字串切片
	slice := make([]string, 0)

	slice = append(slice, "IronMan") // 動態附加一個字串
	printHeros(slice)

	slice = append(slice, "BatMan") // 動態附加一個字串
	printHeros(slice)

	slice = append(slice, "SuperMan") // 動態附加一個字串
	printHeros(slice)
}

func printHeros(slice []string) {
	fmt.Printf("len=%d, cap=%d, value=", len(slice), cap(slice))

	for k, v := range slice {
		fmt.Printf("[%d]%s, ", k, v)
	}
	fmt.Println()
}
```

執行結果:

``` bash
len=1, cap=1, value=[0]IronMan, 
len=2, cap=2, value=[0]IronMan, [1]BatMan, 
len=3, cap=4, value=[0]IronMan, [1]BatMan, [2]SuperMan, 
```

我們從上面的結果可以發現，每次執行 append 時候都因容量不足而擴增，並且以目前容量 2 倍數進行擴增。



# 小結

今天介紹了切片的用法，go語言的切片實在是非常好用，和陣列相比起來，多了許多靈活與簡潔的用法。最重要的一點就是，切片解決了陣列固定大小的問題，使我們可以動態配置及操作連續空間。
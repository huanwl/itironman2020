大家好，今天是鐵人賽第十天。今天我要來介紹go語言的映射，它和切片一樣是抽象的容器型別，底層也是用陣列來實作。映射與陣列切片最大差別在於，映射可以使用非整數索引型別及不連續的索引值，這是因為映射的底層是用雜湊表(Hash table)實作。



# 映射

映射(map)在一些語言裡稱為字典(dictionary)，指的是以鍵-值(key-value)存取的資料容器。映射也是一種型別，我們可以直接宣告它，初始值為nil:

``` go
// 宣告一個字串為鍵值的映射
var emptyMap map[string]
fmt.Printf("len=%d, value=%v \n", len(emptyMap), emptyMap)

// 宣告一個字串為鍵及整數為值的映射，並指定初始值
var ageOfHeros = map[string]int{"IronMan": 30, "Dr.Strange": 45}
fmt.Printf("len=%d, value=%v \n", len(ageOfHeros), ageOfHeros)
```

執行結果:

``` bash
len=0, value=map[] 
len=2, value=map[Dr.Strange:45 IronMan:30] 
```



## 映射的存取

映射的存取和陣列相似，可以透過 `[ ]` 來存取資料，如下:

```go
var ageOfHeros = map[string]int{"IronMan": 30, "Dr.Strange": 45}

age1 := ageOfHeros["IronMan"]
fmt.Println("IronMan:", age1)

age2 := ageOfHeros["Thor"]
fmt.Println("Thor:", age2)

ageOfHeros["Hulk"] = 52
age3 := ageOfHeros["Hulk"]
fmt.Println("Hulk:", age3)

fmt.Printf("len=%d, value=%v \n", len(ageOfHeros), ageOfHeros)
```

執行結果:

```bash
IronMan: 30
Thor: 0
Hulk: 52
len=3, value=map[Dr.Strange:45 Hulk:52 IronMan:30] 
```

從上面結果來看，我們可以發現在映射中沒有Thor這筆資料，取出來的是型別預設值 0。

另外，我們也可以透過 `for` 印出映射的內容:

``` go
// 招喚四個漫威英雄
var ageOfHeros = map[string]int{"IronMan": 30, "Dr.Strange": 45, "Hulk": 52, "Thor": 66}

for k, v := range ageOfHeros {
    fmt.Println(k, v)
}
```

執行結果:

```bash
Thor 66
IronMan 30
Dr.Strange 45
Hulk 52
```

這邊要注意的是，映射巡覽的順序不一定會和初始值或資料插入的順序一致，因為它的索引值是不連續的。



## 檢查是否存在

 一般來說，我們在用一個 key 讀取映射時，會先檢查這個 key 是否存在，然後再取得 value。在go語言也有提供這樣的作法:

```go
var ageOfHeros = map[string]int{"IronMan": 30}

// 第二個回傳值為 bool 型別，表示是否存在
age, ok := ageOfHeros["Dr.Strange"]
if ok {
    fmt.Println("Dr.Strange:", age)
}
```

即使我們已經知道對映射讀取不存在的 key 會回傳預設值，但如果用預設值來判斷資料是否存在，這樣的作法並不適合，真實的資料很有可能和預設值相同。



## 動態建立映射

由於映射與切片一樣是go語言內建的抽象容器，所以我們也可以用 `make()` 來動態建立一個新的映射:

``` go
var ageOfHeros = make(map[string]int)

ageOfHeros["Dr.Strange"] = 45

age, ok := ageOfHeros["Dr.Strange"]
if ok {
    fmt.Println("Dr.Strange:", age)
}
// 印出: Dr.Strange: 45
```



## 刪除映射鍵值

由於映射裡的資料是分散的，無法像陣列可以用元素覆蓋的方式刪除資料。因此，go語言提供了`delete()` 函式，用來刪除已存在的鍵值:

```go
var ageOfHeros = map[string]int{"IronMan": 30, "Dr.Strange": 45}

// 刪除存在的key
delete(ageOfHeros, "IronMan")
delete(ageOfHeros, "Dr.Strange")

// 刪除不存在的key不會造成錯誤
delete(ageOfHeros, "Thor")

fmt.Printf("len=%d, value=%v \n", len(ageOfHeros), ageOfHeros)
// 印出: len=0, value=map[] 
```



 # 小結

今天介紹了go語言的映射用法，這種資料結構在很多語言都有被實作，像是C#的Dictionary。而使用映射的目的，除了能表達複雜形式的資料(ex: JSON)外，也可以提高查詢效能，像是快取資料通常也是基於映射的資料結構。


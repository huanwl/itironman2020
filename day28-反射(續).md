大家好，今天是鐵人賽第二十八天。今天繼續講反射。



# 反射值

反射除了可以獲取變數的型別資訊外，也可以獲取變數內部的值。

反射值的方式有兩種：

1. 先取出空介面，再用斷言來轉成指定型別。
2. 基本型別可以直接取出。

```go
func main() {
	var a int = 2019

    // 取出反射值對象
	valueOfA := reflect.ValueOf(a)

    // 方式一
	getA := valueOfA.Interface().(int)

    // 方式二
	getA2 := valueOfA.Int()

	fmt.Println(getA, getA2)
}

/* 執行結果：
2019 2019
*/
```

反射值的型別是 [Value](https://golang.org/pkg/reflect/#Value) 。



## 反射函式與呼叫

既然反射可以獲取變數的值，那如果是一個函式變數，不就可以在取出值後呼叫它？沒錯，不但可以反射呼叫函式，還可以指定參數。

```go
func add(a, b int) int {
	return a + b
}

func main() {
	funcValue := reflect.ValueOf(add)

    // 建立函式參數
	paramList := []reflect.Value{reflect.ValueOf(10), reflect.ValueOf(20)}

    // 反射叫函式
	results := funcValue.Call(paramList)

	fmt.Println(results[0].Int())
}

/* 執行結果：
30
*/
```



## 反射結構欄位值

結構體的欄位值也可以被反射獲得，甚至是巢狀結構也有辦法獲取內部結構的欄位值，如下所示：

```go
type Hero struct {
	Name    string
	Age     int
	Partner *Hero
}

func main() {
	tony := Hero{
		Name:    "Iron Man",
		Age:     30,
		Partner: &Hero{Name: "Thor", Age: 25},
	}

	valueOfTony := reflect.ValueOf(tony)

    // 用索引獲取值
	fmt.Println(valueOfTony.Field(0))

    // 用名稱獲取值
	fmt.Println(valueOfTony.FieldByName("Age"))

    // 用索引陣列獲取巢狀欄位值
	fmt.Println(valueOfTony.FieldByIndex([]int{2, 0}))
}

/* 執行結果：
Iron Man
30
Thor
*/
```





## 反射結構方法與呼叫

反射結構也可以獲取方法，並且用反射方式呼叫它，只是要非常小心指標的部份：

```go
type Hero struct {
	Name    string
	Age     int
	Partner *Hero
}

func (h *Hero) GetName() string {
	return h.Name
}

func main() {
	tony := &Hero{
		Name:    "Iron Man",
		Age:     30,
		Partner: &Hero{Name: "Thor", Age: 25},
	}

	valueOfTony := reflect.ValueOf(tony)

    // 獲取方法並呼叫
	fmt.Println(valueOfTony.Method(0).Call([]reflect.Value{}))

    // 先取出內部結構欄位，再獲取方法並呼叫
	fmt.Println(valueOfTony.Elem().Field(2).Method(0).Call([]reflect.Value{}))
}

/* 執行結果：
[Iron Man]
[Thor]
*/
```

當反射值為指標時，就需要判斷是否使用 `Elem()` 取出指向的內容。




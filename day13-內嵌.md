大家好，今天是鐵人賽第十三天。在前兩天內容中，我們了解go語言的結構可以封裝資料，以及定義方法。至於今天我們要來談的是，結構該如何共享程式碼。

在物件導向程式中，通常會用繼承來共享上層元件的程式碼。然而，go語言沒有繼承的特性，但我們能用組合的方式來共享程式碼。不僅如此，go語言還提供了一種優於組合的語法，稱作內嵌。

 

# 組合(composition)

先來談談我所知道的組合，大部分的文章會講到組合是聚合(aggregation)的一種，而它們都是源自於UML的產物，實際上UML定義的定義很模糊也很難理解。因此，我要講的是它們最基本的一面，也就是 `Is-A` 和 `Has-A` 關係: 

- Is-A: 繼承關係，表示一個物件也是另一個物件。
- Has-A: 組合關係，表示一個物件擁有另一個物件。

很多文章和書都建議我們要多用**組合少用繼承**，這是因為繼承會對物件造成巨大的依賴關係。我們用一個範例來說明組合:

```go
// 定義一個英雄結構，包含了正常人結構
type Hero struct {
	Person   *Person
	HeroName string
	HerkRank int
}

// 定義一個正常人結構
type Person struct {
	Name string
}

func main() {
	var tony = &Hero{&Person{"Tony Stark"}, "Iron Man", 1}
	fmt.Printf("Hero=%+v\n", *tony)
	fmt.Printf("Person=%+v\n", *(tony.Person))
}
```

執行結果:

```bash
Hero={Person:0xc0000841e0 HeroName:Iron Man HerkRank:1}
Person={Name:Tony Stark}
```

上面範例中，我們看到了所謂的組合就是結構再包結構的概念，透過這樣的方式共享結構資料或方法。



# 內嵌(Embedding)

再來談談go語言的內嵌特性，這個特性並沒有寫在**A Tour of Go**，而是在**Effective Go**裡頭。

Effective Go: [Embedding](https://golang.org/doc/effective_go.html#embedding) 

Go語言的內嵌其實就是組合的概念，只是它更加簡潔及強大。內嵌允許我們在結構內組合其他結構時，不需要定義欄位名稱，並且能直接透過該結構叫用欄位或方法。我們將上面的範例改成使用內嵌，如下:

```go
// 定義一個英雄結構
type Hero struct {
	*Person   // 不需要欄位名稱
	HeroName string
	HerkRank int
}

// 定義一個正常人結構
type Person struct {
	Name string
}

func main() {
	var tony = &Hero{
		&Person{"Tony Stark"},
		"Iron Man",
		1}

	fmt.Printf("%s\n", tony.Name)    // 直接叫用內部結構資料
	// 等於 fmt.Printf("%s\n", tony.Person.Name)
}
// 執行結果: Tony Stark
```

實際上，內嵌的結構欄位還是會有名稱，就是和結構本身的名稱同名。

另外，上面範例是用匿名初始化，也可以使用具名初始化，差別在於初始化參數的數量和順序是可以被調整的:

```go
var tony = &Hero{
		Person:   &Person{"Tony Stark"},
		HeroName: "Iron Man",
		HeroRank: 1}
```



## 內嵌與方法

上面看到的範例都是內嵌結構資料，現在我們來試試看內嵌結構方法，修改同一個範例如下:

```go
// 定義一個英雄結構
type Hero struct {
	*Person
	HeroName string
	HeroRank int
}

// 英雄都會飛
func (*Hero) Fly() {
	fmt.Println("I can fly.")
}

// 定義一個正常人結構
type Person struct {
	Name string
}

// 正常人會走路
func (*Person) Walk() {
	fmt.Println("I can walk.")
}

func main() {
	var tony = &Hero{
		Person:   &Person{"Tony Stark"},
		HeroName: "Iron Man",
		HeroRank: 1}

	tony.Walk()   // 等於 tony.Person.Walk()
	tony.Fly()
}
```

執行結果:

```bash
I can walk.
I can fly.
```



## 內嵌結構欄位同名

當有多個內嵌結構時，就有可能發生欄位同名的問題，我們修改一下上面的範例，加入一個寵物結構:

```go
// 定義一個英雄結構
type Hero struct {
	*Person
	*Pet
	HeroName string
	HeroRank int
}

// 定義一個正常人結構
type Person struct {
	Name string
}

// 定義一個寵物結構
type Pet struct {
	Name string
}

func main() {
	var tony = &Hero{
		Person:   &Person{"Tony Stark"},
		Parner:   &Parner{"Pepper"},
		HeroName: "Iron Man",
		HeroRank: 1}

	fmt.Printf("%s\n", tony.Name)
}
```

由於 Person 和 Parner 都有 Name 這個欄位，直接叫用 tony.Name 就會產生衝突，編譯器會顯示錯誤訊息:

```bash
./main.go:40:25: ambiguous selector tony.Name
```





# 小結

今天介紹了go語言的內嵌特性，依然可以有共享程式碼的效果。
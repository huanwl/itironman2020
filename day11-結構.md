大家好，今天是鐵人賽第十一天。在前幾天的文章中，我們大致上了解指標和容器的概念，而從今天開始，我們要進入物件導向的世界。很多人會問說go語言是不是物件導向語言？這個問題其實官方已經有回答了:

FAQ: [Is Go an object-oriented language?](https://golang.org/doc/faq#Is_Go_an_object-oriented_language)

簡單來說，由於go語言少了繼承的特性，因此我們只能稱作物件導向風格的語言。然而，繼承雖然有很好的共享實作及多型的特性，但它也帶來了不少問題，因為繼承類別屬於侵入式依賴，go語言主張以組合和介面來取代繼承。



# 結構(Struct)

疑？上面講了這麼多物件導向，怎麼今天不是應該要講類別(class)嗎？事實上，go語言沒有類別，只有結構(struct)，這又是一個從C語言來的東西，只是go的結構強大很多。

在go語言裡，結構的地位就如同Java/C#中的類別，可以封裝資料與行為。使用結構需要先用 `type` 定義型別，然後我們就可以用它宣告變數，如下:

```go
// 定義一個英雄結構型別，欄位有名稱、年齡、力量
type Hero struct {
    name             string
    age, power  int
}

// 宣告一個英雄
var andy Hero
fmt.Println(andy)

// 宣告一個英雄，並初始化所有欄位
var tony = Hero{"IronMan", 30, 666}
fmt.Println(tony)

// 宣告一個英雄，並初始化部份欄位
var stephen = Hero{name: "Dr.Strange"}
fmt.Println(stephen)
```

執行結果:

```go
{ 0 0}
{IronMan 30 666}
{Dr.Strange 0 0}
```

結構宣告的初始值可以指定欄位名稱，這樣能避免初始化所有欄位，尚未初始化的欄位就會是型別預設值。

然而，這樣的宣告方式會有一個問題：當傳遞結構給函式時，資料不會被修改。因此，如果是上面的範例，傳遞的結構會被複製一份到函式裡，除非回傳修改後的結構，不然無法修改原本的結構。

```go
type Hero struct {
	name       string
	age, power int
}

func main() {
	var tony = Hero{"IronMan", 30, 666}

	fmt.Println("Before change:", tony)

	changeHero(tony)

	fmt.Println("After change:", tony)
}

func changeHero(h Hero) {
	h.name = "Dr.Strange"
	h.age = 55
	h.power = 9999
}
```

執行結果:

```go
Before change: {IronMan 30 666}
After change: {IronMan 30 666}
```



## 結構與指標

在物件導向程式中，我們比較常用參考來傳遞物件，這樣可以確保物件是同一個。在go語言中，我們可以用指標來傳遞結構位址，達到傳參考的效果。

還記得 [day7-指標](https://ithelp.ithome.com.tw/articles/10214851) 嗎？我們可以用 `&` 取得變數的記憶體位址，而結構是可以在宣告的時候就使用 `&` 表示回傳一個位址，以下將前一個範例修改為指標版本:

```go
type Hero struct {
	name       string
	age, power int
}

func main() {
	var tony = &Hero{"IronMan", 30, 666}

	fmt.Println("Before change:", *tony)

	changeHero(tony)

	fmt.Println("After change:", *tony)
}

func changeHero(h *Hero) {
	h.name = "Dr.Strange"
	h.age = 55
	h.power = 9999
}
```

執行結果:

```go
Before change: {IronMan 30 666}
After change: {Dr.Strange 55 9999}
```

上面的結果中，我們發現結構內容被修改了，因為這次是傳遞指標參數，函式會透過記憶體位址修改原本的結構。



## new( )

Go語言也可以用 `new(結構名)` 建立結構，效果就和 `&結構名` 一樣 。但是用 `new` 就不能同時初始化結構裡的欄位:

```go
var tony = new(Hero)

tony.name = "IronMan"
tony.age = 30
tony.power = 666

fmt.Println(*tony)
```

執行結果:

```go
{IronMan 30 666}
```



## 匿名結構

Go的結構還有一個特殊的宣告方式-匿名結構，感覺有點像匿名函式。我們不用事先定義好結構型別，在變數宣告時產生一次性的結構型別，如下:

```go
// 一般變數宣告的匿名結構
var tony = &struct {
    name       string
    age, power int
}{"IronMan", 30, 666}

fmt.Println(*tony)

// 短變數宣告的匿名結構
stephen := &struct {
    name       string
    age, power int
}{name: "Dr.Strange"}

fmt.Println(*stephen)
```

執行結果:

```go
{IronMan 30 666}
{Dr.Strange 0 0}
```



# 小結

今天大概介紹結構的基本用法，明天還會繼續說明結構的其他用法。結構算是Go語言中最複雜的資料類型，需要分個好幾天才能說完它。今天就先到這裡了，明天見。


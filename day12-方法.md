大家好，今天是鐵人賽第十二天。昨天我們談到物件是用來封裝資料和行為，go語言可以透過定義及宣告結構型別來封裝物件資料。而今天要講的是，go語言中的物件行為要如何定義及操作。行為在物件導向程式中極為重要，它的本質其實就是物件裡的函式，我們通常稱作方法。



# 方法(Method)

我覺得go語言最特別的地方就是方法，和以往我學過的語言都不一樣。以Java/C#來說，方法的定義會寫在類別之中；雖然go語言沒有類別，但方法也不是寫在結構內，而是寫在外面，並且會透過接受者(receiver)來指定方法給一個結構型別。下面我們先來看一個範例:

```go
// 定義一個英雄結構型別，欄位有名稱、生命、力量
type Hero struct {
	name          string
	health, power int
}

// 定義一個英雄攻擊英雄的方法
func (h1 *Hero) Attack(h2 *Hero) {
	h2.health = h2.health - h1.power
}

func main() {
	// 召喚鋼鐵人
	var tony = &Hero{"IronMan", 1000, 250}
	fmt.Println("Create Hero1:", *tony)

    // 召喚奇異博士
	var stephen = &Hero{"Dr.Strange", 1000, 777}
	fmt.Println("Create Hero2:", *stephen)

    // 命令鋼鐵人去打奇異博士
	tony.Attack(stephen)
	fmt.Println("After attack:", *tony, *stephen)
}
```

執行結果:

```go
Create Hero1: {IronMan 1000 250}
Create Hero2: {Dr.Strange 1000 777}
After attack: {IronMan 1000 250} {Dr.Strange 750 777}
```

從執行結果可以看出在方法執行後，結構的資料有被修改，這是因為方法的參數或接收者是用指標來傳遞，如同昨天所講的一樣。



## 接收者

Go語言的接收者像是其他語言中的 **this** 或 **self**，它就是一個連結呼叫物件的變數。我們會在 `func` 和方法名稱之間定義它，語法如下:

```go
func (p Point) AddX(val float64) {
	p.X = p.X + val
}

type Point struct {
	X, Y float64
}

func main() {
	var p = &Point{1.1, 2.5}
	p.AddX(1.2)
	fmt.Println(p)
}
```

執行結果:

```go
{1.1 2.5}
```

上面範例中的發現一件有趣的事，我明明就是宣告一個指標變數，怎麼執行方法卻沒有修改資料？

這是因為方法是用傳值接受者，當go看到指標變數呼叫的方法是傳值接受者，這時候go會自動幫我們轉換:

```go
p.AddX(1.2)       // 這是語法糖
(*p).AddX(1.2)  // 實際的語法
```

因此，我們將上面的範例改成傳指標接受者，如下:

```go
func (p *Point) AddX(val float64) {
	p.X = p.X + val
}

type Point struct {
	X, Y float64
}

func main() {
	var p = &Point{1.1, 2.5}
	p.AddX(1.2)
	fmt.Println(p)
}
```

執行結果:

```go
{2.3 2.5}
```

這次資料就有被修改了，由於指標變數呼叫的方法是傳指標接受者，go也就不用幫我們轉換了。

另外，這個情形也是可以倒過來，範例如下:

```go
func (p *Point) AddX(val float64) {
	p.X = p.X + val
}

type Point struct {
	X, Y float64
}

func main() {
	var p = Point{1.1, 2.5}
	p.AddX(1.2)
	fmt.Println(p)
}
```

執行結果:

```go
{2.3 2.5}
```

這個範例很神奇的是，我們宣告的 p 是一般變數，它不是指標。但是當它在呼叫方法後，竟然修改了資料。原因就和前一個範例剛好相反，當go看到一般變數呼叫的方法是傳指標接收者，這時候go也會自動幫我們轉換:

```go
p.AddX(1.2)        // 這是語法糖
(&p).AddX(1.2)  // 實際的語法
```



## 方法與函式

Go語言的方法與函式有異曲同工之妙，我們其實也可以用函式來模擬方法。

```go
func AddX(p *Point, val float64) {
	p.X = p.X + val
}

type Point struct {
	X, Y float64
}

func main() {
	var p = Point{1.1, 2.5}
	AddX(&p, 1.2)
	fmt.Println(p)
}
```

執行結果:

```go
{2.3 2.5}
```

這樣寫好像也能定義物件行為，但有種說不出來的感覺。事實上，方法就是這樣的一個函式，只是程式語言通常會提供一個比較特別或比較潮的語法。

另外一點，使用函式的話就沒有上面提到的語法糖了，傳入時必須自己取值(` * `)或取址(` & `) 。



# 小結

今天介紹了go語言中的方法，可以透過接受者指定結構或其他型別。到目前為止，我覺得go語言最難學的地方還是在指標，幾乎每個地方都有分指標寫法和一般寫法。如果觀念不夠好，寫出來的程式一定Bug一堆。


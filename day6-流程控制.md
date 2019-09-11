大家好，今天是鐵人賽第六天，要來講go語言的流程控制(flow control)。在程式裡，每一行程式碼的執行順序就稱為流程，一般的程式流程是由程式碼的編寫順序從上而下依序執行。但是當我們在開發較複雜的程式時，常常需要選擇性的執行某行程式碼，或是重複執行某一段程式碼，這樣的行為就稱為流程控制。流程控制主要分為分支語法和循環語法兩種:



# 分支語法

分支是指用條件來選擇執行某一段程式碼，go語言提供 `if/else` 和 `switch case` 兩種語法。

## if / else


if/else可以說是所有程式最常用的邏輯判斷語法，go的用法也差不多，只是條件不再需要小括號( )，然後左大括號 ( { ) 必須要和 if 同一行，以及 else 和 else if 則必須和右大括號和左大括號同一行。

``` go
myAge := 30
if myAge < 13 {
    fmt.Println("Child")
} else if myAge >= 13 && myAge < 20 {
    fmt.Println("Teen")
} else {
    fmt.Println("Adult")
}
// 執行結果: Adult
```

如果大括號位置放不對，編譯就會錯誤，go語言在撰寫風格上規定的很嚴格，而且是強制性的。

條件也可以直接放一個布林變數，或是呼叫一個回傳布林的函式:

``` go
isHuman := true
if isHuman {
	fmt.Println("walk")
} else {
	fmt.Println("fly")
}
// 執行結果: walk
```

go語言的 if 還有一個特殊寫法，可以在條件之前加上一行表達式:

``` go
myAge := 30
if myAge = myAge - 15; myAge < 20 {
    fmt.Println("Teen")
} else {
    fmt.Println("Adult")
}
// 執行結果: Teen
```

這個條件前的表達式還可以透過 `:=` 宣告變數，後面的條件可以拿來使用。

``` go
if myAge := 30; myAge < 20 {
    fmt.Println("Teen")
} else {
    fmt.Println("Adult")
}
// 執行結果: Adult
```



## switch case

switch case也是一種很常見的語法，實際上它能做的事換成用 if/else 一樣也做得到，而我們會用switch case的目的也就是為了美觀，排版整齊、條件可以寫的比較少。

試著想想看當 if 和 else if 越來越多時，如下:

``` go 
myAge := 30
if myAge < 13 {
    fmt.Println("Child")
} else if myAge >= 13 && myAge < 20 {
    fmt.Println("Teen")
} else if myAge >= 20 && myAge < 50 {
    fmt.Println("Adult")
} else {
    fmt.Println("Elder")
}
// 執行結果: Adult
```

我們可以改成用 switch case 就會變得簡潔許多:

``` go
myAge := 30
switch {
case myAge < 13:
    fmt.Println("Child")
case myAge >= 13 && myAge < 20:
    fmt.Println("Teen")
case myAge >= 20 && myAge < 50:
    fmt.Println("Adult")
default:
    fmt.Println("Elder")
}
// 執行結果: Adult
```

上面的範例可以看出go的switch case比較特殊，允許case帶條件式。一般來說，在C語言與其他大部份語言中的switch case是只能帶一個值，go也有保留這樣的寫法:

``` go
flag := 3
switch flag {
case 1:
	fmt.Println("First")
case 2:
	fmt.Println("Second")
case 3:
	fmt.Println("Third")
default:
	fmt.Println("Other")
}
// 執行結果: Third
```

傳統上，case只能帶值的寫法，讓switch case少了很多出場機會。而go語言改良了許多，除了現在看到的case帶條件以外，還可以帶多個值:

``` go
flag := 1
switch flag {
case 0, 1:
    fmt.Println("Zero - First")
case 2, 3, 4:
    fmt.Println("Second - Four")
default:
    fmt.Println("Other")
}
// 執行結果: Zero - First"
```

最後，有一個重點一直沒有提到，那就是每個case結束後不用再寫 `break` 了，go語言的case執行完就會直接離開switch block，這真是一個非常棒的改良。因為大部分的情境裡， case完成後都是要直接離開的，在C語言裡就要用 `break` 才能跳出switch。



# 循環語法

循環是指重複執行一段程式碼，直到滿足條件後才結束。如果要讓程式碼重複執行多次會有兩個作法，一個是迴圈，另一個是遞迴。差別在於迴圈是用for關鍵字實現，而遞迴是用函式呼叫來實現。

## for

通常稱作for迴圈(for loop)，基本語法如下:

``` go
for i := 0; i < 10; i++ {
	fmt.Printf("%d", i)
}
// 執行結果: 123456789
```

在迴圈之中可以透過 `i++` 語法，在每次執行完成時，對變數 i 自動加 1，或是想要相反就用`i--` 語法，每次減少 1。注意一點，go語言沒有提供 `++i` 之類的語法。

回想一下C語言，我記得還有看過另一個跑迴圈關鍵字 `while`，但是在go語言中沒有它。但我們可以用for做出類似的寫法:

``` go
i := 0
for i < 10 {
    fmt.Printf("%d", i)
    i++
}
// 執行結果: 123456789
```

或是用for做一個無窮迴圈(沒有結束條件):

``` go
for {
    // 用 break 跳出迴圈
}
```

我們可以用雙重迴圈來完成更多事，下面的範例是用兩個for印出九九乘法表:

``` go
for i := 1; i <= 9; i++ {
    for j := 1; j <= i; j++ {
    	fmt.Printf("%d*%d=%d ", i, j, i*j)
    }
    fmt.Println()
}
```

執行結果:

``` bash
1*1=1 
2*1=2 2*2=4 
3*1=3 3*2=6 3*3=9 
4*1=4 4*2=8 4*3=12 4*4=16 
5*1=5 5*2=10 5*3=15 5*4=20 5*5=25 
6*1=6 6*2=12 6*3=18 6*4=24 6*5=30 6*6=36 
7*1=7 7*2=14 7*3=21 7*4=28 7*5=35 7*6=42 7*7=49 
8*1=8 8*2=16 8*3=24 8*4=32 8*5=40 8*6=48 8*7=56 8*8=64 
9*1=9 9*2=18 9*3=27 9*4=36 9*5=45 9*6=54 9*7=63 9*8=72 9*9=81 
```



# 小結

今天說明了go的流程控制語法，分別是if/else, switch case，以及for迴圈。go語言在迴圈語法上統一用for取代while，這一點也是不錯的設計，不用再去考慮要用那一種。事實上，除了這三種控制語法，go語言還有提供一種邪惡的語法，叫做 goto。據我所知，goto是一種來自低階語言的流程控制語法，並且已經被一些高階語言(java)移除或禁止使用。但應該是沒這麼恐怖，goto詳細的用法我也還沒時間研究，之後有時間再補上吧。












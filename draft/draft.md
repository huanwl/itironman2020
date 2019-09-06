## 短聲明變數
在函式中， := 簡潔賦值語句在明確類型的地方，可以用於替代 var 定義。
函式外的每個語法塊都必須以關鍵字開始（ var 、 func 、等等）， := 結構不能使用在函式外。

## 數值常數
數值常數是高精度的 值 。
一個未指定類型的常數由上下文來決定其類型。

``` go
package main

import "fmt"

const (
	Big   = 1 << 100
	Small = Big >> 99
)

func needInt(x int) int { return x*10 + 1 }
func needFloat(x float64) float64 {
	return x * 0.1
}

func main() {
	fmt.Println(needInt(Small))
	fmt.Println(needFloat(Small))
	fmt.Println(needFloat(Big))
} 
```

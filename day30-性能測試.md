大家好，今天是鐵人賽最後一天了，總覺得這一個月過得特別漫長，有幾天因為比較忙碌，就寫得比較短，之後我會找時間補上去。

最後一天再來講Go語言的測試工具，昨天介紹了單元測試，今天就來介紹性能測試。



# 性能測試(Benchmark)

性能測試就是在測試一段程式或一個函式的執行效能，包含CPU計算時間、記憶體佔用比例等。

Go語言工具內建性能測試工具，用法和單元測試工具相似，測試碼也是放在 `_test` 結尾的檔案裡，最好不要和單元測試混在一起，我們先來建立一個性能測試檔 `benchmark_test.go`，如下：

```go
package main

import (
	"testing"
)

func Benchmark_Add(b *testing.B) {
	n := 0
	for i := 0; i < b.N; i++ {
		n++
	}
	b.Log(n)
}
```

我們可以注意到幾件事：

1. 性能測試函式必須以 `Benchmark` 開頭。
2. 參數為 `b *testing.B` 。
3. `b.N` 為性能測試框架提供的測試次數，不能假設它的範圍。

上面測試碼用終端機執行 `go test -v -bench=. benchmark_test.go` ，結果如下：

```bash
$ go test -v -bench=. benchmark_test.go
goos: linux
goarch: amd64
BenchmarkAdd-8   	1000000000	         0.263 ns/op
--- BENCH: BenchmarkAdd-8
    benchmark_test.go:14: 1
    benchmark_test.go:14: 100
    benchmark_test.go:14: 10000
    benchmark_test.go:14: 1000000
    benchmark_test.go:14: 100000000
    benchmark_test.go:14: 1000000000
PASS
ok  	command-line-arguments	0.315s
```

指令中的 `-bench=. ` 表示執行檔案中的所有函式，也可以指定函式 `-bench=Benchmark_Add`。	



## 記憶體測試

先建立一個需要動態配置記憶體的函式：

```go
func Benchmark_Alloc(b *testing.B) {
	slice := make([]string, 0)

	for i := 0; i < b.N; i++ {
		slice = append(slice, "hello world")
	}

	b.Log(len(slice))
}
```

在性能測試的指令中加入 `-benchmem` 即可執行記憶體測試，如下：

```bash
$ go test -v -bench=Benchmark_Alloc -benchmem benchmark_test.go
goos: linux
goarch: amd64
Benchmark_Alloc-8   	16527759	        73.8 ns/op	      97 B/op	       0 allocs/op
--- BENCH: Benchmark_Alloc-8
    benchmark_test.go:24: 1
    benchmark_test.go:24: 100
    benchmark_test.go:24: 10000
    benchmark_test.go:24: 1000000
    benchmark_test.go:24: 11421886
    benchmark_test.go:24: 16527759
PASS
ok  	command-line-arguments	2.161s
```

性能測試結果就會多了兩個記憶體相關的指標。


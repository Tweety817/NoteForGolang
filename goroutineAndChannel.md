# Go -  Goroutine,Channel
###### tags: `Go`,`Goroutine`,`Channel`
## Goroutine、Channel 用途
Goroutine用於平行處理。也就是**非同步執行**。
執行時如果是單線程（Single Thread）的，那麼執行時只會用到一個 CPU，如果你的電腦有兩個以上的 CPU，那可以想辦法讓他們分工合作。
分工合作需要溝通，Channel用途在於可以讓不同的Goroutine去溝通。
## 如何宣告Channel
搭配make()使用。
```go=
import "fmt"

func main(){
     s := make(chan string) //宣告一個 channel 變數
     s <- "hello"           //寫入 channel (sender)
     val <- s               //讀取 channel (receiver)
     fmt.Println(val)
}
```
分辨讀寫非常容易，請看 <- 符號放在哪邊，**chan<- 指向自己就是寫，<-chan 離開自己就是讀**，相當好分辨，如果 func 內讀寫都需要使用，則不需要使用任何箭頭符號，但是我會建議把讀寫的邏輯都拆開不同的 func 處理，對於閱讀上非常有幫助。
```go=
Write(chan<- int)
Read(<-chan int)
ReadWrite(chan int)
```
## 範例
* 任務:加總數字
```go=
func Sum(numbers []int) int {
	total := 0
	for _, n := range numbers {
		total += n
	}
	return total
}
```
* 任務:分兩個CPU加總數字
```go=
func SumTwoParallel(numbers []int) int {
	mid := len(numbers) / 2

	ch := make(chan int)
	go func() { ch <- Sum(numbers[:mid]) }()
	go func() { ch <- Sum(numbers[mid:]) }()

	total := <-ch + <-ch
	return total
}

//結果 用兩個CPU運算比較快:
// > go test -bench=^BenchmarkSumTwoParallel$
// goos: darwin
// goarch: amd64
// BenchmarkSumTwoParallel-8        5000     300351 ns/op
```
## 確認有多少CPU
有多少CPU就全部教叫出來做事，可以使用 `runtime.NumCPU()` 取得 CPU 的數量，再把 slice 切割成 N 等份給不同的 goroutine 去加總，最後再統合到 total，這樣不管跑這段程式的電腦有幾個 CPU 都可以均勻分配。

```go=
func SumMaxParallel(numbers []int) int {
	nCPU := runtime.NumCPU()
	nNum := len(numbers)

	ch := make(chan int)
	for i := 0; i < nCPU; i++ {
		from := i * nNum / nCPU
		to := (i + 1) * nNum / nCPU
		go func() { ch <- Sum(numbers[from:to]) }()
	}

	total := 0
	for i := 0; i < nCPU; i++ {
		total += <-ch
	}
	return total
}
```
## 參考資料
https://larrylu.blog/golang-goroutine-parallel-processing-
https://blog.wu-boy.com/2020/01/when-to-use-go-channel-and-goroutine/
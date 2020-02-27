# Go - goroutine
###### tags: `Go`,`goroutine`
## 用途
平行處理。也就是**非同步執行**。
執行時如果是單線程（Single Thread）的，那麼執行時只會用到一個 CPU，如果你的電腦有兩個以上的 CPU，那可以想辦法讓他們分工合作。
```go=
package main

import (
	"fmt"
	"time"
)

func Print() {
	for i := 0; i < 10; i++ {
		fmt.Println(i)
		time.Sleep(1 * time.Second)
	}
}

func main() {

	go Print()

	for i := 1000; i < 1010; i++ {
		fmt.Println(i)
		time.Sleep(1 * time.Second)
	}
}
```
由上面範例可以看到，在 main 裡面的迴圈值跟 Print 裡面迴圈的值，交錯印出來。表示 Print 這個 func 跟 main 裡面，是平行處理的。

## 主線程、子線程
```go=
package main

import (
	"fmt"
	"time"
)

func Print() {
	for i := 0; i < 10; i++ {
		fmt.Println(i)
		time.Sleep(1 * time.Second)
	}
}

func main() {
	go Print()
	time.Sleep(1 * time.Second)
	fmt.Println("Hello world")
}
// 0
// 1
// Hello world
```
在這邊可以看到 Print 裡面的值，只印出了幾個就中斷了。原因是，任何情況下，**只要 main thread (主線程) 中斷或結束，所有的子線程(goroutine)都會中斷。**

## 得知Goroutine執行結束
有沒有辦法知道 Goroutine 執行結束呢？實際上沒有任何方法可以得知，除非你主動設計一種機制，可以在 Goroutine 結束時執行通知，使用 **Channel** 是一種方式，這在之後的文件再說明，這邊先說明另一種方式，也就是使用 **sync.WaitGroup**。
```go=
package main

import (
    "fmt"
    "math/rand"
    "sync"
    "time"
)

func random(min, max int) int {
    rand.Seed(time.Now().Unix())
    return rand.Intn(max-min) + min
}

func tortoise(totalStep int, wg *sync.WaitGroup) {
    defer wg.Done()

    for step := 1; step <= totalStep; step++ {
        fmt.Printf("烏龜跑了 %d 步...\n", step)
    }
}

func hare(totalStep int, wg *sync.WaitGroup) {
    defer wg.Done()

    flags := [...]bool{true, false}
    step := 0
    for step < totalStep {
        isHareSleep := flags[random(1, 10)%2]
        if isHareSleep {
            fmt.Println("兔子睡著了zzzz")
        } else {
            step += 2
            fmt.Printf("兔子跑了 %d 步...\n", step)
        }
    }
}

func main() {
    wg := new(sync.WaitGroup)
    wg.Add(2)

    totalStep := 10

    go tortoise(totalStep, wg)
    go hare(totalStep, wg)

    wg.Wait()
}
```
有個 **runtime.GOMAXPROCS() 函式，可以設定 Go 同時間能使用的 CPU 數量**，它會傳回上一次設定的數字，如果傳入小於 1 的值，不會改變任何設定。
因此，可以使用 runtime.GOMAXPROCS(0) 知道目前的設定值。
想在執行時期得知可用的 CPU 數量，可以使用 runtime.NumCPU() 函式，因此，為了確保 Go 會使用全部的 CPU 來運行，可以這麼撰寫：`runtime.GOMAXPROCS(runtime.NumCPU()) `

除了透過 runtime.GOMAXPROCS() 設定之外，也可以透過環境變數 GOMAXPROCS 來設置，實際上，Go 1.5 已經預設會使用所有的 CPU 核心，不過，仍可以透過 runtime.GOMAXPROCS() 函式或環境變數來改變設定。

## 參考資料
https://openhome.cc/Gossip/Go/Goroutine.html

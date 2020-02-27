# Go - Channel
###### tags: `Go`,`Channel`
## Channel是什麼?
Goroutine用於平行處理。也就是**非同步執行**。
執行時如果是單線程（Single Thread）的，那麼執行時只會用到一個 CPU，如果你的電腦有兩個以上的 CPU，那可以想辦法讓他們分工合作。
分工合作需要溝通，Channel用途在於可以讓不同的Goroutine去溝通。
## Unbuffered Channel
保證讀寫都需要執行完畢才可以結束主程式。
## 範例
```go=
func main() {
    go func() {
        fmt.Println("GO GO GO")
    }() //立即執行的函式
    time.Sleep(1 * time.Second)
}
```
上面的例子在執行完 main 函數，就會直接跳出，所以後面設定了等待一秒可以解決此問題，但是一般開發模式不會加上 Timeout，這邊該如何透過 Unbuffered Channel 方式來達到一樣的效果。
```go=
func main() {
    c := make(chan bool) 
    go func() {
        fmt.Println("GO GO GO")
        c <- true //寫channel值
    }()
    <-c //讀channel值
}
```
可以看到上面我用了 make(chan bool) 來建立一個 channel，當在 main 函數最後用** <-c 代表需要等待讀出一個 channel 值**，main 函數才會結束，這時候就達到了跟用 Sleep 一樣的效果，接著將程式碼改成如下:
```go=
func main() {
    c := make(chan bool)
    go func() {
        fmt.Println("GO GO GO")
        <-c //讀channel值
    }()
    c <- true //寫channel值
}
```
你會發現得到同樣的結果，為什麼呢？因為 unbufferd channel 的用途就是，今天在程式碼內丟了一個值進去 channel，這時候 main 函式就需要等到一個 channel 值被讀出來才會結束，所以不管你在 goroutine 內讀或寫，main 都需要等到一個寫一個讀完成後才會結束程式。這就是用 unbuffered channel 來達到同步的效果，也就是保證讀寫都需要執行完畢才可以結束主程式。

## buffered Channel
和Unbuffered Channel宣告方式不同。
buffered channel 是透過 make() 後面有帶容量值，開發者可以一次宣告此 channel 可以容納幾個值。
```go=
func main() {
    c := make(chan bool, 1)
    go func() {
        fmt.Println("GO GO GO") //不會被print出來
        <-c
    }()
    c <- true //寫值進去就結束 
}
```
大家可以執行看看上述程式碼，會發現完全沒有輸出東西，原因是什麼，**buffered channel 就是只要容量有多少，你都可以一直塞值進去，但是不用讀出來沒關係，所以當丟了 c <- true 進去後，主程式不會等到讀出來才結束**，造成使用者沒有看到 fmt.Println("GO GO GO")，這就是最大的差異。

```go=
func main() {
    c := make(chan bool, 1)
    go func() {
        fmt.Println("GO GO GO") //會被print出來
       c <- true //寫值進去就結束 
    }()
    <-c //沒這裡的事
} 
```
## 差異
unbuffered channel 就是代表在主程式內，需要等到讀或寫都完成，main 才可以完整結束 (讀跟寫 buffered channel 需要在不同的 goroutine 才不會被 block)，而 buffered channel 相反，你可以一直丟資料進去 Channel 內，但是不需要讀出來（前提是 buffered channel 空間夠大不會爆掉），所以 main 才提前結束。如果您想要用 channel 做到同步或異步的效果，這邊就需要注意了。

## 參考資料
https://blog.wu-boy.com/2019/04/understand-unbuffered-vs-buffered-channel-in-five-minutes/
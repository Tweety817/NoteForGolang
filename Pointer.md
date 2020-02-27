# GO - Pointer
###### tags: `Go`,`Pointer`
## Pointer
當我們在呼叫一個函式時帶入一個參數，則這個參數會被複製到函式中：
```go=
func zero(x int) {
    x = 0
}
func main() {
    x := 5
    zero(x)
    fmt.Println(x) // x is still 5
}
```

在這個程式中，zero 這個函式不會動到 main 函式中的 x 變數值，可是如果我們想要改，該怎麼做呢？一種方式是使用一個**特殊的資料型別，即所謂的指標（pointer）**：

```go=
func zero(xPtr *int) {
    *xPtr = 0
}
func main() {
    x := 5
    zero(&x)
    fmt.Println(x) // x is 0
}
```
**指標可以取得儲存變數值的記憶體位置，而不是變數值本身（或是指向某物的指標值）**。透過指標 (*int)，zero 函式可以修改原本的變數值。

## 指標的理解:
1. 一個變數指向記憶體中儲存某個值得地址，那麼這個變數是一個指標變數，個人是如此理解也就是說指標指的是地址，不是值。
2. 指標（Pointer）是程式語言中的一個物件，利用地址，它的值直接指向存在電腦儲存器中另一個地方的值。由於通過地址能找到所需的變數單元，可以說，地址指向該變數單元。因此，將地址形象化的稱為“指標”。意思是通過它能找到以它為地址的記憶體單元。
```go=
func PointStudy()  {   
    // 每一個變數在執行時都有一個地址（這個地址儲存著這個變數對應的值）  
    var temp int = 1   
    //  go語言得取地址符， temp 變數，通過&符號來獲取temp這個變數在內    存中的地址，我們將獲取到的或者地址賦值給一個指標變數 pointTemp ,型別*int   
    var pointTemp *int= &temp
   fmt.Println(temp)   
   fmt.Println(pointTemp)
}
//輸出結果
1
0xc00005a2d0
```
指標變數也是變數，變數、指標和地址三者的關係是：**每一個變數都有自己地址，每一個指標的值就是地址**（也就是這個指標通過fmt.Println輸出後一個地址）。

## The * and & operators
在 Go 語言中，指標是使用星號字元「*」表示，後面接著儲值的資料型別。
在 zero 函式中， `xPtr` 是一個指向 int 型別的指標。

`* `是用來提取（dereference）指標變數的值，提取一個指標可以存取指標所指向的內容值。當我們寫 `*xPtr = 0`，這表示我們將 int 型別的整數值 0 儲存在 xPtr 所參照的記憶體位置中。若我們試著改用 xPtr = 0，則我們在編譯程式時會發生錯誤，因為 xPtr 的型別不是 int，而是 *int，這個型別只能存入 *int 型別。

最後，我們使用 `& `運算符來取得變數本身的位址，`&x `會傳回一個 *int 型別（指向一個 int 整數），由於 x 的型別是 int，透過指標可以修改原本的變數內容。在 main 函式的 `&x` 與在 zero 函式的  `xPtr`  都參照到相同的記憶體位址。

## 參考資料
http://golang-zhtw.netdpi.net/08_pointers
https://www.jishuwen.com/d/2S04/zh-tw
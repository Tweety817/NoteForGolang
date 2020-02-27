# Go -  build and package
###### tags: `Go`
## package
以下程式碼，檔案取名為:`main.go`
```go=
package main

import "fmt"

func main() {
    fmt.Println("Hello, World")
    fmt.Println("哈囉！世界！")
}
```
**每個 .go 原始碼，都必須從 package 定義開始**，而對於包括**程式進入點 main 函式的 .go 原始碼，必須是在 package main 之中。**

以下程式碼，檔案取名為:`hello.go`
```go=
package main

import "hello"

func main() {
    hello.HelloWorld()
}
```
main.go 中要用到剛建立的 hello 套件中的 HelloWorld 函式，這時 package 的設定就會發揮一下效用，你得將 hello.go 移到 src/hello 目錄之中，也就是**目錄名稱必須符合 package 設定之名稱。**

每個原始碼開頭，只要 **package 設定的名稱都與目錄相符就可以**了。例如，你可以有個原始碼是 hello.go，位於 src/goexample 底下。

**一個 package 可以有數個原始碼檔案**，各自組織自己的任務，在執行 go install goexample 之後，上面兩個原始碼會在 pkg 目錄的 `$GOOS_$GOARCH 目錄`中產生 goexample.a 檔案。
## 目錄
你可以在套件目錄之前增加父目錄，例如，可以建立一個 src/cc/openhome 目錄，然後將方才的 hello.go 與 hi.go 移至該目錄之中，接著執行 go install cc/openhome/goexample，那麼，在 pkg 目錄的 `$GOOS_$GOARCH 目錄`中，會產生對應的 cc/openhome 目錄。

## 遠端套件
若你想將原始碼發佈給他人使用時就很方便，例如，你可以建立 src/github.com/JustinSDK 目錄，然後將方才的 goexample 目錄移到 src/github.com/JustinSDK 當中，這麼一來，顯然地，你的 main.go 就要改成：

```go=
package main

import "github.com/JustinSDK/goexample"

func main() {
    goexample.Hi()
    goexample.Hello()
}
```
也就是說，你可以直接將 /src/github.com/JustinSDK/goexample 當作檔案庫（repository）發佈到 Github，那麼，其他人需要你的原始碼時，有個很方便的 go get 指令可以用，我將這個範例發佈在 Github 的 JustinSDK/goexample 了，因此，你可以執行以下指令：
`go get github.com/JustinSDK/goexample`

## GOPATH
如果沒有設定 GOPATH 的話，Go 預設會是使用者目錄的 go 目錄，雖然目前 GOPATH 中只一個目錄，不過 GOPATH 中可以設定數個目錄，現在我的 go-exercise 目錄底下會有這些東西：
![](https://i.imgur.com/RxYofWO.png)

## Go get
go get 會自行判斷該使用的協定，以這邊的例子來說，就會使用 git 來複製檔案庫至 src 目錄底下，結果就是 src/github.com/JustinSDK 底下，會有個 goexample 目錄，其中就是原始碼，go get 在下載原始碼之後，就會開始進行編譯，因此，你也會在 pkg 目錄中的 `$GOOS_$GOARCH 目錄`底下，github.com/JustinSDK 中找到編譯好的 .a 檔案。

接著 **，你就可以如上頭的程式撰寫 import "github.com/JustinSDK/goexample" 來使用這個套件。**

當然，執行 go install main 的話，你的 pkg 目錄中的 `$GOOS_$GOARCH` 目錄，會有個 github.com/JustinSDK 目錄，裏頭放置著 goexample.a 檔案，而編譯出來的可執行檔，則會放置在 bin 目錄之中，此時，你的目錄應該會像是：
![](https://i.imgur.com/Sf9rLKQ.png)

## 呼叫權限
開頭的大寫:表示這是個公開的函式，可以在套件之外進行呼叫。
函式名稱是小寫，那麼會是套件中才可以使用的函式。

## go build
如果想**編譯原始碼為可執行檔，那麼可以使用 go build**，例如，直接在 go 目錄中執行 go build src/main/main.go，**就會在執行指令的目錄下，產生一個名稱為 main.exe 的可執行檔**，可執行檔的名稱是來自己指定的原始碼檔案主檔名，執行產生出來的可執行檔就會顯示 Hello, World。

你也可以建立一個 bin 目錄，然後執行 go build -o bin/main.exe src/main/main.go，這樣產生出來的可執行檔，就會被放在 bin 底下。

## go install
每次使用** go build，都是從原始碼編譯為可執行檔，這比較沒有效率**，如果想要**編譯時更有效率一些，可以使用 go install**，例如，在目前既有的目錄與原始碼架構之下，於 go 目錄中執行 go install hello 的話，你就會發現有以下的內容：
![](https://i.imgur.com/sxMMrff.png)

go install packageName 表示要安裝指定名稱的套件，**如果是 main 套件，那麼會在 bin 中產生可執行檔，如果是公用套件，那麼會在 pkg 目錄的 $GOOS_$GOARCH 目錄中產生 .a 檔案**

## go env 
go env 來查看 Go 使用到的環境變數

## import
在 import 時預設會使用套件名稱作為呼叫套件中函式等的前置名稱，你可以在 import 時指定別名。例如：

```go=
package main

import f "fmt"

func main() {
    f.Println("哈囉！世界！")
}
```
若指定別名時使用 .，就不需要套件名稱作為前置名稱，例如：

```go=
package main

import . "fmt"

func main() {
    Println("哈囉！世界！")
}
```
不能只是 import x "x" 來試圖只執行套件的初始函式，因為** Go 編譯器不允許 import 了某個套件而不使用，然而若指定別名時使用 _，則不會導入套件，只會執行套件的初始函式，也就是套件中使用 func init() **定義的函式。

## init
每個套件可以有多個 init 定義在各個不同的原始檔案中，套件被 import 時會執行。

若是 main 套件，則會在所有 init 函式執行完畢後，再執行 main 函式。

Go 執行套件初始化時，不會保證套件中多個 init 的執行順序。

## 參考資料:
https://openhome.cc/Gossip/Go/HelloWorld.html
https://openhome.cc/Gossip/Go/Package.html
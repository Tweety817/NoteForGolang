# Go - 為何需要指標
###### tags: `Go`,`Pointer`
## 說明
不使用指標的話，某些情況是沒法賦值給結構體的。
## 範例
```go=
package main

import (
	"fmt"
)


func Compute(a int) {
	a = 0
}
func main() {
	x := 5
	Compute(x) //0
	fmt.Println(x) //5
}
```
上面範例經過 Compute 這個 func ，x 值並無改變，因為在 golang ，運作行為是『複製』 變數傳進去，所以兩個變數是完全獨立且分開的。
```go=
package main

import (
	"fmt"
)

func Compute(a *int) {
	*a = 0
}
func main() {
	x := 5
	Compute(&x) //0
	fmt.Println(x)//0
}
```
上面範例為什麼 x，會有不一樣？ 是因為這時侯 golang 行為是傳送位址進去，所以在 Compute 裡面所改到的值是原始 x，所改到的值。

**golang 裡面 slice & map 都是 pointer型別**，所如果把它當作參數傳入使用，要注意。

## 利用OOP來理解*跟& operator
```go=
package main

import (
	"fmt"
)

type Member struct {
	Age  int
	Name string
}

//這可以類比成Class的構造函式
func (m *Member) GetAge() int {
	return m.Age
}
func (m *Member) GetName() string {
	return m.Name
}

func main() {
	mem := &Member{ //傳地址 讓上方構造函示根據記憶體地址去找到記憶體的值
		Age:  18,
		Name: "syhlion",
	}
	fmt.Printf("Member Name:%s, Age:%d", mem.GetName(), mem.GetAge())
}
```
## 參考資料
https://www.jishuwen.com/d/2DBs/zh-tw
https://ithelp.ithome.com.tw/articles/10204330
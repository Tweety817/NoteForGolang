# Go - interface
###### tags: `Go`,`interface`

## interface是什麼?
golang 的 interface，我覺得對初學者來說會有點疑惑，畢竟 interface 在這裡有兩個意思，一個代表『泛用型別』、一個代表類似傳統 OOP(Object-oriented programming) 所說的 interface，下面就來介紹兩種使用方式。

**介面是一種型態，它定義了宣告但沒有實作。**

## 泛用型別
interface{} 如果在後面加上兩個大括號，可以把它當作一種 type 使用。
在強型別語言中，如果濫用這個機制，會吃到不少苦頭。推薦的使用情境都是用在面對一個**型別不是太穩定的 api 需求**，會使用這種型別設計。

```go=
type Logger interface {
  Log(message string)
}
```
你可能會覺得這樣有什麼用處？**介面可以讓你的程式碼從實作中去耦合**。例如，你可能會有很多種不同的 loggers：
* 範例1:
```go=
type SqlLogger struct { ... }
type ConsoleLogger struct { ... }
type FileLogger struct { ... }
```
* 範例2:
```go=
package main

import (
	"fmt"
)

func main() {
	var a interface{}
	var b interface{}
	var c interface{}

	a = 5
	b = 1.5
	c = "hello world"
	fmt.Println(a, b, c)
}
```

## 不用顯示聲明
golang 的 interface 跟其他 OOP(Object-oriented programming) 不太一樣，它不需要『顯示聲明』
```go=
package main

import (
	"fmt"
)

type Member interface {
	GetName() string
	GetAge() int
}

type Robot struct {
	name  string
	age   int
	power int
}

func (r *Robot) Work() {
}

func (r *Robot) GetName() string {
	return r.name
}
func (r *Robot) GetAge() int {
	return r.age
}

type Car struct {
	name     string
	age      int
	odometer int
}

func (r *Car) Run() {
}

func (r *Car) GetName() string {
	return r.name
}
func (r *Car) GetAge() int {
	return r.age
}

func Cleaning(m Member) {
	fmt.Printf("Consumer Name:%s, Age:%d\n", m.GetName(), m.GetAge())
}

func main() {
	r := &Robot{
		name:  "GUMDAM",
		age:   1,
		power: 100,
	}
	c := &Car{
		name:     "BENZ",
		age:      2,
		odometer: 100,
	}
	Cleaning(c)
	Cleaning(r)
}
```

由上面可以看到，我建構了 Robot struct 還有 Car struct，這兩個完全不同類型的 struct，竟然可以丟進去 Cleaning func，就是因為他們兩個都有實作， Member interface，**在 golang 裡面 interface 的定義是隱性的，不用刻意像其他語言一樣要使用 implement 之類的 keyword，來顯示宣告**。
**它只要有實作該 interface 所規範的 func ，即可定義它有實作。**

* 好處:
這種在接外部 package 時非常好用，我可以無痛用我的 struct ，丟到別人定義的 api 裡面。

## 其他語言的顯示聲明
在 C# 或 Java 中，當一個類別實作一個介面時，並需要明確的定義：

```csharp=
public class ConsoleLogger : Logger {
  public void Logger(message string) {
    Console.WriteLine(message)
  }
}
```
# Go - 循環引用
###### tags: `Go`
## 循環引用錯誤
當你開始撰寫更複雜的系統時，你一定會遇到循環引用的問題。當 A 套件要引用 B 套件，但 B 套件又引用 A 套件時就會發生這樣的狀況(不管是直接引用或是透過其他套件間接引用)。這種情況編譯器是不會允許的。
```go=
package shopping

import (
  "shopping/db"
)

type Item struct {
  Price float64
}

func PriceCheck(itemId int) (float64, bool) {
  item := db.LoadItem(itemId)
  if item == nil {
    return 0, false
  }
  return item.Price, true
}
```

## 為何編譯器不允許

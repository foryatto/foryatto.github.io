- [输入](#输入)
- [进制转换](#进制转换)
- [排序](#排序)
- [类型转换](#类型转换)
- [容器](#容器)
  - [双向链表](#双向链表)
    - [初始化](#初始化)
    - [Push](#push)
    - [Front()、Back()、Len()](#frontbacklen)
    - [Remove](#remove)
    - [插入, 移动](#插入-移动)
  - [heap](#heap)

# 输入

```go
func main() {

    var n int
    fmt.Scanf("%d", &n)

    in := bufio.NewScanner(os.Stdin)
    for in.Scan() {
        Println(in.Text())
    }

}
```

# 进制转换

```go
package main

import (
    "fmt"
    "strconv"
)

func main() {
    var input string
    fmt.Scanf("%s", &input)
    result, _ := strconv.ParseInt(input[2:], 16, 32) // 16 to 10
    Println(strconv.FormatInt(123, 2))      // 10 to 2
    Println(strconv.FormatInt(0b0010, 10))  // 2 to 10. (0x, 0o, 0b)(16, 8, 2)


    fmt.Println(result)
}
```

# 排序

调用sort.Sort()函数，实现以下接口：

```Go
package sort
type Interface interface {
    Len() int            // 获取元素数量
    Less(i, j int) bool // i，j是序列元素的指数。
    Swap(i, j int)        // 交换元素
}

// int排序 sort.Ints([]int{1, 2, 3})
// 字符串排序 sort.Strings([]string{"a", "b", "c"})
```

倒序:

```Go
func main() {

    data := []int{4, 2, 3, 1}

    sort.Sort(sort.Reverse(sort.IntSlice(data)))

    fmt.Println(data)

}
```

```go
// 对自定义类型的切片进行排序
// sort.Slice()

package main

import (
	"fmt"
	"sort"
)

type student struct {
	name  string
	score int
	index int
}

func main() {
	var n, sortType int
	fmt.Scanf("%d\n%d", &n, &sortType)
	studs := make([]*student, n)
	println(n, sortType)
	for i := 0; i < n; i++ {
		studs[i] = &student{}
		fmt.Scanf("%s %d", &studs[i].name, &studs[i].score)
		studs[i].index = i
	}
	if sortType == 0 {
		sort.Slice(studs, func(i, j int) bool {
			if studs[i].score != studs[j].score {
				return studs[i].score > studs[j].score
			}
			return studs[i].index < studs[j].index
		})
	} else if sortType == 1 {
		sort.Slice(studs, func(i, j int) bool {
			if studs[i].score != studs[j].score {
				return studs[i].score < studs[j].score
			}
			return studs[i].index < studs[j].index
		})
	}
	for i := 0; i < n; i++ {
		fmt.Printf("%s %d\n", studs[i].name, studs[i].score)
	}
}

```

# 类型转换

```Go
// 字符串转数字 num, _ := strconv.Atoi("123") 
// num, _ := strconv.ParseInt("123", 10, 32)

// 数字转字符串 str := strconv.Itoa(123)
```

# 容器

go自带了3个容器类型：list（双向链表）、heap（堆）、ring（圈）

## 双向链表

```Go
// 链表的一个元素 
type Element struct { 
    next, prev *Element // 前后指针 
    list *List // 所属链表
    Value any // 值 
}

// 链表
type List struct {
    root Element // 哨兵元素
    len int // 链表元素个数 
}
```

### 初始化

List支持延迟初始化，因此不管你使用list.New()创建一个已经初始化的list，或者直接使用list.List{}创建一个未初始化的list，都可以正常运行。

在调用PushFront()、PushBack()、PushFrontList()、PushBackList()时会调用 **lazyInit()** 检查是否已经初始化，如果没有初始化则调用 **Init()** 进行初始化。

```Go
package main

import (
   "container/list"
   "fmt"
)

func main() 
   // 使用list.New()直接初始化
   l1 := list.New()
   l1.PushFront(1)
   fmt.Println(l1.Front().Value) // 1

   // 使用list.List{}延迟初始化
   l2 := list.List{}
   l2.PushFront(2)
   fmt.Println(l2.Front().Value) // 2
}
```

### Push

PushFront()、PushBack()分别是在链表头部或尾部添加元素，PushFrontList()、PushBackList()分别是在链表头部或在尾部插入链表

```Go
package main

import (
   "container/list"
   "fmt"
)

func main() {
   // 新建三个只有一个节点的链表
   l1 := list.New()
   l1.PushFront(1)
   l2 := list.New()
   l2.PushBack(2)
   l3 := list.New()
   l3.PushBack(3)

   // 把l1和l3分别链到l2的前面和后面
   l2.PushFrontList(l1)
   l2.PushBackList(l3)

   // 打印
   PrintlnList(l2)
   //1
   //2
   //3
}

func PrintlnList(l *list.List) {
   if l.Front() == nil {
      return
   }
   for cur := l.Front(); cur != nil; cur = cur.Next() {
      fmt.Println(cur.Value)
   }
}
```

### Front()、Back()、Len()

分别是获取头元素、获取尾元素、获取长度，都不会对链表修改。

```Go
package main

import (
   "container/list"
   "fmt"
)

func main() {
   // 新建一个有三个节点的链表
   l := list.New()
   l.PushBack(1)
   l.PushBack(2)
   l.PushBack(3)
   fmt.Println(l.Front().Value) // 1
   fmt.Println(l.Back().Value)  // 3
   fmt.Println(l.Len())         // 3
}
```

### Remove

```Go
package main

import (
   "container/list"
   "fmt"
)

func main() {
   // 新建链表
   l := list.New()
   l.PushBack(1)
   e3 := l.PushBack(3)
   l.PushBack(4)

   // 移除e3节点
   l.Remove(e3)
}
```

### 插入, 移动

InsertBefore()、InsertAfter()
MoveBefore()、MoveAfter()、MoveToFront()、MoveToBack()

```Go
package main

import (
   "container/list"
   "fmt"
)

func main() {
   // 新建一个有三个节点的链表
   l := list.New()
   l.PushBack(1)
   e3 := l.PushBack(3) // 这里故意把3这个节点存下来
   l.PushBack(5)

   PrintlnList(l)
   //1
   //3
   //5

   l.InsertBefore(2, e3) // 在e3前面插入2
   l.InsertAfter(4, e3)  // 在e3后面插入4
   PrintlnList(l)
   //1
   //2
   //3
   //4
   //5
}

func PrintlnList(l *list.List) {
   if l.Front() == nil {
      return
   }
   for cur := l.Front(); cur != nil; cur = cur.Next() {
      fmt.Println(cur.Value)
   }
}
```

## heap

堆适合于需要数据有序，又需要能够随时添加或移除元素，最好是能够每次取最大或最小元素的场景。

```Go
package main

import (
   "container/heap"
   "fmt"
)

type IntHeap []int

func (h *IntHeap) Len() int {
   return len(*h)
}

func (h *IntHeap) Less(i, j int) bool {
   return (*h)[i] < (*h)[j]
}

func (h *IntHeap) Swap(i, j int) {
   (*h)[i], (*h)[j] = (*h)[j], (*h)[i]
}

func (h *IntHeap) Push(x any) {
   *h = append(*h, x.(int))
}

func (h *IntHeap) Pop() any {
   elem := (*h)[len(*h)-1]
   *h = (*h)[:len(*h)-1]
   return elem
}

func main() {
   var intHeap IntHeap
   heap.Init(&intHeap)
   heap.Push(&intHeap, 5)
   heap.Push(&intHeap, 3)
   heap.Push(&intHeap, 4)
   heap.Push(&intHeap, 2)
   heap.Push(&intHeap, 1)

   fmt.Println(heap.Pop(&intHeap)) // 1
   fmt.Println(heap.Pop(&intHeap)) // 2
   fmt.Println(heap.Pop(&intHeap)) // 3
   fmt.Println(heap.Pop(&intHeap)) // 4
   fmt.Println(heap.Pop(&intHeap)) // 5
}
```

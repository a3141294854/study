

# Go 语言学习



## 变量的定义

go的变量定义是: 名称在前，类型在后，可用var来定义，也可用:=(通常用于短期变量，而且不能用在全局变量，它的含义是定义并初始化)

### 使用`var`

```go
var a int = 3
```

### 使用 `:=`

```go
a := 3
```



## 输入和输出

大体上和c语言差不读

### 输入

1 `Scanf` 格式化输入，需要特定的占位符，对空格和换行符敏感

```go
fmt.Scanf("%d",&a)
```

2`Scan` 读取一个数字或字符串，不会读取到空格和换行符

```go
fmt.Scan(&a)
```

### 输出

1 `Printf` 语法参考c语言，占位符，格式化输出

```go
fmt.Printf("%d",a)
```

2 `Println` 会自动换行，逗号隔开

```go
fmt.Println("hello",a)
```



## 基本数据类型

1整数型，正如其名，存整数的

2浮点型，正如其名，存小数的

3字符型，正如其名，存单个字符的（可以看作是个整数，毕竟编码）,用单引号

4字符串类型，正如其名，存多个字符的，用双引号

5布尔型，就两个值，true 和 false 

（如果一个基本数据类型，只定义不赋值，默认对应的零值，整数是0，布尔是false，字符串是""）



## 数据容器

数组，切片，字典（map）

### 数组

跟c语言的没多大差别

```go
//定义
var list [3] int
list := [3]int{1,2,3}
list := [3]int{0:1,1:2}
//引用
a := list[3]

```

## 切片

类似vector，长度可变的数组，定义数组时不写长度就好了。切片的长度，就是元素的个数，容量涉及到内存,会自动管理。

```go

//定义
var list []int
list := []int{}
//除了基本数据类型，其它的只定义不赋值，为nil
//把数组arr索引1到3的数据复制过来
list := arr[1;4]
//添加
list = append(list,1)
//其实好像是把list和1拼接起来，所以也可以有下面操作，把特定位置元素删除
i := 2
list = appen(list[i],list[i+1])
//引用同数组
a := list[3]
//排序 升和降
sort.Ints(list)

sort.Slice(s, func(i, j int) bool {
		return s[i] > s[j]
	})
sort.Sort(sort.Reverse(sort.IntSlice(s)))
//两种降序，我习惯用第一种，问就是cmp的锅

```

### Map

一个键指向一个值，类比函数，键是自变量，值是因变量（但是一般一个值对一个键，冲突的情况应该还遇不到）

```go
//定义,先键和值，注意键只能是基本数据类型，值还可以是函数等
list := [string]string
//添加
list["姓"] = "傅"
//引用通数组,返回两个数据，值，和一个布尔型告诉你有没有
a := list["姓"]
a,ok := list["姓"]
//删除
delete(list,"姓")
```



## 判断和循环

大部分和c语言一样，就是这个range非常好用，用来遍历容器

```go
for index, value := range collection {
    // 循环体
}
//如果是map的话，返回的是键和值
```



## 函数

### 基本函数定义

```go
func name(a int , b int){
}
func name(a,b int){
}
func name(a,b int)int{
    sum := a + b
    return sum
}
```



### 匿名函数

在特定作用域内，短时使用的，没有名字的，可以赋给变量的函数，可用于闭包

```go

add := func(a,b int)int{
    return a + b
}
//如果要立即调用,要加个（）
func(a,b int)int{
    return a + b
}()
```

### 高阶函数

目前看到是用map储存函数

```go
var op int
fmt.Scan(&op)
op_map = map[int]func(){
    1 : a
    2 : b
    3 : c
}
//前面已经定义了三个函数
op_map[op]()
```



### 闭包

一般来说，每个变量都有特定的作用域，离开就会去除，闭包就是搞个匿名函数，引用它，从而在离开作用域后，这个变量还活着（这个变量被捕获后，就独立出来了，原来函数的初始化不会影响），不然为了实现这种效果，就要开全局变量。

```go
package main

import "fmt"

func main() {
    // 创建一个计数器闭包
    counter := createCounter()
    
    fmt.Println(counter()) // 输出: 1
    fmt.Println(counter()) // 输出: 2
    fmt.Println(counter()) // 输出: 3
    
    // 创建另一个独立的计数器
    counter2 := createCounter()
    fmt.Println(counter2()) // 输出: 1 (独立的计数)
    fmt.Println(counter())  // 输出: 4 (继续第一个计数器的计数)
}

// 创建闭包的函数
func createCounter() func() int {
    count := 0 // 被闭包捕获的变量
    
    // 返回一个匿名函数（闭包）
    return func() int {
        count++    // 可以修改外部函数的变量
        return count
    }
}
```

### `init` 函数和`defer`函数

`init`是最先执行的，包直接的按照文件名顺序

`defer`是最后执行的，按照距离返回值的距离，近的先执行



## 结构体和自定义数据类型

基本同c语言，加了个特定的绑定，如果是想在函数中修改结构体内容，记得指针传递

```go
type 类型名字 基础类型
```



```go
type name struch{
    a int
}

//所有name结构的都可以使用这个函数
func (s name)PrintInfo(){
    fmt.Println(s.a)
}
//继承（代码来源于枫枫的博客）
type People struct {
  Time string
}

func (p People) Info() {
  fmt.Println("people ", p.Time)
}

// Student 定义结构体
type Student struct {
  People
  Name string
  Age  int
}

// PrintInfo 给机构体绑定一个方法
func (s Student) PrintInfo() {
  fmt.Printf("name:%s age:%d\n", s.Name, s.Age)
}

func main() {
  p := People{
    Time: "2023-11-15 14:51",
  }
  s := Student{
    People: p,
    Name:   "枫枫",
    Age:    21,
  }
  s.Name = "枫枫知道" // 修改值
  s.PrintInfo()
  s.Info()                   // 可以调用父结构体的方法
  fmt.Println(s.People.Time) // 调用父结构体的属性
  fmt.Println(s.Time)        // 也可以这样
}
```



## 接口

我的理解是一个智能分类器，

首先建造一个接口，显示它可以有哪些操作可以执行和返回的值是什么，

然后建造几个对象，还有对应的各种操作的具体实现，

最后建造一个执行函数（数据类型为端口）根据传入的结构体体的类型，自动执行相应的方法

```go
package main

import "fmt"

type wild interface {
	jiao() string
	pao() string
}

type person struct {
	name string
}

func (p person) jiao() string {
	return p.name + " jiao" + "person"
}

func (p person) pao() string {
	return p.name + " pao" + "person"
}

type robot struct {
	name string
}

func (r robot) jiao() string {
	return r.name + " jiao" + "robot"
}

func (r robot) pao() string {
	return r.name + " pao" + "robot"
}

func action(a wild) {
	fmt.Println(a.jiao())
	fmt.Println(a.pao())
}

func main() {
	尔 := person{
		name: "尔",
	}
	它 := robot{
		name: "它",
	}
	action(尔)
	action(它)

}

```

## 模块和包

模块就是你做的项目，包就是你的项目内实现不同功能的文件夹，每个文件夹内包括各自具体的代码



## 线程和通道（ goroutine & channel）

线程就是开另一个程序去执行另一个动作，通道就是不同线程之间传递信息的桥梁

```go
go func(){
    fmt.Println("好")
    t <- true
}()
t := make(chan bool,3)
//缓冲区可以存档信息，如果满了，程序会停止，
defer close(t)
//关闭通道，常配合defer
flag := <-t
//一次存一个，一次拿一个
```

```go

import "sync"
wg.Add(delta int)
​作用​：增加或减少等待的协程数量
​参数​：delta - 要增加的数量（正数）或减少的数量（负数）
​通常用法​：wg.Add(1)表示要等待1个新协程
wg.Done()
​作用​：表示一个协程已完成，相当于 wg.Add(-1)
​参数​：无
​通常用法​：在协程函数结束时调用
wg.Wait()
​作用​：阻塞当前线程，直到计数器归零
​参数​：无


```

```go
select {
case data := <-ch1:
    // 处理ch1数据
case ch2 <- value:
    // 向ch2发送数据  
case <-time.After(time.Second):
    // 超时处理
default:
    // 非阻塞操作
}
//
Select 特性​：

随机选择就绪的 case

可实现超时、优先级处理

配合 for循环持续监听
```

```go
//优雅的接收，自动检测tong'dao
for data := range Chan {
    list = append(list,data)
  }
```



## defer、panic、recover

`defer`前面有讲，常用于资源清理

panic为直接停止程序，并给出信息，用于报错，会触发defer

```
panic("错了")
```

recover为恢复程序，要配合defer一起用，而且继续的地方不同，panic向上推进直到遇到recover

```go
func 函数C() {
    defer func() {
        if 错误 := recover(); 错误 != nil {
            fmt.Printf("🛡️ 在函数C中捕获: %v\n", 错误)
        }
    }()
    
    fmt.Println("C. 最深层函数")
    panic("💥 最深层错误")
    fmt.Println("C. 这行不会执行")  // ❌ 跳过
}

func 函数B() {
    fmt.Println("B. 中间层函数")
    函数C()
    fmt.Println("B. 这行会执行")  // ✅ 执行
}

func 函数A() {
    fmt.Println("A. 最外层函数")
    函数B()
    fmt.Println("A. 这行会执行")  // ✅ 执行
}

func main() {
    函数A()
    fmt.Println("主程序结束")  // ✅ 执行
}
```

## 关于context

我的理解是个发送电台,记得加`defer cancel()`

### 定时停止，手动取消

```go
ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
//定时停止，从第一个被传入函数时计时，3秒后，所有传入ctx的协程，有监听ctx.Done():的执行操作,如果想要分开计时，就在传入前，重新定义个ctx
ctx, cancel := context.WithCancel(context.Background())
//用于手动关闭，有函数进行cancel()后，所有监听的ctx的执行操作

```

### 进行值的传递

```go
//类似map，但是上下文信封（现在还很蒙蔽）（值和键不可改变）
ctx := context.WithValue(parentContext, key, value)
value := ctx.Value(key)
func main() {
    // 1. 创建基础Context
    ctx := context.Background()
    
    // 2. 添加第一个值
    ctx = context.WithValue(ctx, "username", "张三")
    
    // 3. 添加第二个值（链式调用）
    ctx = context.WithValue(ctx, "userID", 12345)
    
    // 4. 添加第三个值
    ctx = context.WithValue(ctx, "isAdmin", true)
    
    // 5. 在函数中获取和使用值
    processRequest(ctx)
}
func processRequest(ctx context.Context) {
    // 获取值（返回interface{}，需要类型断言）
    username := ctx.Value("username").(string)
    userID := ctx.Value("userID").(int)
    isAdmin := ctx.Value("isAdmin").(bool)
    
    fmt.Printf("用户: %s (ID: %d), 管理员: %t\n", 
        username, userID, isAdmin)
    
    // 传递给下一层函数
    checkPermission(ctx)
    logAction(ctx, "访问了系统")
}


```

### 联级取消

```go
package main

import (
    "context"
    "fmt"
    "time"
)

func main() {
    // 父Context
    parentCtx, parentCancel := context.WithCancel(context.Background())
    defer parentCancel()
    
    // 创建多个子Context
    childCtx1, cancel1 := context.WithCancel(parentCtx)
    defer cancel1()
    
    childCtx2, cancel2 := context.WithCancel(parentCtx)  
    defer cancel2()
    
    // 启动多个协程监听不同的Context
    go worker(childCtx1, "Worker1")
    go worker(childCtx2, "Worker2")
    go worker(parentCtx, "ParentWorker")
    
    // 3秒后取消父Context，所有子Context都会收到取消信号
    time.Sleep(3 * time.Second)
    fmt.Println("取消父Context...")
    parentCancel()
    
    time.Sleep(1 * time.Second)
}

func worker(ctx context.Context, name string) {
    for {
        select {
        case <-ctx.Done():
            fmt.Printf("%s: 收到取消信号\n", name)
            return
        default:
            fmt.Printf("%s: 工作中...\n", name)
            time.Sleep(1 * time.Second)
        }
    }
}
```

### 定时停止

```go
package main

import (
    "context"
    "fmt"
    "time"
)

func main() {
    // 设置今天下午3点为截止时间
    deadline := time.Date(2024, 1, 1, 15, 0, 0, 0, time.Local)
    ctx, cancel := context.WithDeadline(context.Background(), deadline)
    defer cancel()
    
    if dl, ok := ctx.Deadline(); ok {
        fmt.Printf("必须在 %v 前完成\n", dl.Format("15:04:05"))
    }
    
    go func() {
        for {
            select {
            case <-ctx.Done():
                fmt.Printf("截止时间到达: %v\n", ctx.Err())
                return
            default:
                fmt.Printf("当前时间: %v\n", time.Now().Format("15:04:05"))
                time.Sleep(1 * time.Second)
            }
        }
    }()
    
    time.Sleep(10 * time.Second)
}
```

### 关于绑定

```go
// 绑定：timerCtx 继承 manualCtx 的取消能力
manualCtx, manualCancel := context.WithCancel(context.Background())
timerCtx, timerCancel := context.WithTimeout(manualCtx, 5*time.Second)  // 绑定！

// 此时：timerCtx 会在以下情况取消：
// 1. 5秒超时
// 2. manualCancel() 被调用
```



## 错误处理

说明错误信息，及时停止（其它的以后再学）

```go
result, err := someFunction()
if err != nil {
    return fmt.Errorf("做什么操作时失败: %v", err)
}
```

## 反射

```go
	reqValue := reflect.ValueOf(model).Elem() //获取指针指向的值
	fieldValue := reqValue.FieldByName(ty)    //根据字段名获取对应的值
    reflect.ValueOf(req).Elem().FieldByName(v).IsZero() //是使用反射来检查结构体某个字段是否为零值的代码。
    interface_id := reflect.ValueOf(req).Elem().FieldByName("ID").Interface()//这里返回的是value对象，要转换成interface对象
    reflect.TypeOf()
```


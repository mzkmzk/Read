# Go并发编程实践

# 2. 语法规范

## 2.1基本构成元素

类型断言: 判断一个接口值的实际类型是否为某个类型, 或一个非接口值的类型是否实现了某个接口类型, v1.[I1]

解决类型断言的运行时恐慌, `var i1, ok = interface{}(v1).(I1)`

## 2.2 基本类型

- byte: 字节类型, 可以看做8位二进制表示的无符号整数类型
- rune: rune类型, 涌过来存储Unicode字符, 可以看做32位二进制表示的有符号整数类型

## 2.3 高级类型

### 2.3.1 数组

数组类型的零值一定是一个不包含任何元素的空数组

### 2.3.4 函数方法

方法是函数的一种, 它实际就是与某个数据类型关联在一起的函数

```go
type myInt int

func (i myInt) add(another int) myInt {
    i = i + myInt(another)
    return i
}

i1 := myInt(1)
i2 := i1.add(2)
fmt.Println(i1, i2) // 1 3
```
在i1调用add方法时, 这个值被赋给了接受者变量1, 所以i和i1是两个变量

如果需要改造可以

```go
func (i *myInt) add(another int) myInt {
    *i = *i + myInt(another)
    return *i
}

i1 := myInt(1)
i2 := i1.add(2)
fmt.Println(i1, i2) // 3 3
```

在非指针数据类型的值上, 也是能够调用其指针方法的.因为Go在内部做了自动转换

例如add是指针方法,那么i1.add(2)会被自动转换为(&i1).add(2)

### 2.4.4 for语句

- 迭代nil的通道值, 会让当前刘畅永远阻塞在for语句上

### 2.4.5 defer语句

- 当外围函数中的语句正常执行完毕时, 只有其中所有的延迟函数都执行完毕, 外围函数才会真正结束执行
- 当执行外围函数中的return语句时, 只有其中所有的延迟函数都执行完毕后, 外围函数才会真正返回
- 当外围函数中的代码引发运行恐慌时, 只有其中所有的延迟函数都执行完毕后, 该运行恐慌才会真正被扩散至调用函数

注意点

- 多个defer 语句, 执行顺序与声明顺序相反
- defer 通过参数传入, 参数的值在会在当前defer语句被执时求出

### 2.4.6 panic和recover

> recover

recover应该和der语句配合使用

```go
defer func() {
    if e := recover(); e != nil {
        fmt.Printf(e)
    }
}
```

# 3. 并发编程综述

## 3.2 多进程编程

### 3.2.2 关于同步

执行过程中不能中断的操作称为原子操作, 而只能被串行化访问或执行的某个资源或某段代码称为临界区

原子操作是必须一个单一汇编指令的,一般是sync/atomic中的函数

相比原子操作, 让串行化执行的若干代码形成临界区更加通用

### 3.2.3 管道

管道是只能父进程和子进程以及祖先和子进程进行通信方式

### 3.2.4 信号

操作系统信号是IPC中唯一一种异步的通信方式

unix系统中`SIGKILL`和`SIGSTOP`不能由进程自行处理

# 4. Go的并发机制

## 4.1 原理探究

goroutine: 不要用共享内存的方式来通信. 作为替代, 应该以通信作为手段来共享内存

### 4.1.1 线程实现模型

- M: machine, 一个M代表一个内核线程
- P: processor, 一个P代表一个Go代码片段所需的资源(上下文环境)
- G: goroutine, 一个G代表一个Go代码片段

## 4.3 channel

### 4.3.1 channel的基本概念

通道类型是一个引用的类型

> 类型表达法

`chan T`, T代表该通道类型的元素类型

`type IntChan chan int`

这种声明通道类型是双向的

如果需要声明单向类型通道, 需要使用`<-`

- `chan<- T` 只能向此通道发送元素值
- `<-chan T` 只能接受此通道的元素值

> 操作特性

通道主要用于多个goroutine通信, 在同一时刻, 只能有一个goroutine能向一个通道发送元素值

同一时刻, 也只有一个goroutine从通道接受元素值

已被接受的通道元素值, 会立即从通道删除

> 初始化通道

- 缓冲通道: `make(chan int, 10)` 同一时刻最多可缓存10个元素值
- 非缓冲通道: `make(chan int)` 发送给它的元素值应该立即取走, 否则发送方的goroutine就会被阻塞, 直到有接受方接受这个元素值

> 接受元素值

```go
strChan := make(chan string, 3)
elem, ok := <-strChan
```

此时goroutine会进入GWaiting态, 等待strChan有新的元素值

试图从一个未初始化的通道值接受元素值, 会造成当前goroutine永久阻塞

> 放松元素值

```go
strChan := make(chan string, 3)
strChan <- "a"
strChan <- "b"
strChan <- "c"
```

此时strChan通道已满, 假如再有goroutine向通道发送值, 则该goroutine会被阻塞, 知道缓存通道有空余

`struct{}`代表不包含任何字段的结构类型, 空结构体的变量都拥有相同的内存地址, 建议不携带信息时, 都用struct{} 作为元素类型

- 向一个nil的通道发送元素时, 该goroutine会永久阻塞
- 向一个关闭的通道发送元素时, 会panic

若多个goroutine因为发送信息给已满通道而被阻塞时, 最先被阻塞的会先被唤醒

Go运行时, 每次只会唤醒一个goroutine

> 关闭通道

无论如何都不应该在接受端关闭通道, 因为在接受端通常无法判断发送端是否还会向该通道发送元素值

在发送端关闭通道一般不会对接受端有什么影响. 如果该通道在被关闭时, 仍有元素值, 仍可用表达式取出

### 4.3.2 单向channel

channel为单向通道只是语法糖, 用来做强制类型

不能强制转化channel类型

### 4.3.3 for语句和channel

从一个未初始化的通道中接受元素值会导致当前goroutine的永久阻塞

for语句会不断尝试从通道中接受元素值, 直到通道关闭

通道关闭时, 如果通道仍有元素 for range循环会继续读完再结束

### 4.3.4 select语句

> 分支选择规则

开始执行select语句时, 所有跟在case关键字右边的发送语句或接受语句都会被先求值(从左到右, 从上到下)

```go
package main
import "fmt"

var intChan1 chan int
var intChan2 chan int
var channels = []chan int{intChan1, intChan2}

var numbers = []int{1,2,3,4,5}

func getChan(i int) chan int {
    fmt.Printf("channels[%d]\n", i)
    return channels[i]
}

func getNumber(i int) int {
    fmt.Printf("numbers[%d]\n", i)
    return numbers[i]
}

func main() {
    select {
        case getChan(0) <- getNumber(0):
            fmt.Println("1th")
        case getChan(1) <- getNumber(1):
            fmt.Println("2th")
        default:
            fmt.Println("default")
    }
}
```

输出结果为

```
channels[0]
numbers[0]
channels[1]
numbers[1]
default
```

因为上述channel 都未被初始化, 所以会永久阻塞, 走到了defaultcase

执行select时, 会自上而下的判断每个case发送或接收操作是否可以立即进行, 如果多个case都满足条件

系统会通过伪随机算法选中一个case

如果没有一个case符合条件并且没有default case, 此处将会永久阻塞

default无论位置在哪, 执行舒心都是最后

### 4.3.6 time包和channel

超时设置

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    intChan := make(chan int, 1)
    go func() {
        for i:=0; i< 5; i++ {
            time.Sleep(time.Second)
            intChan <- i
        }
        close(intChan)
    }()
    timeout := time.Millisecond * 500
    var timer *time.Timer
    for {
        if timer == nil {
            timer = time.NewTimer(timeout)
        } else {
            timer.Reset(timeout)
        }
        select {
            case e, ok := <- intChan:
                if !ok {
                    fmt.Println("End.")
                    return
                }
                fmt.Printf("Reveciver: %v\n", e)
            case <-timer.C:
                fmt.Println("Timeout")
        }
    }
}

```
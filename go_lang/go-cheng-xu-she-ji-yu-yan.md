# Go程序设计语言

## 1. 入门

hello world

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello World")
}
```

直接执行

```bash
go run helloWorld.go
```

编译为二进制文件, 方便后续多次执行

```bash
go build helloWorld.go
./helloWorld.go
```

以下几种声明字符串变量的方式是等价的

```go
s := ""
var s string
var s = ""
var s string = ""
```
## 2. 程序结构


### 2.3 变量

声明变量的通用形式: `var name type = expression`

Go里不存在未初始化的变量

### 2.3.2指针

```go
x := 1
p := &x // p是整形指针, 指向x
fmt.Println(*p)  // 1
*p = 2 // 等于 x = 2
fmt.Println(x) // 结果为2
```

指针的零值为`nil`

两个指针在都为nil或者指向同一变量时才会相等

函数返回局部变量的地址是非常安全的

```go
var p = f()
func f() *int {
    v := 1
    return &v
}

fmt.Println(f() == f()) // false
```

### 2.3.3 new函数

`new(T)`创建一个未命名的T类型变量, 并返回其地址(地址类型为 *T)

```go
p := new(int)
fmt.Println(*p) // 0
*p = 2 
fmt.Println(*p) // 2

```

### 2.3.4 变量的生命周期

包级别变量的生命周期是整个个程序的执行时间

局部变量是一个动态的生命周期, 变量会一直生存到它变为不可访问

### 2.4.1 多重复制

需要两个边路交互值时

```go
a{i],a[j] = a[j], a[i]
```

## 2.5 类型声明

语法

`type name underlying-type`

类型声明一般出现在包级别, 如果名字开头是大写字母, 其他的包也可以访问它

```go
package tempconv

import "fmt"

type Celsius float64
type Fahrenheit float64

const (
    AbsoluteZeroC Celsius = -273.15
    FreezingC    Celsius = 0
    BoilingC     Celsius = 100
)

func CToF (c Celsius) Fahrenheit { return Fahrenheit(c*9/5 + 32)}
func FToC (f Fahrenheit ) Celsius { return Celsius((f - 32) * 5 / 9)}
```

`Celsius`和`Fahrenheit`的底层即使都使用了float64, 但他们不是相同的类型

所以不能用算数表达式进行比较和合并

对于每一种类型T, 都可以进行类型转换操作T(x), 如果两个类型具有相同的底层类型或者两者都指向相同底层类型变量的未命名指针类型

则两者是可以互相转换的

任何情况下, 运行时的转换不会失败

```go
var c Celsius
var f Fahrenheit

fmt.Println(c == 0) // true
fmt.Println(f >= 0) // true
fmt.Println(c == f) // 编译错误
fmt.Println(c == Celsius(f)) // true
```

下面的声明中Celsius参数c出现在函数名字前面, 名字叫String的方法关联到Celsius类型


```go
func (c Celsius) String() string { return fmt.Sprintf("%g℃", c)}
```

变量通过fmt包作为字符串输出时, 可以控制类型值的线上方式

```go
c := FToC(212.0)

fmt.Println(c.String()) // 100℃
fmt.Println("%v\n", c) // 100℃
fmt.Println("%s\n", c) // 100℃
fmt.Println(c) // 100℃
fmt.Println("%g\n", c) // 100
fmt.Println(float65(c)) // 100
```

## 2.6 包和文件

`gopl.io/ch1/helloworld`包的文件就存储在`$GOPATH/src/gopl.io/chi1/helloworld`中

在Go中, 包中导出的标识符是以大写字母开头

### 2.6.2 包初始化

```go
func init() { ... }
```


init函数不能被调用和被引用

在每一个文件里, 当程序启动时, init函数按照他们的声明顺序自动执行

```go
var cwd string
func init() {
    cwd, err := os.Getwd() //编译错误, 未使用cwd
    if err != nil {
        log.Fatalf(err)
    }
}
```

这里的cwd并不会影响到外部的cwd, 在同一作用域中, cwd并未被声明, 所以`:=`后cwd是init函数的局部变量

并未影响到外部的cwd

可以更改为

```go
var cwd string
func init() {
    var err error
    cwd, err = os.Getwd() 
    if err != nil {
        log.Fatalf(err)
    }
}
```

## 3. 基本数据

### 3.1整数

有符号整数: int8, int16, int32, int64

无符号整数: uint8, uint16, uint32, uint64

此外还有两种类型init和unit 在特定平台上, 大小与原生的有符号整数/无符号整数数值相同

还要衶无符号整数uintptr, 大小不明确, 但足够存放指针, 一般用于底层编程

int、uint、uintptr都不同于类似的整形, 例如int和init32要显示的进行转换

取模运算符`%`仅能运用于整数

整数与整数相除, 都会舍弃小数部分

溢出的高位数部分会被丢弃, 如果原本计算结果是有符号类型, 并且最左侧位是1则会形成负值

```go
var u uint8 = 255
fmt.Println(u, u+1, u*u) // 255 0 1

var i int8 = 127
fmt.Println(i, i+1, i*i) // 127 -128 1
```

## 3.2 浮点数

go的两大浮点数类型有float32, float64

## 3.5 字符串

```go
s := "hello world"
c := s[len(s)] //宕机, 下标越界
```

子串生成语法`s[i,j]`, 包括第一个, 但不包括第j个, 长度为 j -i

i的默认值为0, j的默认值为len(s), j的值小于则宕机

```go
fmt.Println(:5) // hello
fmt.Println(7:) // world
fmt.Println(:) // hello world
```

因为字符串是不可改变的, 所以字符串内部不允许修改

```go
s[0] = 'L' //编译错误
```

### 3.5.1 字符串字面量

"Hello World"

原生字符串字面量的书写形式是`...`, 会把回车符删除(换行符保留)

所以可以用原生字符串写多行字符

## 4. 复合数据类型

### 4.1 数组

```go
var q [3]int = [3]int{1,2,3} //与下面等价
q := [...]int{1,2,3}
```

`...`表示数组长度由初始化数组的元素决定

变量数组具有长度不可变性

```go
q := [3]int{1, 2, 3}
q = [4]int{1,2,3,4} //编译错误
```

指定key的初始化

```go
type Currency int
const (
    USD Currency = iota
    EUR
    GBP
    RMB
)

symbol := [...]string{USD: "$", EUR: "e", GBP: "e", RMB: "¥"}

fmt.Println(RMB, symbol[RMB]) // 3 ¥
```

在这种情况下索引可以任意顺序, 并且有时可省略, 

还有这种情况

```go
r := [...]{99: -1}
```

定义了一个含有100个元素的数组r, 除了最后一个元素值是-1外, 该数组中的其他元素值都为0

如果一个数组的元素是可比较的, 那么这个数组也是可比较的

`==`比较的结果是两边元素的值是否完全相同

```go
a := [2]int{1, 2}
b := [...]int{1, 2}
c := [2]int{1, 3}
fmt.Println(a == b, a == c, b == c) // true false false
d := [3]int{1, 2}
fmt.Println(a == d) // 编译失败 长度不一样无法比较
```

Go把数组和其他类型都看为`值传递`

### 4.2 slice

`slice`表示一个拥有相同元素的可变长度序列

slice一般写成`[]T`

slice有3个属性: 指针, 长度和容量

指针指向数组的第一个可以从slice中访问的元素

长度是slice的元素个数

它不能超过slice的容量

容量大小通常从slice的起始元素到底层数组的最有一个元素间元素的个数

go的内置函数len和cap用来返回slice的长度和容量

slice无法用==来比较是否拥有相同元素

slice唯一可以用`==`的就是判断是否为nil

`if summer == nil {...}`

slice的零值是`nil`

值为`nil`的slice的长度和容量都为0

```go
var s []int // len(s) == 0, s == nil
s = nil // len(s) == 0, s == nil
s = []int(nil) // len(s) == 0, s == nil
s = []int{} // len(s) == 0, s != nil
```

如果想坚持slice是否为空, 最好是`len(s) == 0` 而不是 `s == nil`

使用`make`来创建slice

```go
make([]T, len)
make([]T, len, cap)// 和make([]T, cap)[:len]功能一样
```

第一行创建了容量和长度都是len

而第二行只引用了前len个元素, 但是容量是数组的长度, 为未来的slice元素留出空间

### 4.2.1 append函数

```go
func appendInt(x []int, y int) []int {
    var z []int
    zLen := len(x) + 1
    if zLen <= cap(x) {
        // x仍有空间
        z = x[:zLen]
    } else {
        // slice 已无空间
        // 为了达到分摊线性复杂性, 容量扩展一倍
        zCap := zLen
        if zCap < 2*len(x) {
            zCap = 2 * len(x)
        }
        z = make([]int, zLen, zCap)
        copy(z, x)
    }
    z[len(x)] = y
    return z
}
```

copy函数中, 源slice和目标slice 可能对应同一个底层数组

甚至可能出现元素重叠

copy函数的返回值是 返回实际复制的元素个数

要更新一个slice的指针, 长度或容量都必须这样

```go
runes = append(runes, r)
```

### 4.2.2 slice就地修改

例如 删除一个slice里的空字符串

```go
func noEmpty(strings []string) []string {
    i := 0
    for _, s := range strings {
        if s != "" {
            strings[i] = s
            i++
        }
    }
    return strings[:i]
}
```

这样会导致底层数组会被更改

```go
data := []string{"one", "", "three"}
fmt.Printf("%q\n", noEmpty(data)) // ["one", "three"]
fmt.Printf("%q\n", data) // ["one", "three", "three"]
```

所以通常我们这么写

```go
data = noEmpty(data)
```

在slice中取出某一个元素

```go
func remove(slice []int ,i int ) []int {
    sLen := len(slice)
    if i < 0 || i >= sLen {
        return slice
    } 
    copy(slice[i:], slice[i+1:])
    return slice[:len(slice) - 1]
}

func main() {
    s :=  remove([]int{5, 6, 7, 8, 9}, 2)
	fmt.Println(s)
}
```

copy函数覆盖的逻辑为, 源slice(第二个参数)按对应索引位复制到目标slice

所以上面的copy函数是, `copy([]int{7, 8, 9}, []int{8, 9})`

源slice的第0位复制目标slice的第0位, 源slice的第1位复制目标slice的第1位

所以最终`slice[i:]`会被改变成`[8, 9, 9]` 

整个slice就位`[5, 6, 8 ,9 ,9]`, 再删掉最后一位即可

## 4.3 map

散列表是设计精妙, 用途广泛的数据结构之一

是一个拥有键值对元素的无序集合

键的值是唯一的

无论这个散列表有多大, (获取/ 更新/ 删除) 这些操作基本是通过常量时间的键比较就可以完成的

map中所有的键 都拥有相同的数据类型, 所有的值也拥有相同的类型

> 创建

创建map的方式

```go
args := make(map[string]int)

// 或通过字面量

args := map[string]int{
    "alice": 14,
    "charlie": 34
}

// 等价于

args := make(map[string]int)

args["slice"] = 14
args["charlie"] = 34

```

> 删除

```go
delete(args, "alice") //移除元素args["slice"]
```

即使键不在map中, 上面的操作也是安全的

map通过键来找值, 如果对应值不存在, 就返回该类型的零值

例如下面的代码是可行的

```go
args["boo"] = args["boo"] + 1 // 1
```

> 获取

但是map元素不是一个变量, 不可以获取其地址, 因为map增长可能导致已有元素被重新散列到新的位置

遍历map

```go
for name, age := range args {
    fmt.Printf("%s\t%d\n", name, age)
}
```

map的元素迭代顺序是不固定的

map的零值是nil

```go
var ages map[string]int
fmt.Println(ages == nil ) // true
fmt.Println(len(ages) == 0 ) // true

ages["carol"] = 21 //宕机, 为零值map中的项赋值
```

设置元素之前, 必须初始化map

通过下标的方式访问map中的元素总是会有值

- 键在map中, 返回对应的值
- 键不在map中, 返回值的零值

判断键是否存在

```go
if age, ok := ages["bob"]; !ok {
    /* bob 不是map中的键 */
}
```

map是不可比较的, 只能跟nil对比

map的键必须是可比较的

## 4.4 结构体

结构体是将零个或者多个任意类型的命名变量组合在一起的聚合数据类型

点号同样可以用在结构体的指针上

```go
var employeeOfTheMonth *Employee = &dilbert
employeeOfTheMonth.Position += " (proactive team player)"

// 最后一句等价于

(*employeeOfTheMonth).Position += " (proactive team player)"
```

结构体A的属性中不能嵌套同一结构体A

但是结构体A中的属性可以嵌套结构A的指针类型

对创建递归的数据结构比较有用, 比如链表和数

下面实现一个二叉树插入排序的例子

```go
type tree struct {
    value int 
    lef, right *tree
}

func Sort(values []int) {
    var root *tree
    for _, v := range values {
        add(root, v)
    }
    appendValues(values[:0], root)
}

func appendValues(values []int, t *tree) []int {
    if t != nil {
        values = appendValues(values, t.left)
        values = append(values, t.value)
        values = appendValues(values, t.right)
    }
    return values
}


func add(t *tree, v) *tree {
    if (t == nil) {
        // 等价于 *tree{value: value}
        t = new(tree)
        t.value = value
        return t
    }
    if (value < t.value) {
        t.left = add(t.left, value)
    } else {
        t.right = add(t.right, value)
    }
    return t
}


```

结构体的零值是由结构体成员的零值组成

> 4.4.1 结构字面量

声明字面量

```go
type Point struct{ X, Y int}
p := Point{1, 2}
```

这种按顺序声明的方式, 不太建议, 代码可阅读性不高

建议使用


```go
p := Point{
    X: 1,
    Y: 2
}
```

这种声明的key, 可以不按顺序声明key

结构体的属性为小写开头, 则为不可导出

```go
package p
type T struct {a, b int} 

package q
var _ = p.T{a: 1} // 编译错误, 无法引用a, b
```

结构体可作为参数传给函数, 一般为了提高效率, 大型结构体通常用结构体指针直接传递给函数或从函数中返回

因为结构体一般都以指针函数传递, 所以经常直接声明变量就位指针变量

```go
pp := &Point{1, 2}

//等价于

pp := new(Point)
*pp = Point{1, 2}
```

> 4.4.2 结构体比较

如果结构体的所有成员变量都可以比较

那么结构体就是可比较的

其中`==` 按顺序比较两个结构体变量的成员变量

与其他可比较类型一样, 可比较的结构体可作为map的键类型

```go
type address struct {
    hostname string
    port int
}

hits := make(map[address]int)
hits[address{"golang.org", 443}]++
```
> 4.4.3 结构体嵌套和匿名成员

结构体嵌套机制: 将一个命令结构提当作另一个结构体类型的匿名成员使用

例如`x.f`就可以代替`x.d.e.f`

Go允许我们定义不带名称的结构体成员, 只需要指定类型即可, 这种结构体成员称为匿名成员

```go
type Point struct {
    X,Y int
}

type Circle struct {
    Point
    Radius int
}

type Wheel struct {
    Circle
    Spokes int
}

var w Wheel
w.X = 8 // w.Circle.Point.X = 8
W.radius = 5 // w.Circle.Radius = 5
```

但在声明时, 必须要按照嵌套类型来

```go
w = Wheel{Circle{Point{8, 8}, 5}, 20}

// 或

w = Wheel {
    Circle: Circle{
        Point: Point{X:8 ,Y: 8}
    },
    Spokes: 20
}
```

### 4.5 JSON

结构体转为json

```go
data, err := json.Marshal(movies)
if err != nil {
    log.Fatalf("JSON marshaling failed: %s", err)
}
fmt.Printf("%s\n", data)
```

Marshal返回一个字节slice

若想格式化JSON可以使用`json.MarshalIndex(movies, "", "    ")`, 第2个参数为输出的前缀字符串, 第二个缩进的字符串

只有可导出的成员会转换为JSON字段

结构体中可以设定成员标签定义

```go
Year int `json:"release"`
Color bool `json:"color, omitempty"`
```

上述表明当结构体转为json时, 

- Year字段会转为属性名叫`release`的字段
- Color字段会转为属性名叫`color`的字段, 并且表示该字段的值为零值或者为空, 则不输出到JSON里

与marshal对应的逆操作(JSON->struct)的方法叫unmarshal

```go
var titles []struct{ Title string }
if err := json.Unmarshal(data, &titles); err != nil {
    log.Fatalf("JSON.unmarshaling failed %s", err)
}
fmt.Println(titles) // [{Casablanca}, {Cool Hand Luke} {Bullitt}]

```

## 4.6 文本和html模板

主要使用`text/template`包和`html/template`包

这两个包基本用法一样,

主要是`html/template`会对HTML,JS,CSS和URL进行自动转义, 规避安全问题

使用示例

html

```go
const templ = `{{ .TotalCount }} issues:
{{ range .Items }}--------------
Number: {{ .Number }}
User: {{ .User.Login }}
Title: {{ .Title | printf "%.64s" }}
Age: {{ .CreateAt | daysAgo }} days
{{ end }}`
```

- `.`开头即为获取变量 
- `|` 与linux命令行类似, 把前面的结果 丢给后面的 技术处理

使用模板

```go
var report = template.Must(
        template.New("issuelist")).
            Funcs(template.FuncMap("daysAgo": daysAgo))
            Parse(templ)
    )

func main() {
    result, err := github.SearchIssues(os.Args[1:])
    if err != nil { 
        log.Fatal(err)
    }
    if err := report.Execute(os.Stdout, result); err != nil {
        log.Fatal(err)
    }
}
```

`html/template`的基本用法

```go
func main() {
    const templ = `<p>A: {{ .A }}</p><p>B: {{ .B }}</p>`
    t := template.Must(template.New("escape").Parse(templ))
    var data struct {
        A string // 不受信任的文本
        B template.HTML //受信任的html
    }
    data.A = "<b>Hello!</b>"
    data.B = "<b>Hello!</b>"
    if err != t.Execute(os.Stdout, data); err != nil {
        log.Fatal(err)
    }
}

```

A则为被转义后输出, B则按照正常html被解析

## 5 函数

### 5.1 函数声明

```go
func name(parameter-list) (result-list) {
    body
}
```

实参都是按值传递的, 但如果实参包含引用类型, 比如指针, slice, map, 函数, 通道, 那么使用形参变量可能会简介修改实参变量

如果发现函数是没有函数体, 表明该方法是用汇编语言实现的, 声明的是函数的签名

```go
package math
func Sin(x float64) float64
```

### 5.3 多返回值

裸返回, 即函数直接return,  但返回参数和函数体内的变量有一致, 则执行`return`, 会把同名的参数返回


```go
func CountWordsAnImages(url string) (words, images int, err error) {
    resp, err := http.Get(url)
    if err != nil {
        return //返回 0 0 err
    }
    doc, err := html.Parse(resp.Body)
    resp.Body.Close()
    if err != nil {
        return // 返回0 0 err
    }
    words, images = countWordsAndImage(doc)
    return // 返回 words images err
}
```

尽量少使用

### 5.4 错误

如果函数只有一种错误情况, 那么最后一个参数一般设为布尔类型

假如情况有很多种, 那么返回error

Go语言的异常只是针对程序bug导致预料之外的错误, 而不能通过常规错误处理方法出现在程序中

> 错误处理策略

例子1: 将错误传递下去(最常见的例子)

```go
resp, err := http.Get(url)
if err != nil {
    return nil, err
}

```

例子2: 需要为错误添加关键信息


```go
doc, err = html.Parse(resp.Body)
resp.Body.close()

if err != nil {
    return nil, fmt.Errorf("parsing %s as HTML: %v", url, err)
}
```

这里如果只返回解析失败的err, 很难分析问题, 带上url和html, 则容易分析问题很多

一般的`f(x)`调用只负责报告函数的行为f和参数值x

例子3: 不固定或者不可预测的错误, 进行重试

对于不固定或者不可预测的错误, 在短暂的间隔后对操作进行重试是合乎情理的, 超过一定的重试次数和限定时间后再报错退出

```go
func WaitForServer(url string) error {
    const timeout = 1 * time.Minute
    deadline := time.Now().Add(timeout)
    for tries :=0; time.Now().Before(deadline); tries++ {
        _, err := http.Head(url)
        if err == nil {
            return nil 
        }
        log.Printf("server not responding (%s) retrying...", err)
        time.Sleep(time.Second <<  uint(tries)) //指数退避策略 // 1S 2S 4S 8S 16S 32S 
    }
    return fmt.Errorf("server %s failed to respond after %s", url, timeout)
}
```

> 5.4.2 文件结束标识

最终用户对多种错误感兴趣, 而不是中间设计的逻辑

所以我们的err 有时需要详细的暴露是哪种情况, 好让使用者针对不同的error做处理

例如IO包要暴露文件读取完毕的error

```go
package io
import "errors"

var EOF = errors.New("EOF")
```

使用者

```go
in := bufio.NewRender(os.Stdin)

for {
    r, _, err := in.ReadRune()
    if err == io.EOF {
        break // 停止读取
    }
    if err != nil {
        return fmt.Error("read failed: %v", err)
    }
    ...
}

```

### 5.5 函数变量

函数类型的零值是nil, 调用空的函数变量将会宕机

```go
var f func (int) int
f(3) //宕机
```

这么写则不宕机

```go
var f func (int) int
if f != nil {
    f(3)
}
```

函数是不可比较的

> 捕获迭代变量

```go
var rmdirs []func()
for _, dir := range tempDirs() {
    os.MkdirAll(dir, 0755)
    rmdirs = append(rmdirs, func() {
        os.RemoveAll(dir)
    })
}
```

这里的执行结果是, `os.RemoveAll`会执行多次, 但是删除的路径一直都是最后一次循环的dir

要修复这个问题可以这么改, 将dir至于函数体内

```go
var rmdirs []func()
for _, d := range tempDirs() {
    dir := d
    os.MkdirAll(dir, 0755)
    rmdirs = append(rmdirs, func() {
        os.RemoveAll(dir)
    })
}
```

### 5.7 变长函数

```go
func sum(vals ..int) int {
    ...
}
```

`...int`代表0个或多个int参数

也可以用于参数传递中

```go
values := []int{1, 2,3, 4}
sum(values...)
```

### 5.8 延迟函数调用

延迟函数调用主要运用defer语句

无论是在正常情况下, 执行return语句或函数执行完毕

还是不正常的情况下, 比如宕机

实际调用延迟到包含defer语句的函数执行后才执行

defer语句没有限制使用次数, 执行的书序为defer语句声明的倒序进行

延迟执行的匿名函数能够改变外层还是返回给调用者的结果

```go
func double(x int) (result int) {
    defer func { fmt.Printf("double(%d) = %d\n", x, result) }() // 8w
    return x +x
}

func triple(x int) (result int) {
    defer func() { result += x }()
    return double(x)
}

fmt.Println(triple(4)) // 12

```

## 5.10 恢复

当出现宕机时, 假如需要对某一种错误进行恢复, 可以使用recover


```go
func soleTitle() (title string, err error) {
    
    type bailout struct {}
    defer func() {
        switch p := recover(); p {
            case nil:
            case bailout{}:
                err = fmt.Errorf("multiple title elements")
            default:
                panic(p)
        }
    }()
    title := ...
    panic(bailout{})
    return title, nil
}
```

## 6 方法

### 6.1 方法声明

```go
type Point struct {X, Y float64}
func (p Point) Distance(q Point) float64 {
    return xxx
}
```

方法能赋给任意类型, 例如slice, 数字等, 除了指针类型和接口类型

### 6.2 指针接受者方法

由于主调函数会复制每一个实参变量, 如果函数需要更新一个变量, 或者如果一个实参太大而我们希望避免复制整个实参

隐藏我们必须使用指针来传递变量的地址

```go
func (p *Point) ScaleBy(factor float64) {
    p.X *= factor
    p.Y *= factor
}
```
这个方法是`(*Point).ScaleBy`, 括号是必须的, 否则会被解析为`*(Point.ScaleBy)`

习惯上遵循 如果Point的任何一个方法使用指针接受者, 那么所有的Point方法都应该使用指针接受者

为防止混淆, 不允许本身是指针的类型进行方法声明

```go
type P *int
type (P) f() {...} //编译错误 
```

指针接受者方法调用

```go
r := &Point{1, 2}
r.ScaleBy(2)
fmt.Println(*r) // {2, 4}
```

如果接受者p是Point类型的变量, 但方法要求一个 *Point的接受者, 我们可以简写为

p.ScaleBy(2)

实际上编译器会对变量进行`&p`的隐式转换

不能够对一个不能取地址的Point接受者调用*Point方法, 因为无法获取临时变量的地址

```go
Point{1, 2}.ScaleBy(2) //编译错误, 不能获取Point类型字面量地址
```

当实参接受者是*T类型, 而形参是T类型, 编译器也会隐式的解引用接受者

```go
pptr.Distance(q) // 隐式转换为*pptr
```

如果所有类型T方法的接受者是T自己(而非*T), 那么复制它的实例是安全的

调用方法的时候都必须进行一次复制

但是任何方法的接受者是指针的情况下, 应该避免复制T实例

因为这么做可能会破坏内部原本的数据, 比如复制bytes.Buffer实例只会得到相当于原来bytes数组的一个别名

随后的方法调用会产生不可预期的结果

`nil是一个合法的接受者`

### 6.3 通过结构体内嵌组成类型

主要用作结构体的组合

```go
var (
    mu sync.Mutex //保护mapping
    mapping = make(map[string]string)
)

func Lookup(key string) string {
    mu.Lock()
    v := mapping[key]
    mu.Unlock()
    return v
}
```

对结构体进行封装

```go
var cache = struct {
    sync.Mutex
    mapping map[string]string
}

func Lookup(key string) string {
    cache.Lock()
    v := cache.mapping[key]
    cache.Unlock()
    return v
}
```

## 6.4 方法变量与表达式

```go
type Point struct { X,Y float64}
func (p Point) Add(q Point) Point { return Point{p.X + q.X, p.Y + q.Y}}
func (p Point) Sub(q Point) Point { return Point{p.X -q.X, p.Y - q.Y }}

type Path []Point

func (path Path) TranslateBy(offset Point, add bool){
    var op func(p, q Point) Point
    if add {
        op = Point.Add
    } else {
        op = Point.Sub
    }

    for i := range path {
        path[i] = op(path[i], offset)
    }
}
```

## 7. 接口

### 7.2 接口类型

一个接口类型定义了一套方法, 如果一个具体类型要实现该接口, 必须实现接口类定义的所有方法

声明接口

```go
package io

type Reader interface {
    Read(p []byte) (n int, err error)
}

type Closer interface {
    Close() error
}
```

另外, 我们还可以发现通过组合已有的接口得到新的接口

```go
type ReadWriter interface {
    Reader
    Writer
}
```

### 7.3 实现接口

只要一个类型实现了一个接口要求的所有方法, 那么这个类型实现了这个接口

```go
var w io.Writer
w = os.Stdout // ok *os.File实现了Write方法
w = new(bytes.Buffer) // ok *bytes.Buffer有Write方法
w = time.Second // 编译失败
```

接口也可以赋值给接口

```go
var rwc io.ReadWriteCloser
w = rwc // ok
```

### 7.5 接口值

一个接口类型的值包含两部分

- 具体类型(动态类型)
- 该类型的一个值(动态值)

类型只是编译时的概念, 类型不是一个值

类型需要用`类型描述符`来提供每个类型的具体信息, 比如它的名字和方法

```go
var w io.Writer // 类型: nil 值 nil
w = os.Stdout // 类型 *os.File的类型描述符, 值: os.Stdout的副本
w = new(bytes.Buffer) // 类型 *bytes.Buffer的类型描述符, 值为新分配缓冲区的指针
w = nil // 类型: nil 值 nil
```

一个接口值可以指向多个任意大的动态值

```go
var x interface{} = time.Now() // 类型time.Time, 值: sec:xxx, nsec:xxx, ioc:'"UTC"
```

接口值可以用==和!=比较, 如果两个接口值都是nil或者(动态类型一致&&动态值一致)

需要注意: 在比较两个接口值时, 假如接口值动态类型一致, 但动态值是不可比较的(比如slice), 会宕机

```go
var x interface{} = []int{1, 2, 3}
fmt.Println(x === x) //宕机
```

> 注意含有空指针的非空接口

```go
const debug = true
func main() {
    var buf *bytes.Buffer
    if debug {
        buf = new(bytes.Buffer)
    }
    f(buf)
    if debug {
        ...
    }
}

func f(out io.Writer) {
    if out != nil {
        out.Write([]byte("done!\n"))
    }
}
```

调用`f`时, 会把一个空指针赋值给io.Writer

进行`out != nil`判断, 此时out的类型为`*bytes.Buffer` 而非nil, 虽然值为`nil`

但这个判断仍会返回true

导致`out.Write`会宕机

需要做如下修改

```go
var buf io.Writer
```

因为`*bytes.Buffer`满足了该接口, 但不满足该接口的行为, 即接受者不能为空, 而io.Writer则允许接受者为nil

### 7.10 类型断言

类型断言是一个作用在接口值上的操作, 语法为`x.(T)`

x为一个接口的表达式, T为一个类型

断言类型T假如未具体类型, 那么会检查x的动态类型是否就是T

```go
var w io.Writer
w = os.Stdout
f := w.(*os.File) //成功 f == os.Stdout
c := w.(*bytes.Buffer) // 宕机, 接口持有的是*os.File, 不是 *bytes.Buffer
```

断言类型假如是一个接口类型, 那么会检查x的动态类型是否满足T

检查成功, 动态值并不会提取出来, 结果仍然是一个接口值, 接口值的类型和值部分也没有变更

只是结果的类型为接口类型T

即接口类型的断言, 只会更换接口类型(通常方法数量是增多)

```go
var w io.Writer
w = os.Stdout
rw := w.(io.ReadWriter) //成功 *os.File 有Read和Write方法

w = new(ByteCounter)
rw = w.(io.readWriter) //崩溃, *ByteCounter 没有Read方法
```

无论在哪种类型作为断言类型, 如果操作数是一个空的接口, 类型断言都会失败



```go
w = rw
w = rw.(io.Writer) // 晋档rw == nil时失败
```

当断言接口返回值为两个时, 断言不会崩溃, 而是把结果放到第二个返回值

```go
var w io.Writer = os.Stdout
f, ok = w.(*os.File)// ok为true
b, ok = w.(*bytes.Buffer) //ok为false
```

### 7.11 使用类型断言来识别错误

用PathError来获取更详细的error信息

```go
package os
type PathError struct {
    Op string
    Path string
    Err error
}

func (e *PathError) Error() string {
    return e.Op + " " + e.Path + ": " + e.Err.Error()
}

import (
    "errors"
    "syscall"
)

var ErrorNotExist = errors.New("file does not exist")

func IsNotExist(err error) bool {
    if pe, ok != err.(*PathError); ok {
        err = pe.Err
    }
    return err == syscall.ENOENT || err == ErrNotExist
}

```

### 7.13 类型分支

```go
func sqlQuote(x interface{}) string {
    if x == nil {
        ...
    }else if _, ok := x.(int); ok {
        ...
    }
    ...
}
```

等价于

```go
func sqlQuote(x interface{}) string {
    switch x.(type) {
        case nil: ...
        case int, uint: ...
    }
}
```

## 8. goroutine和通道

### 8.1 goroutine

```go
f() 调用f() 等待它返回
go f() 新建一个调用f()的goroutine 不用等待
```

### 8.4 通道

goroutine是go并发的执行体

通道就是他们之间的链接

通道是可以让一个goroutine发送特定值到另一个goroutine的通信机制

每一个通道是一个具体类型的导管, 叫做通道的元素类型, 一个int元素类型的通道写为`chan int`

使用make创建一个通道

```go
ch := make(chan int)
```

像map一样, 通道是一个使用make创建的数据结构的引用

当复制或者作为参赛传递到一个函数时, 复制的是引用

通道的零值是nil

通道的主要操作是发送和接受

```go
ch <- x //发送语句
x = <- ch // 赋值语句中的接受表达式
<-ch // 接受语句 抛弃结果
close(ch) // 关闭通道
```

直接使用make创建的通道称为无缓冲通道, 而make可以接受第二个参数, 表示通道容量的整数

```go
ch = make(chan int) // 无缓冲通道
ch = make(chan int , 0) // 无缓冲通道
ch = make(chan int , 3) // 容量为3的缓存通道
```

> 8.4.1 无缓冲通道

无缓冲通道的发送操作将会阻塞, 直到另一个goroutine在对应的通道上执行操作, 这时值传送完成

两个goroutine都可以继续执行

相反, 如果接受操作先执行, 接受放goroutine将阻塞, 直到另一个goroutine在同一通道上发送一个值

使用无缓冲通道会导致发送和接受goroutine同步化, 无缓冲通道也称为同步通道

> 8.4.2 管道

通道可以用来连接goroutine, 这样一个的输出是另一个的输入, 这个叫做管道(pipeline)

例如一个goroutine发送自然数->接受自然数,并发送平方值->输出值

```go
func main(){
    naturals := make(chan int)
    squares := make(chan int)
    
    go func() {
        for x:=0; x < 100 ; x++ {
            naturals <- x
        }
        close(naturals)
    }()

    go func {
        for x: range naturals {
            squares <- x * x
        }
        close(squares)
    }()

    for x := range squares {
        fmt.Println(x)
    }
}
```

结束时关闭每一个通道不是必须的, 通道可以通过垃圾回收期判断是否回收它

试图关闭一个已经关闭的通道会宕机


> 8.4.3 单项通道

- `chan<- int`: 只能作为发送int的通道, 不能接受
- `<- chan int`: 只能作为接受int的通道, 不能发送, 关闭一个仅接受的通道, 会编译错误

更改上述代码


```go
func counter(out chan<- int) {
    for x:=0; x< 100; x++ {
        out <- x
    }
    close(out)
}

func squarer(out chan<- int, in <- chan int) {
    for v := range in {
        out <- x * x
    }
    close(out)
}

func printer(in <- chan int) {
    for v := range in {
        fmt.Println(v)
    }
}

func main() {
    naturals := make(chan int)
    squares := make(chan int )

    go counter(naturals)
    go squarer(squares, naturals)
    printer(squares)
}

```

> 8.4.4 缓冲通道

缓冲通道有一个元素队列

获取缓冲通道当前用于的元素个数`len(ch)`

获取缓冲通道的总容量`cap(ch)`

```go
func mirroredQuery() string {
    responses := make(chan string, 3)
    go func { responses <- request("asia.gopl.io") }()
    go func { responses <- request("europe.gopl.io") }()
    go func { responses <- request("americas.gopl.io") }()
    return <- responses 
}
```

这里同时向3个不同地域的服务器发送请求, 会返回最先到达的.

但这里假如使用无缓冲通道, 那么比较慢的两个goroutine就会卡住, 因为他们发送响应结果到通道时,

没有goroutine来接受, 这叫做goroutine泄漏属于bug, 泄漏的goroutine不会自动回收

### 8.5完并行循环

编写一个并行制作图片缩略图的方法

```go
func makeThumbnails4(filenames []string) error {
    errors := make(chan error)
    for _, f := range filenames {
        go func(f string) {
            _, err := thumbnail.ImageFile(f)
            errors <- err
        }(f)
    }

    for range filenames {
        if err := <-errors; err != nil {
            return err //不正确, goroutine泄漏
        }
    }
    return nil
}
```

上面当第一个发送了非nil的错误时, 它将错误返回给调用者

这样没有goroutine 继续从errors返回通道上进行接收, 会导致每一个现存的goroutine在试图给errors发送消息时永久阻塞

知道程序卡住 或者系统内存耗尽

简介方案有几个

- 创建一个足够容量的缓存通道, 这样没有工作goroutine在发送消息时候阻塞
- 或者, 主goroutine返回第一个错误的同时, 创建另一个goroutine来读完通道

```go
func makeThumbnails6(filenames <- chan string) int64 {
    sizes := make(chan int64)
    var wg sync.WaitGroup // 工作goroutine的个数
    for f := range filenames {
        wg.Add(1)
        go func (f string) {
            defer wg.Done() // 等价于 defer wg.Add(-1)
            thumb, err : thumbnail.ImageFile(f)
            if err != nil {
                log.Println(err)
                return
            }
            info, _ := os,Stat(thumb) //忽略错误
            sizes <- info.Size()
        }(f)
    }
    go func() {
        wg.Wait()
        close(sizes)
    }()
    var total int64
    for size := range sizes {
        total += size
    }
    return total
}
```

- sync.WaitGroup 可以被多个goroutine安全的操作
- Add方法必须在goroutine开始之前执行

问题1 wg.Wait为什么要用goroutine

否则就会把后面的for循环阻塞住, 导致通道塞住

### 8.6 示例: 并发的web爬虫

```go
func crawl(url string) []string {
    fmt.Println(url)
    list, err := links.Extract(url)
    if err != nil {
        log.Print(err)
    }
    return list
}

func main() {
    worklist := make(chan []string)

    go func() { worklist <- os.Args[1:]}()

    seen := make(map[string]bool)
    for list := range worklist {
        for _, link := range list {
            if !seen[link] {
                seen[link] = true
                go func(link string) {
                    worklist <- crawl(link)
                }(link)
            }
        }
    }
}
```

这是一个广度优先的搜索网址url方法

假如爬虫怕的url比较多

可能一开始是正常的, 后面会出现错误

```bash
dial tcp: loopup blog.golang.org: no such host
dial tcp: 23.21.222.120:443: socket: too many open files
```

这是因为我们没有限制并发数, 导致超过程序能打开文件的限制

可以用缓存通道来限制并发数

```go
var tokens = make(chan struct{}, 20)

func crawl(url string) []string {
    fmt.Println(url)
    tokens <- struct{}{} //获取令牌
    list, err := links.Extract(url)
    <- tokens // 释放令牌
    if err != nil {
        log.Print(err)
    }
    return list
}
```

- 为什吗用struct{}作为通道类型? 其实都可以, 只是因为struct{}占用的空间大小是0

现在还有个问题, 这个程序用于不会结束

这里需要一个计数器

```go
func main() {
    worklist := make(chan []string)
    var n int //等待发送到任务列表的数量
    go func() { worklist <- os.Args[1:]}()

    seen := make(map[string]bool)
    for ; n>0; n-- {
        list := <-worklist
        for _, link := range list {
            if !seen[link] {
                seen[link] = true
                n++
                go func(link string) {
                    worklist <- crawl(link)
                }(link)
            }
        }
    }
}
```

## 9. 使用共享变量实现并发

### 9.1 竞态

一个函数在并发调时仍然能正常工作, 那么这个函数是并发安全的

数据静态发生在 两个goroutine并发读写同一个变量并且至少有一个是写入时

避免数据竞争方法

- 不要通过共享内存来通信, 而是通过通信来共享内存, 即只有一个goroutine能修改数据
- 互斥锁机制

### 9.2 互斥锁: sync.Mutex

函数A加锁之后 调用了函数B, 函数B也要求加锁, 这就导致死锁

```go

func Withdraw(amount int) bool {
    mu.Lock
    defer mu.Unlock
    Deposit(-amount)
}

func Deposit(amount int) {
    mu.Lock()
    balance = blance + amount
    mu.Unlock()
}

```

### 9.3 读写互斥锁

大部分读的情况并发其实无需锁, 当没有写操作的时候, 所以读的时候可以只加读锁

这样没有写锁的时候, 多个读锁是互不影响的

```go
var mu sync.RWMutex
var balance int

func Balance() int {
    mu.RLock()
    defer mu.RUnlock()
    return balance
}
```

### 9.4 内存同步

```go
var x, y int

go func() {
    x = 1
    fmt.Print("y:", y, " ")
}

go func() {
    y = 1
    fmt.Print("x:", x, " ")
}
```

可能出现的结果

- x:0 , y:1
- x:1 , y:1
- y:0 , x:1
- y:1 , x:1
- x:0 , y:0
- y:0 , x:0

### 9.5 延迟初始化

延迟一个昂贵的初始化步骤到有时机需求的时候是一个很好的实践

```go
var icons map[string]image.Image

func loadIcons() {
    icons = map[string]image.Image{
        "spades.png": loadIcon("spades.png")
    }
}

// 线程不安全
func Icon(name string) image.Image {
    if icons == nil {
        loadIcons()
    }
    return icons[name]
}
```

容易出现的错误是

loadIcons其实是两行语句

```go
func loadIcons() {
    icons = make(map[string]image.Image)
    icons["spades.png"] = loadIcon("spades.png")
}
```

所以第一个goroutine进入`loadIcons`执行完了`icons = make(map[string]image.Image)`, 但未进行元素赋值

这时第二个goroutine进来了, 会判断icons为nil, 但其实初始化还未完成

简单的处理方式

```go
func Icon(name string) image.Image {
    mu.Lock()
    defer mu.Unlock()
    if icons == nil {
        loadIcons()
    }
    return icons[name]
}
```

虽然这样改造后是并发安全, 但是每次进来都得加下锁, 其实在确认初始化完毕后, 完全没必要用锁

我们改造下

```go
var mu sync.RWMutex
var icons map[string]image.Image

func Icon(name string) image.Image {
    mu.RLock()
    if icons != nil {
        icon:icons[name]
        mu.RUnlock()
        return icon
    }
    mu.RUnlock()

    mu.Lock()
    if icons == nil {
        loadIcons()
    }
    icon := icons[name]
    mu.Unlock()
    return icon
}
```

这样就效率和并发安全都ok, 就是代码有点繁琐..

sync提供了针对一次性初始化的问题的特定方案: sync.Once

```go

var loadIconsOnce sync.Once
var icons map[string]image.Image

func Icon(name string) image.Image {
    loadIconsOnce.Do(loadIcons)
    return icons[name]
}
```
`Do`会锁定互斥量并检查里边的布尔变量, 第一次这个布尔变量为假, D偶会调用loadIcons 然后把变量设置为真

后续的调用相当于空操作

### 9.6 竞态检测器

竞态检测器会找到一些有问题的案例, 只需要在go build / go run / go test里增加-race参数

一个goroutine写入一个变量后 中间没有任何同步的操作, 就有另一个goroutine读写了该变量

但是它只能检测到那些在运行时发生的竞态

### 9.7 并发非阻塞缓存

最简单的述求

使用http请求一个url

```go
func httpGetBody(url string) (interface{}, error) {
    resp, err := http.Get(url)
    if err != nil {
        return nil, err
    }
    defer resp.Body.Close()
    return ioutil.ReadAll(resp.Body)
}
```

线程不安全的缓存方法

```go
package memo

type Memo struct {
    f func
    cache map[string]result
}

type Func func(key string) (interface{}, err)

type result struct {
    value interface{}
    err error
}

func New(f Func) *Memo {
    return &Memo{f: f, cache: make(map[string]result)}
}

// 线程不安全
func (memo *Memo) Get(key string) (interface{} err) {
    res, ok := memo.cache[key]
    if !ok {
        resp.value, resp.err := memo.f(key)
        memo.cache[key] = res
    }
    return res.value, res.err
}
```

使用线程不安全的缓存方法

```go
m := memoNew(httpGetBody)
for url := range incomingURLs() {
    value, err := m.Get(url)
}
```

这里每次获取都是串行的, 所以并发不安全的缓存方法也没关系, 但是假如我们想并行, 就需要改造了

改造缓存通用包

```go
type entry struct {
    res result
    ready chan struct{} //res 准备好后会被关闭
}

type New(f Func) *Memo {
    return &Memo{f:f, cache: make(map[string]*entry)}
}

type Memo struct {
    f Func
    mu sync.Mutex //保护cache
    cache map[string]*entry
}

func (memo *Memo) Get(key string) (value interface{}, err error) {
    memo.mu.Unlock()
    e := memo.cache[key]
    if e == nil {
        e = &entry{ready: make(chan struct{})}
        memo.cache[key] = e
        memo.mu.Unlock
        e.res.value, e.res.err = memo.f(key)
        close(e.ready) // 广播数据已准备完毕的消息
    } else {
        memo.mu.Unlock
        <-e.ready //等待数据准备
    }
    return e.res.value, e.res.err
}

```

这里的核心是

- 锁和解锁中间不执行通信任务
- 通信任务通过通道来通知完成

我们想象另一种解决不用锁, 而用通道

```go
type Func func(key string )(interface{}, error)
type result struct {
    value interface{}
    err error
}

type entry struct {
    res result
    ready chan struct{}
}

func (e *entry) call (f Func, key string){
    e.res.value, e.res.err = f(key)
    close(e.ready) //通知数据已加载完毕
}

func (e *entry) deliver(response chan<- result) {
    // 等待数据加载完成
    <-e.ready
    //向客户端发送结果
    response <- e.res
}

type request struct {
    key string
    response chan <- result
}
type Memo struct{ requests chan request}

func New(f Func) *Memo {
    memo := &Memo{requests: make(chan request)}
    go memo.server(f)
    return memo
}

func (memo *Memo) server(f Func) {
    cache := make(map[string]*entry)
    for req := range memo.requests {
        e := cache[req.key]
        if e == nil {
            e = &entry{ready: make(chan struct{})}
            cache[req.key] = e
            go e.call(f, req.key)
        }
        go e.deliver(req.response)
    }
}

func (memo *Memo) Get(key string) (interface{}, error) {
    response := make(chan result)
    memo.requests <- request{key, response}
    res := response
    return res.value, res.err
}
```

- 用无缓冲通道来起到锁的作用

### 9.8 goroutine和线程

> 9.8.1 可增长的栈

每个OS线程都有一个固定大小的栈内存(通常2MB)

而线程典型状态下是2KB, 而也可以栈大小可达1GB

> 9.8.2 goroutine调度

OS线程由OS内核来调度, 每隔几毫秒, 一个硬件时钟中断发到CPU, CPU调用一个叫调度器的内核函数

- 函数暂停正在运行的线程
- 把线程的寄存器信息保存到内存
- 查看线程列表决定下一个运行哪个线程
- 再从内存回复线程的注册表信息
- 执行选中的线程

goroutine使用m:n调度技术(可以复用/调度每个gourmet到n个OS线程)

与线程调度器不同, Go调度器不由硬件时钟发送, 而由Go语言结构来触发

比如一个goroutine调用time.Sleep或被通道阻塞或互斥量操作时, 调度器会把这个goroutine设为休眠模式

因为它不需要切换到内核语境, 所以一个goroutine比调度一个线程成本低很多

> 9.8.3 GOMAXPROCS

Go调度器使用GOMAXPROCS参数来决定使用多少个OS线程来同时执行Go代码

一般是机器上的CPU数量, GOMAXPROCS是`m:n调度`中的n

- 正在休眠或者阻塞的goroutine不占用线程
- 阻塞在I/O和其他系统调用中或调用非Go语言编写的函数时, goroutine需要一个独立的OS线程, 但这个线程不计算在GOMAXPROCS内


```go
for {
    go fmt.Print(0)
    fmt.Print(1)
}
```

```bash
> GOMAXPROCS=1 go run hacker-cliche.go // 1111111111111000000111111
> GOMAXPROCS=2 go run hacker-cliche.go // 010101010101100110010101010
```
第一个因为每次最多有一个goroutine运行, 所以一开始是主goroutine, 它输出1, 执行一段时间后, Go调度器让主goroutine休眠, 唤起另一个goroutine

第二个因为有3个goroutine, 所以就比较随机了

## 10 包和Go工具

可在godoc.org找包

### 10.5 空导入

```go
import _ "image/png"
```

作用在副作用代码, 例如`init`中主动向其他包注册内容

例如png包

```go
package png
func Decode(r io.Reader) (image.Image, error)
func DecodeConfig(r io.Reader) (image.Config, error)

func init() {
    const pngHeader ="xxx"
    image.RegisterFormat("png", pngHeader, Decode, DecodeConfig)
}
```

这样在主程序引用了image包和空的png包, 那么image包就有了解析png的能力

### 10.7 go工具

> 10.7.5 内部报

go build会识别路径还有`internal`的情况, 这种包叫内部包

内部包只能被另一个包导入, 这个包位于internal目录的父目录为根目录的树中

例如`net/http/internal/chunked` 可以从`net/http/httputil`或`net/http`导入

但不能从`net/url`导入

然而`net/url` 可以导入`net/http/httputil`

## 11.测试

### 11.1 go test工具

以`_test.go`结尾的文件不是go build的目标, 而是go test编译的目标

在测试文件中, 有3类函数会特殊对待

- 功能测试函数: 以Test前缀开头, go test会报告结果PASS|FAIL
- 基准测试函数: 以Benchmark开头, 用来测试某些操作的性能, go test 会报告平均执行时间
- 示例函数: 以Example开头, 用来提供机器检测过的文档

### 11.2 Test函数

每一个测试文件必须导入testing包, 函数签名为

```go
func TestName(t *testing.T) {
    
}
```

`t.Error()`|`t.Errorf()`是让功能测试失败的方法, 这样异常不包括跟踪栈信息

只运行某些测试用例

`go test -v -run="正则"`

可以用`t.Fatal()`|`t.Fatalf()`来终止测试, 这些调用必须吆喝Test函数在一个goroutine

一般的错误字符提示是`f(x)=y, want z`

> 11.2.3 白盒测试

- test可以覆盖同包的非导出函数
- 覆盖完非导出函数, 需要记得还原

```go
func TextName(t *testing.T) {
    saved := notifyUser
    defer func(){ notifyUser = saved }()
}
```

> 11.2.4 外部测试包

`net/url`包被`net/http`包依赖

但`net/url`包想写跟`net/http`的交互的测试用例

因为`net/http`依赖`net/url`包, 所以`net/url`测试用例引入`net/http`包的话 就是循环依赖, Go不允许

这时我们可以把测试函数定义在外部测试包解决这个问题, 例如在`net/url`的路径上定义一个`net/url_test`的测试文件

我们可以使用`go list`来查找这个包的产品代码, 包内测试代码和包外测试代码

```bash
> go list -f={{.GoFiles}} fmt
[doc.go format.go print.go scan.go]

> go list -f={{.TestGoFiles}} fmt
[ export_test.go]

> go list -f={{.XTestGoFiles}} fmt
[fmt_test.go scan_test.go stringer_test.go]
```

我们模块会因为封装, 不把一些方法/变量暴露给其他模块,

但是有时为了满足其他包来测试我们的包, 我们可以通过创建`export_test.go`文件来向外部测试包, 暴露模块内部的函数和变量

### 11.3 覆盖率

测试旨在发现bug, 而不是证明其存在

### 11.4 Benchmark函数

```go
import "testing"

func BenchmarkIsPalindrome(b *testing.B) {
    ...
}
```

可以通过pprof分析性能

```bash
go tool pprof -text -nodecount=10 ./http.test cpu.log
```

## 12 反射

在编译时不知道类型的情况下, 可更新变量、在运行时查看值、调用方法以及直接对它们的布局进行操作

这种机制称为反射

### 12.1 为什吗使用反射

需要写一个函数有能力统一处理各种值类型的函数, 而这些类型可能

- 无法共享同一接口
- 布局未知
- 在我们设计函数时还粗存在(例如`fmt.Printf`)

### 12.2 reflect.Type和reflect.Value

反射功能主要由`reflect`包提供, 定义了两个重要的类型

`Type`: Go语言的一个类型

`reflect.TypeOf`函数接受任何的`interface{}`参数, 并把接口中的动态类型以`reflect.Type`形式返回

```go
t := reflect.Typeof(3) // 一个reflect.Type
fmt.Println(t.String()) // int
fmt.Println(t) // int
```

把一个具体值赋给接口类型时会发生一个隐式类型转换, 转换会生成包含两个部分的接口值

- 动态类型部分是操作数的类型(int)
- 动态值部分是操作数的值(3)

reflect.Typeof返回一个接口值对应的动态类型, 所以它返回总是具体类型(而不是接口类型)

```go
var w io.Writer = os.Stdout
fmt.Println(reflect.Typeof(w)) // 返回是"*os.File" 而非io.Writer
```

reflect.Valueof函数接受任意的interface{}, 并将接口的动态值以reflect.Value的形式返回

reflect.Valuof的逆操作是reflect.Value.Interface, 返回一个interface{}接口值

```go
v := reflect.Valueof(3) // 一个reflect.Value
x := v.Interface() // an interface{}
i := x.(int) // an int
fmt.Printf("%d\n", i) // 3
```

reflect.Value.Kind() 方法可以获取少数的几种类型

- 基础类型Bool, string, 各种数字类型
- 聚合类型Array和Struct
- 引用类型: Chan, Func, Ptr, Slice, Map, Interface
- Invalid类型 表示没有任何值, reflect.Value的零值就是Invalid类型

```go
package format

import (
    "reflect"
    "strconv"
)

func Any(value interface{}) string {
    return formatAtom(reflect.Valueof(value))
}

func formatAtom(v reflect.Value) string {
    switch v.Kind() {
        case reflect.Invalid:
            return "invalid"
        case reflect.Int, reflect.Int8, reflect.Int16,
            reflect.Int32, reflect.Int64:
            return strconv.formatUnit(v.Unit(), 10)
        // 省略浮点数和复数类型
        case reflect.Bool:
            return strconv.FormatBool(v.Bool())
        case reflect.String:
            return strconv.Quote(v.String())
        case reflect.Chan, reflect.Func, reflect.Ptr, reflect.Slice, reflect.Map:
            return v.Type().String() + " 0x" + strconv.FormatUnit(uint64(v.Pointer(), 16))
        default: // reflect.Array, reflect.Struct, reflect.Interface
            return v.Type().String() + " value"
    }
}

var x int64 = 1
var d time.Duration = 1 * time.Nanosecond
fmt.Println(format.Any(x)) // 1
fmr.Println(format.Any(d)) // 1
fmt.Println(format.Any([]int64{x})) // []int64 0x8202b87b0
fmt.Println(format.Any([]time.Duration{d})) // []time.Duration 0x8202b87e0
```

### 12.3 Display 一个递归显示器

即使非导出字段在反射中也是可见的
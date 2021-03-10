# TypeScript 编程

# 2 TypeScript概述

### 2.2 类型系统

```typescript
let a: number = 1
let b: string = 'hello'
let c: boolean[] = 'true false'

// 以下写法与上面等效, typescript会自动推算类型
let a = 1
let b = 'hello'
let c = [true, false]
```

一般来说, 最好让TypeScript推导类型, 少数情况下显式注解类型

除非是把就的js代码转为ts代码, 不然应该100%具有类型覆盖

## 2.3 代码编译器设置

### 2.3.1 tsconfig.json

这里的配置是关于`tsc`编译相关的

配置的可选项都在这 [tsconfig.json配置选项](http://json.schemastore.org/tsconfig)

[tsconfig.json官方文档说明](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)

这里写个最常见的demo

```json
{
    "compilerOptions": {
        "module": "commonjs",
        "esModuleInterop": true,
        "allowSyntheticDefaultImports": true,
        "target": "es2018",
        "noImplicitAny": true,
        "moduleResolution": "node",
        "sourceMap": true,
        "outDir": "dist",
        "baseUrl": ".",
        "paths": {
            "*": [
                "node_modules/*",
                "src/types/*"
            ]
        },
        "lib": ["es2018"]
    },
    "include": [
        "src/**/*"
    ],
    
}
```

|配置|配置说明|
|---|---|
|strict|检查代码尽量严格, 会强制所有代码都正确声明了类型, 建议你设置为true|

### 2.3.2 tslint.json

这个文件是设置检查代码风格的

看了下`vue3`, `puppeteer`大部分都是ts写的, 但是还是用eslint来做代码风格检查

但是, 简单来配置, 用tslint估计也不会有什么问题

```bash
- npm install --save-dev tslint typescript 
```

创建文件`tslint.json`

```json
{
    "defaultSeverity": "error",
    "extends": [
        "tslint:recommended"
    ],
    "rules": {
        "semicolon": false,
        "trailing-comma": false
    }
}
```

### 2.4 index.ts

第一个ts文件

```typescript
console.log("Hello World")
```

编译和运行代码

```bash
- ./node_modules/.bin/tsc
- node ./dist/index.js
```

可以参考[微软官方推荐的脚手架](https://github.com/microsoft/TypeScript-Node-Starter)

看里面的结构和package.json中scripts的内容

### 3 类型全解

## 3.2 类型浅谈

### 3.2.1 any

尽量不要用`any`

### 3.2.2 unknown

unknown与any类似 都表示任意值

但是unknown 需要开发先用typeof和instanceof运算符细化

```typescript
let a :unknown = 30
let b = a === 123
let c = a + 10 // Error Ts2571 Object is type unknown
if (typeof a === 'number') {
    let d = a + 10 // 40
}
```

### 3.2.4 number

number包括 整数、浮点数、正数、负数、Infinity、NaN等

处理较长数字时, 可以用`_`作为分隔

```typescript
let oneMillion = 1_000_000 //等同于1000000
```

### 3.2.5 bigint

number最大的整数是2的53次方

bigint大得多, 支持+, -, *, / 和比较

```typescript
let a = 1234n
```

### 3.2.7 symbol

创建一个唯一的字符串, 一般用作对象中key, 而防止key在对象中重复而被覆盖

```typescript
let a = Symbol('a')
```

### 3.2.8 对象

```typescript
let a: object = {
    b: 'x'
}
a.b // Error TS2339 Property 'b' does not exist on type 'object'
```

object的范围只比any范围窄一点, 只能表示是一个js对象(非null)

```typescript
let a = {
    b: 'x'
}
a.b // x string
```

也可以给b加个类型推导

```typescript
let a: {b: string} = {
    b: 'x'
}
```

灵活的给对象增加类型推导

```typescript
let a: {
    b: number // 1
    c?: string // 2
    [key: number]: boolean // 3
    readonly firstName: string // 4
}
```

- 1: a必须有个类型为number的属性b
- 2: a可能有个类型为string的属性c, 如果有属性c, 其值可能为undefined
- 3: a可能有任意多个数字属性, 其值为布尔值
- 4: firstName为只读属性, 必须在初始化时赋值

### 3.2.9 类型别名、交集和并集

```typescript
type Age = Number
type Person = {
    name: string
    age:Age
}
```

并集使用`|`, 交集使用`&`

```typescript
type Cat { name: string, purrs: boolean }
type Dog = { name: string, barks: boolean, wags: boolean }
type CatOrDogOrBoth = Cat | Dog
type CatAndDog = Cat & Dog

// Cat
let a: CatOrDogOrBoth = {
    name: 'Bonkers',
    purrs: true
}

// Dog
a = {
    name: 'Domino',
    barks: true,
    wags: true
}

//两者
a = {
    name: 'Donkers',
    barks: true,
    purrs: true,
    wags: true
}

```

### 3.2.10 数组

声明数组

```typescript
let a = [1, 2, 3]
let c: string[] = ['a']
let d = [1, 'a'] // (string | number ) []
let e: Array<(string | number )> = [1, 'a'] 
```

在同一个数组处理不同基础类型的数据时, 必须使用typeof进行判断

```typescript
let d = [1, 'a']
d.map(_ => {
    if (typeof _ === 'number') {
        return _ * 3
    }
    return _.toUpperCase()
})
```

ts会根据数组里的内容 不断推导数组类型

```typescript
function buildArray() {
    let a = [] // any[]
    a.push(1) // number[]
    a.push('x') //(string |number )[]
}
```

### 3.2.11 元组

元组是array的子类型

是定义数组的特殊方式: 长度固定, 各索引位上的值具有固定的已知类型

```typescript
let b: [string, string, number] = ['malcolm', 'abc', 1234]
```

可选元素

```typescript
let tranFares: [number, number?][] = [
    [3.1],
    [2,1],
]

// 等价于

let tranFares: ([number] | [number, number]) [] = { ... }
```

剩余元素

```typescript
// 至少一个string元素的数组
let friends: [string, ...string[]] = ['Sara', 'Tali']
```

只读数组

```typescript
let as: readonly number[] = [1,2,3]
```

只读数组无法使用push和splice方法 还有`as[0]=5`这种复制方法

只能通过复制数组的方法, 例如`.concat`和`.slice`方法

### 3.2.12 null、undefined、void和never

void表示函数没有显式的返回值

never表示函数根本不返回(例如函数抛出异常, 或者永远运行下去)

```typescript
//返回never的函数
function d() {
    throw TypeError('error')
}

//返回never的函数
function e(){
    while(true) {
        xxx
    }
}
```

### 3.2.13 enum 枚举

枚举可分为, 字符串->字符串的映射 或字符串到数字之间的映射

```typescript
enum Language {
    English,
    Spanish,
    Russian
}

// 下面与上面结果一致

enum Language {
    English = 0,
    Spanish = 1,
    Russian = 2
}
```

typescript会自动按顺序给枚举值得默认赋值

```typescript
enum Language {
    English = 100,
    Spanish = 500,
    Russian
}

// 下面与上面结果一致

enum Language {
    English = 100,
    Spanish = 500,
    Russian = 501
}
```

> enum 不安全索引问题

```typescript
enum Language {
    English = 100,
    Spanish = 500,
    Russian
}

let a = Language.English // 100
let b = Language.Spanish // 500
let c = Language.aaa // Error TS2339, Property 'aaa' does not exist on type 'typeof Language'
let d = Language['English'] // 100
console.log(d)

let e = Language[6] // string
console.log(e) // undefined

let f = Language[100] // English
console.log(f)
```

ts并不会阻止`e`这样的访问方式

改为const会只允许使用Key来查询值

```typescript
const enum Language {
    English = 100,
    Spanish = 500,
    Russian
}

let a = Language.English // 100
let b = Language.Spanish // 500
let d = Language['English'] // 100

let e = Language[6] // A const enum member can only be accessed using a string literal.ts(2476)

let f = Language[100] // A const enum member can only be accessed using a string literal.ts(2476)
```

默认情况下`const enum`编译是使用内插的

```typescript
const enum Language {
    English = 100,
    Spanish = 500,
    Russian
}

let a = Language.English // 100
let b = Language.Spanish // 500
let d = Language['English'] // 100

export default Language
```

这部分代码会编译成

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
let a = 100 /* English */; // 100
let b = 500 /* Spanish */; // 500
let d = 100 /* 'English' */; // 100
console.log(d);
//# sourceMappingURL=test.js.map
```

我们的`export default Language`会直接消失掉

而 假设设置`const enums`为非内插模式

设置`tsconfig.json`的`compilerOptions.preserveConstEnums`为true

编译后的结果是

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
var Language;
(function (Language) {
    Language[Language["English"] = 100] = "English";
    Language[Language["Spanish"] = 500] = "Spanish";
    Language[Language["Russian"] = 501] = "Russian";
})(Language || (Language = {}));
let a = 100 /* English */; // 100
let b = 500 /* Spanish */; // 500
let d = 100 /* 'English' */; // 100
exports.default = Language;
console.log(d);
//# sourceMappingURL=test.js.map
```

这种模式的代码才是正常的

即使是这种模式, 假如这部分代码发布到npm, 业务方更改了Language中的属性值, 原库也是不知道的

使用`const enum`时尽量避免使用内插, 而且只在受自己控制的ts程序中使用, 不要发布到npm

# 4 函数

### 4.1.4 注解this的类型

想为函数绑定this的类型

```typescript
function fancyDate(this: Date) {
    return $(this.getDate())
}
```

### 4.1.7 调用签名

定义函数的参数和返回值类型

```typescript
type Log = (message: string, userId?: string) => void
let log: Log (
    message,
    userId: 'xxxx'
) => {
    console.log(message)
}
```

### 4.1.9 函数类型重载

```typescript
type CreateElement = {
    (tag: 'a'): HTMLAnchorElement
    (tag: 'canvas'): HTMLCanvasElement
    (tag: string): HTMLElement
}

let createElement: CreateElement = (tag: string): HTMLElement => {
    ....
}

```

## 4.2 多态

```typescript

function

type Filter = {
    (array: number[], f: (item: number) => boolean): number[]
    (array: string[], f: (item: string) => boolean): string[]
}

let filter: Filter (
    array,
    f
) => {
    let result = []
    for(let i = 0; i < array.length; i++) {
        let item = array[i]
        if(f(item)) {
            result.push(item)
        }
    }
    return result
}
```

增加Filter的灵活性还可以使用泛型

```typescript
type Filter = {
    <T>(array: T[], f: (item: T) => boolean ): T[]
}
```

### 4.2.1 什么时候绑定泛型

声明泛型的位置不仅限定泛型的作用域

还觉得TypeScript何时绑定具体的类型

```typescript
type Filter = {
    <T>(array: T[], f: (item: T) => boolean ): T[]
}

let filter: Filter = (array, f) => { ...}
```

`<T>`声明在调用签名中声明(位于签名的开始圆括号前面), TypesCript将在调用Filter类型的函数时为T绑定具体类型

而如把`T`绑定在类型别名`Filter`中, TypeScript泽要求在使用时显式绑定类型

```typescript
type Filter<T> = {
    (array: T[], f: (item: T) => boolean ): T[]
}

let filter: Filter<number> = (array, f) => { ... }
```

### 4.2.2 可以在什么地方声明泛型

```typescript
type Filter = {
    <T>(array: T[], f: (item: T) => boolean ): T[]
}

type Filter<T> = {
    (array: T[], f: (item: T) => boolean ): T[]
}

function filter<T>(array: T[], f: (item: T) => boolean): T[] { ...}
```

标准库中的filter和map函数


```typescript
interface Array<T> {
    filter(
        callbackfn: (value: T, index: number, array: T[]) => any,
        thisArg?: any
    ): T[]
    map<U>(
        callbackfn: (value: T, index: number, array: T[]) => U,
        thisArg?: any
    ): U[]
}

```

### 4.2.3 泛型推导

```typescript
function map<T, U>(array: T[], f: (item: T) => U): U[] {
    // ...
}

map(
    ['a', 'b', 'c'],
    _ => _ === 'a'
)
```

上面TypeScript会自动推导T为string, U为boolean

但也可以显示写注解

```typescript
map <string, boolean>(
    ['a', 'b', 'c'],
    _ => _ === 'a'
)
```

```typescript
let promise = new Promise(resolve =>
    resolve(45)
)
promise.then(result => // 推导结果为{}
    result * 4 // Error T2362
)
```

这里因为我们没有提供足够的信息, TypeScript通过泛型函数的参数类型推导泛型的类型

所以T默认是{}

可更正为

```typescript
let promise = new Promise<number>(resolve =>
    resolve(45)
)
promise.then(result => // 推导结果为{}
    result * 4 // Error T2362
)
```

### 4.2.4 泛型别名

注意, 在类型别名中只有一个地方可以声明泛型, 即紧随类型别名的名称之后、赋值运算之前


```typescript
type MyEvent<T> = {
    target: T
    type: string
}

```

使用MyEvent这样的泛型时, 必须显式绑定类型参数, TypeScript无法自动推导

```typescript
let myEvent: MyEvent<HTMLButtonElement | null > = {
    target: document.querySelector('#myButton')
    type: 'click'
}
```

类型别名也可以在函数签名中使用

```typescript
function triggerEvent<T>(event: MyEvent<T>): void {
    ...
}

triggerEvent({ //T 是Element | null
    target: document.querySelector('#myButton')
    type: 'click'
})

```

下面说下TypeScript的编译步骤

- 调用triggerEvent传入一个对象
- 根据函数签名, TypeScript认定传入的参数必为MyEvent<T>
- MyEvent<T>与传入的对象进行匹配, 发现T应该为`document.querySelector('#myButton')`的返回类型, 即为`Element | null`
- TypeScript检查所有代码, 把T都替换为Element | null
- 确定所有类型都满足可赋值性, 确保代码类型安全

### 4.2.5 受限的多态

这里以二叉树为例

主要为了演示函数如何处理多种同类型的返回值

```typescript
type TreeNode = {
    value: string
}

type LeafNode = TreeNode & {
    isLeaf: true
}

type InnerNode = TreeNode & {
    children: [TreeNode] | [ TreeNode, TreeNode]
}

```

TreeNode 算是父类, 有两种类型的子类 一种是空节点, 一种是有子节点的

一般处理TreeNode的方法都需要对这三种类型进行兼容

使用demo

```typescript
let a: TreeNode = { value: 'a' }
let b: LeafNode = { value: 'b', isLeaf: true }
let c: InnerNode = { value: 'c', children: [b] }

let a1 = mapNode(a, _ => _.toUpperCase()) // TreeNode
let b1 = mapNode(b, _ => _.toUpperCase()) // LeafNode
let c1 = mapNode(c, _ => _.toUpperCase()) // InnerNode
```

这个mapNode 应该如何实现

```typescript
function mapNode<T extends TreeNode> (
    node: T,
    f: (value: string) => string
): T {
    return {
        ...node,
        value: f(node.value)
    }
}

```

- mapNode函数定义了泛型参数T, T的上限为TreeNode, 即T可以是TreeNode, 也可以是TreeNode的子类型

为什吗要这样声明T呢?

- 如果只输入T(没有extends), 那么mapNode会提示不能安全读取node.value
- 如果根本不用T, 即声明为(node: TreeNode, f: (value: string) => string ) => TreeNode, 那么a1, b1, c3 都会丢失信息称为TreeNode

> 使用受限的多态模拟边长参数

模拟call

```typescript
function call<T extends unknown[], R>(
    f: (...args: T) => R,
    ...args:T
): R {
    return f(...args)
}
```


# 5. 类和接口 

## 5.1 类和继承

TypeScript支持3个访问修饰符

- public: 任何地方都可访问, 默认为这个
- protected: 只有当前类和子类可以访问
- private: 只有当前类的实例可以访问

## 5.4 接口

类型别名和接口的区别

- 类型别名更加通用, 右边可以是任何类型, 或者类型表达式(&,|等运算符), 而接口右边必须为结构

例如下面这些无法用接口重写

```typescript
type A = number
type B = A | string
```

- 同一作用域中, 多个同名接口自动合并, 同一作用域的多个类型别名将导致编译错误

### 5.4.1 声明合并

以下User的接口将会自动合并

```typescript
interface User { name: string }
interface User { age: number }

let a: User = {
    name: 'xxx',
    age: 30
}
```

假设接口里定义了冲突的字段说明, 将会报错


## 5.7 多态

- 构造方法不能声明泛型, 应该在类声明中声明泛型
- 静态方法不能访问类的泛型

# 6 类型进阶

## 6.1 类型之间的关系

### 6.1.2 型变

先声明下这里的特殊符号

- `A <: B`: A类型是B类型的子类型, 或者同种类型
- `A >: B`: A类型是B类型的超类型, 或者同种类型

Ts中 想要保证A对象可赋值给B对象,

必须保证A对象的每个属性都是B属性的子类型或同种类型

这种柜子的转变叫为协变

型变有四种

- 不变: 只能是T
- 协变: 可以是<:T
- 逆变: 可以是>:T
- 双变: 可以是 <:T 或 >:T

对象, 类, 数组, 和函数的返回类型都是协变

但是函数的参数类型进行逆变


> 函数型变

如果函数A的参数数量少于或等于函数B的参数数量, 满足以下条件, 函数A就是函数B的子类型

- 函数A的this类型未指定, 或者>:函数B的this类型
- 函数A的各个参数类型>:函数B的相应参数
- 函数A的返回类型<:函数B的返回类型

### 6.1.4 类型拓展

let 和var声明的变量会进行类型拓展

```typescript
let a = 'x' // string
let b = 3 // number
var c = true // boolean
const d = {x: 3 }// { x: number}

enum E { X, Y, Z}
let e = E.X // E
```

如果使用const时推导的就不一样


```typescript
const a = 'x' // 'x'
const b = 3 // 3
const c =  true // true

enum E { X, Y, Z}
const e = E.X // E

```

初始化为null或undefined会拓展为any

```typescript
let a = null // any
a =  3 // any
a = 'b' //any

```

但是脱离了声明的作用域后, 变量会重新分配类型


```typescript
function x () {
    let a = null
    a = 3
    a = 'b'
    return a
}

x() // string
```

> const类型

const 不仅能繁殖拓展类型, 还会递归的将成员设为readonly

```typescript
let a = { x: 3} // {}
let b: {x: 3} // {x:3}
let c = {x: 3} as const // {readonly  x: 3}

let d = [1, {x: 2}] // (number | { x: number })[]
let e = [1, {x: 2}] as const // readonly [1, { readonly x: 2}]
```
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
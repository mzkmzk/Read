# 重构改善既有代码的设计

重构是在不改变软件可观察行为的前提下改善其内部结构

## 1.重构第一个案例

重构步骤

1. 写好测试,保证输入输出一致
2. 提取冗长的`switch`
3. 每次修改的篇幅尽量原子,更容易发现错误
4. 重构时,变量改名也重要.
5. 函数应该放在它所使用的数据的所属对象内
5. 去除临时变量
6. 对于常改复杂的switch,建立多态取代switch

## 2. 重构原则

重构: 对软件内部结果的一种调整,目的是在不改变软件可观察行为的前提下,提高其理解性,降低其修改成本

重构的意义

1. 让代码更有结构性
2. 消除重复代码 
3. 把设计模式看出来
4. 重构帮助找到BUG
5. 重构能提高编程速度

何时重构 : 随时随地的进行

基本情况是一下三种,但这三种基本伴随着项目生命周期

1. 添加功能时 : 当我添加功能感觉到困难时,我就会尝试重构能否给我带来便利
2. 修补错误时重构 : 当调试时无法理解代码,就用重构加深自己的理解,因为显然代码还不够清晰--没有清晰到让我一眼就看出BUG
3. 复审代码时重构 : 

重构的难点:

1. 新旧接口: 尽量让旧接口调用新接口
2. 数据库: 苦逼的数据迁移吧

## 3.代码的坏味道

1. 重复代码
2. 过长函数
3. 过大的类
4. 过长的参数列
5. 发散式修改 : 一个类收多个变化影响
6. 散弹式修改 : 一个变化引起多个类响应修改
7. 依恋情结: Model和Controller结合在一起
8. 基本类型偏执: 当有多组基本类型经常出现在一起,应该包装程磊
9. switch惊悚现身
10. 平行继承体系: 当你要增加一个子类,相对应要在别的地方增加一个子类
11. 冗余类: 多余的类
12. 夸夸其谈未来性
13. 临时字段
14. 过度耦合的消息链
15. 中间人: 过度使用委托,当一个类的接口一般函数都委托给其他类.
16. Inappropriate Intimacy: 当子类对父类的了解总是超过后者的主观愿望,就把他移出继承
17. 异曲同工类: 实现类似功能的代码
18. 不完美的类库: 类库出现满足不我们的需求,需要进行大量修改从而达到目的时
19. 纯字段而无作用的类
20. 被拒绝的遗赠: 子类只使用极少的父类功能
21. 过多的注释

## 4.构筑测试体系

测试: 测试你最担心出错的部分

## 5. 重构列表

以下就是我的重构手法了

## 6. 重新组织函数

### 6.1 Exract Method

要诀就是短短短,一个函数实现的功能尽量原子化.

动机: 

1. 被复用机会大

做法:

1. 如果想不出有意义的函数名,就别动了

### 6.2 Inline Method

发现内部代码和函数名称同样清晰易读,非必要的间接性调用

### 6.3 Inline Temp

### 6.4 Replace Temp with Query

### 6.5 Introduce Explaining Variable

### 6.6 Split Temporary Variable

### 6.7 Remove Assignments toPrameters

### 6.8 Replace Method with Method Object

### 6.9 Substitute Algorithm

## 7. 在对象之间搬移特性

### 7.1 Move Method

### 7.2 Move Field 

### 7.3 Extract Class

### 7.4 Inline Class

### 7.5 Hide Delegate

### 7.6 Remove Middle Man

### 7.7 Introduce Foreign Method

### 7.8 Introduce Local Extension

## 8. 重新组织数据

### 8.1 Self Encapsulate Field

### 8.2 Replace Data Value with Object

### 8.3 Change Value to Reference 

### 8.4 Change Reference to Value

### 8.5 Replace Array with Object 

### 8.6 Duplicate Observed Date

### 8.7 Change Unidirectional Association to Bidirectional

### 8.8 Change Bidirectional Association toUnidirectional 

###8.9 Replace Magic Number with Symbolic Constant

### 8.10 Encapsulate Field

### 8.11 Encapsulate Collection

### 8.12 Replace Record with Data Class

### 8.13 Replace Type Code with Class(以类取代类型码)

### 8.14 Replace Type Code with Subclasses(以子类取代类型码)

### 8.15 Replace Type Code with State/Strategy(以State/Strategy取代类型码)

### 8.16 Replace Sbuclass with Fields(以字段取代子类)

## 9 简化条件表达式

### 9.1 Decompose Conditional(分解条件表达式)

### 9.2  Consolidate Conditional Expression(合并条件表达式)

### 9.3 Consolidate Duplicate Conditional Fragments (合并重复的条件片段)

### 9.4 Remove Control Flag(移除控制标记)

### 9.5 Replace Nested Conditional with Guard Clauses(以卫语句取代嵌套条件表达式)

### 9.6 Replace Conditional with Polymorphism (以多态取代条件表达式)

### 9.7 Introduce Null Object(引入NULL)

### 9.8 Introduce Assertion (引入断言)

## 10 简化函数调用

### 10.1 Rename Method (函数改名)

### 10.2 Add Parameter(添加函数)

### 10.3 Remove Parameter (移除参数)

### 10.4 Separate Query from Modifier(将查询函数和修改函数分离)

### 10.5 Parameter Method(令函数携带参数)

### 10.6 Replace Parameter with Explicit Methods(以明确函数取代参数)

### 10.7 Preserve Whole Object(保持对象完整)

### 10.8 Replace Parameter with Methods(以函数取代参数)

### 10.9 Introduce Parameter Object(引入参数对象)

### 10.10 Remove Setting Method(移除设置函数)

### 10.11 Hide Method(隐藏函数)

### 10.12 Replace Constructor with Factory Method(以工厂函数取代构造函数)

### 10.13  Encapsulate Downcast (封装向下转型)

### 10.14 Replace Error Code with Exception (以异常取代错误码)

### 10.15 Replace Exception with Test(以测试取代异常)

## 11 处理概括关系

### 11.1 Pull Up Field(字段上移)

### 11.2 Pull Up Method(函数上移)

### 11.3 Pull Up Constructor Body(构造函数本体上移)

### 11.4 Push Down Method(函数下移)

### 11.5 Push Down Field (字段下移)

### 11.6 Extract Subclass(提炼子类)

### 11.7 Extract Superclass(提炼超类)

### 11.8 Extract Interface(提炼接口)

### 11.9 Collapse Hierachy(折叠继承体系)

### 11.10 Form Template Method(塑造模板函数)

### 11.11 Replace Inheritaance with Delegation (以委托取代继承)

### 11.12 Replace Deletegation with Inheritance(以继承取代委托)

## 12 大型重构

### 12.1 Tease Apart Inheritance (梳理并分解继承体系)

### 12.2 Convert Design to Object (将过程设计转换为对象设计)

### 12.3 Seprate Domain from Presentation (将领域和表述/显示分离)

### 12.4 Extract Hierachy(提炼继承体系)

## 13 重构,复用与实现

## 14 重构工具

## 15 总结
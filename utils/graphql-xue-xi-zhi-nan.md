# Graphql学习指南

# 欢迎来到Graphql的世界

### Graphql设计原则

- 分层: 查询字段层次分明
- 强类型

# Graphql查询语言

### 边和连接

Graphql内置标量类型

- Int
- Float
- String
- Boolean
- ID

### 片段

```
query {
    Lift(id: "jazz-cat") {
        name
        status
        capacity
        night
        trailAccess {
            name
            diffculty
        }
    }
}
```

这是一个正常的查询, 但有很多时候, 我们想把Lift常用信息都提取出来

例如`name` `status` `capacity` `night`这四个字段在很多query里都有, 那么我们应该如何减少重复编写这几个字段呢, 使用`fragment`可以解决我们的问题

上面的query可以改造为

```
fragment liftInfo on Lift {
    name
    status
    capacity
    night    
}

query {
    Lift(id: "jazz-cat") {
        ...liftInfo
        trailAccess {
            name
            diffculty
        }
    }
}
```

### 联合类型

当在一个列表中, 需要返回不同类型的对象, 如何编写query呢

```
query schedule {
    agenda {
        ...on Workout {
            name
            reps
        }
        ...on StudyGroup {
            name
            subject
            students
        }
    }
}
```
返回就是这样的

```
{
    data: {
        agenda: [
            { name: "xxx", "subject": "aaaa", students: 12},
            { name: "aaa": reps: "xxxx"}
        ]
    }
}
```

也可以用片段去查联合类型

```
fragment workout on Workout {
    name
    reps
}

fragment study on StudyGroup {
    name
    subject
    students
}
```

### 接口

当一个字段可能返回多种对象时, 那么就需要使用接口

定义

```
interface Agenda {
    name
}

type Workout implements Agenda{
    name,
    reps
}
```

查询

```
query schedule {
    agenda {
        name
        ...on Workout {
            reps
        }
    }
}
```

### 变更

```
mutation createSong {
    addSong(title: 'aaa', numberOne: true) {
        id
        title
        numberOne
    }
}
```

# 设置schema

### 标量类型

graphql自身只有5个内置标量类型

如何自定义标量类型

```
scalar DateTime

type Photo {
    id: ID!
    created: DateTime!
}
```

[https://github.com/stylesuxx/graphql-custom-types](包含常见的类型)

### 枚举

表明变量只能是指定的几种值

```
enum PhotoCategory {
    SELFIE
    ACTION
    GRAPHIC
}

type Photo {
    id: ID!
    category: PhotoCategory!
}
```

### 连接和列表

|列表声明|定义|
|---|---|
|[Int]|可空的整数值列表|
|[Int!]不可空的整数值列表|
|[Int]!|可空的整数值非空列表|
|[Int!]!|不可空的整数值非空列表|

大多数情况都是不可空的非空列表

> [Int!]和[Int]!的区别

- [Int!]允许[], 但不允许[null]
- [Int]!允许[null], 但不允许[]

### 一对一连接

一张照片只会被一个用户拍出来

```
type User {
    name: String
}

type Photo {
    url: String
    postedBy: User!
}
```

### 一对多连接

```
type User {
    postedPhotos: [Photo!]!
}
```
找出这个用户的所有照片

### 多对多连接

假设我们的人工智能能识别出每张照片的用户是谁

那么一张照片就可能会有多个用户, 而一个用户也会出现在多张图片里

```
type User {
    ...
    inPhotos: [Photo!]
}

type Photo {
    ...
    taggedUsers: [User!]
}
```

### 直通类型

之前一对一, 一对多, 多对多, 一般都是资源与资源之间的关系

但有种情况需要查 关系不是呢是的信息

例如查找人某个用户群里, 这些人认识了多久, 在哪里认识的

```
type Friendship {
    friends: [User!]!
    howLong: Int!
    whereWeMet: Location
}
```

### 不同类型的列表

有时需要在同一个字段描述不同类型的对象

可以用到union 或 interface

> union

一般两个完全没有不同类型的对象, 用这个来做整合

```
union  AgendaItem = StudyGroup | Workout

type StudyGroup {
    name: String!
    subject: String!
}

type Workout {
    name: String!
    reps: Int!
}

type Query {
    agenda: [AgendaItem!]!
}
```

> interface

而一般是从集成自同意父类的话, 建议用这个interface

子类一定要包含接口里的所有字段说明

```
interface  AgendaItem = {
    name: String!
}

type StudyGroup implements AgendaItem{
    name: String!
    subject: String!
}

type Workout implements AgendaItem{
    name: String!
    reps: Int!
}

type Query {
    agenda: [AgendaItem!]!
}
```

### 参数

```
type Query {
    User(id: ID!) User!
}

query {
    User(id: "xxxx") {
        name
        avater
    }
}

```

### 变更

schema
```
type Mutation {
    postPhoto(
        name: String!
        description: String
        category: PhotoCategory=PORTRAIT
    )
}
```

graphql

```
mutation {
    postPhoto(name: "xxxx") {
        id
        url
        posteBy {
            name
        }
    }
}
```

### 输入类型

schema

```
input PostPhotoInput {
    name: String!
    description: String
    category: PhotoCategory=PORTRAIT
}

type Mutation {
    postPhoto(input: PostPhotoInput!): Photo!
}

```

graphql

```
mutation newPhoto($input, PostPhotoInput!) {
    postPhoto(input: $input) {
        id
        url
        posteBy {
            name
        }
    }
}
```

input 还有一个重要功能, 通用化某些查询条件

schema

```
input DateRange {
    start: DateTime!
    end: DateTime!
}

input DataSort {
    sort: SortDirection = DESCENDING
    sortBy: SortAblePhotoField = created
}

type User {
    postedPhotos(paging: DatePage, sorting: DataSort): [Photo!]!
    inPhotos(paging: DatePage, sorting: DataSort): [Photo!]!
}

type Query {
    allUsers(paging: DatePage, sorting: DataSort): [User!]!
    allPhotos(paging: DatePage, sorting: DataSort): [Photo!]!
}
```

### 订阅类型

schema

- newPhoto: 把新照片推送给所有订阅newPhoto的客户端
- newUser: 把有用户创建时, 把user推送给所有订阅newUser事件的端

```
type Subscription {
    newPhoto(category: PhotoCategory): Photo!
    newUser: User!
}

schema {
    query: Query
    mutation: Mutation
    subscription: Subscription
}
```

graphql

```
subscription {
    newPhoto(category: "ACTION") {
        id
        name
        url
    }
}
```

### Schema文档

- 3个双引号表明多行注释
- 2个双引号代表单行注释


```
"""
A user who has been authorized by Github at least once
"""
type User {
    "User name "
    name
}
```

# 创建一个GraphQL api

这个文章比较详细的说 如何用apollo+mongoDB 创建一个用户发布图片的服务

还不错

# Graphql 客户端

如果是js的话, `graphql-request`用来发送graphql请求比较简便些

例如会把参数转换为符合graphql的形式



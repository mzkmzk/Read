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

|列表声明|定义
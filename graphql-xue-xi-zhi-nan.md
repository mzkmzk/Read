# GraphQL学习指南

# 第三章 GraphQL查询语言

用公开GraphQL API查询学习

例如github

- 在线查询 https://developer.github.com/v4/explorer/
- Schema下载链接: https://docs.github.com/en/free-pro-team@latest/graphql/overview/public-schema

# 第四章 设计schema

在设计新API之前, 应该要统筹, 讨论并确定下来类型(type)的集合

schema优先是一种设计方法论

后端团队清楚存储和交付的数据, 前端团队拥有了构建页面时锁需要的结构大纲

前后端无需互相等待, 所有人可以立即开始工作

### 标量类型

GraphQL内置的标量类型(Int, Float, String, Boolean, id)非常有用

我们也可以自定义标量类型, 然后定义规则的保证字段值正确

例如有公共库 https://github.com/stylesuxx/graphql-custom-types

实现了比较常见的标量类型

```
scalar DateTime

type Photo {
    created: DateTime!
}
```

### 枚举

限定一个值的可选类型

```
enum PhotoCategory {
    SELECT 
    PORTRAIT 
    ACTION
    LANDSCAPE
    GRAPHIC
}
```

使用

```
type Photo {
    category: PhotoCategory!
}

### 联合类型

例如计划表中 有锻炼和学习

```
query schedule {
    agenda {
        ... on Workout {
            name
            reps
        }
        ... on Study {
            name
            subject
            
        }
    }
}
```

```
union AgendaItem = Workout | Study

type Study {
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

### 接口

有相同特种的对象类型

实现着必须实现接口的所有属性

```
query schedule {
    agenda {
        name
        start
        end
        ... on Workout {
            reps
        }
    }
}
```

```
scalar DataTime

interface AgendaItem {
    name: String!
    start: DateTime!
    end: DateTime!
}

type Study implements AgendaItem {
    name: String!
    start: DateTime!
    end: DateTime!
    participants: [User!]!
    topic: String!
}

type Workout implements AgendaItem {
    name: String!
    start: DateTime!
    end: DateTime!
    reps: Int!
}

type Query {
    agenda [AgendaItem!]!
}

```

何时用联合, 何时用抽象

联合: 多个无关系的对象, 但有时需要在一个字段表示

接口: 有共同特征的对象, 需要再一个字段表示



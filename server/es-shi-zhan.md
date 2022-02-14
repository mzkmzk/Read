# ElasticSearch 实战

# 2. 深入功能

es中的数据是如何组织起来的

逻辑设计: 搜索应用所要注意的

- 用于索引和搜索的基本单位是文档, 可以将其理解为数据库的一行
- 文档以类型来分组, 类型包含若干文档, 类似表格包含若干行
- 一个或多个类型存在同一索引里, 类似SQL的数据库

物理设计: Es是如何处理数据的

- ES将每个索引划分片, 每份分片可以在集群中的不同服务间迁移

## 2.1 理解逻辑设计: 文档、类型和索引

### 2.1.1 文档

文档的重要属性

- 文档是自我包含的. 一篇文档同时包含字段(如name), 和它的取值(如es denver)
- 文档是层次型的. 文档中还可以包含文档
- 文档拥有灵活的结构. 文档并不依赖预先定义的模式, 例如并非所有的活动都需要`描述`这个字段值, 所以可以彻底忽略该字段

### 2.1.2 类型

类型是文档的逻辑容器, 类似于表格是行的容器

在不同类型中, 最好放入不同的结构的文档

例如可以一个类型定义聚会时的分组, 一个类型定义人们参加的活动

每个类型中字段的定义称为映射, 例如name的字段称为string

而location中的geolocation字段映射是geo_point

每个映射的搜索处理方式不一样

例如string映射可以搜索关键字, 而geo_point映射可以搜索哪个分组离你更近

如果一篇新旧索引的文档拥有一个映射尚不存在的字段, es会自动将新字段加入映射

es会对字段进行猜测映射, 例如值是7 就会猜测是长整型

假如索引了值7之后, 可能再想索引`hello world`, 索引就会失败

对于线上的环境, 最安全的方式是在索引数据之前, 就定义好好所需的映射

### 2.1.3 索引

索引是映射类型的容器, 一个es索引非常像关系型世界的数据库, 是独立的大量文档集合

每个索引存储在磁盘的同组文件中

所以存储了所有映射类型的字段, 还有一些设置

例如`refresh_interval`设置, 定义新近索引的文档对于搜索可见的时机间隔

`刷新`操作比较昂贵, 默认是每秒更新一次, 而不是每来一篇新的文档更新一次

可以设置某个索引的分片的数量, 索引是由一个/多个分片的数据块组成

## 2.2 理解物理设置: 节点和分片

一个索引创建时, 默认每个索引由5个主要分片组成, 而每个主要分片又有一个副本, 一共10份分片

一个分片是一个目录中的文件, 分片也是es将数据从一个节点移到另一个节点的最小单位

默认情况下, 可以连接集群中的任一节点并访问完整的数据集, 就好像集群只有单独的一个节点

> 当索引一篇文档时发生了什么

- 系统根据文档id的散列值选择一个主分片, 并把文档发送到该主分片
- 这份主分片可能存在位于另一个节点
- 然后文档被发送到该主分片的所有副本分片进行索引
- 在主分片无法访问时, 副分片自动升级为主分片

> 搜索索引时发生了什么

es需要在该索引的完整分片集合中进行查找, 这些分片可以是主分片/副分片

### 2.2.2 理解主分片和副本分片

es所处理的最小单元: 分片

一个分片是Lucene的索引

一个包含倒排索引的文件目录

倒排索引的结构使得es不扫描所有的文档情况下, 就能告知你哪些文档包含特定的词条

副本分片可以在运行时进行添加和删除, 而主分片不行

在创建索引之前, 必须决定分片的数量

过少的分片限制可扩展性, 过多的分片会影响性能, 默认为5是个不错的开始

## 2.3 索引新数据

手动索引第一篇文档, 使用`put`请求

`http://location:9200/get-together/group/1`

- get-together为索引名称
- group为类型的名称
- 1位文档id

### 2.3.1 通过cURL索引一篇文档

```bash
curl -XPUT -u elastic:6ZghfYT294FE  -H "Content-Type: application/json"  'http://es-cn-4590vgbn4000757o6.elasticsearch.aliyuncs.com:9200/mzk-es-action/group/1?pretty' -d '{
    "name": "es denver",
    "organizer": "lee"
}'
```

加pretty的原因是为了得到格式化的返回

返回值

```
{
  "_index" : "mzk-es-action",
  "_type" : "group",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```

### 2.3.2 创建索引和映射类型

上一节的命令为何会成功?

- 索引之前不存在: 并未发送任何命令来创建一个叫做`mzk-es-action`的索引
- 映射之前未定义: 每一定义任何称为group的映射类型来刻画文档中的字段

> 手动创建索引

```bash
curl -XPUT -u elastic:6ZghfYT294FE  -H "Content-Type: application/json"  'http://es-cn-4590vgbn4000757o6.elasticsearch.aliyuncs.com:9200/mzk-es-action2'
{"acknowledged":true,"shards_acknowledged":true,"index":"mzk-es-action2"}%
```

> 获取映射

```bash
curl -XGET -u elastic:6ZghfYT294FE  -H "Content-Type: application/json"  'http://es-cn-4590vgbn4000757o6.elasticsearch.aliyuncs.com:9200/mzk-es-action/_mapping/group?pretty'

{
  "mzk-es-action" : {
    "mappings" : {
      "group" : {
        "properties" : {
          "name" : {
            "type" : "text",
            "fields" : {
              "keyword" : {
                "type" : "keyword",
                "ignore_above" : 256
              }
            }
          },
          "organizer" : {
            "type" : "text",
            "fields" : {
              "keyword" : {
                "type" : "keyword",
                "ignore_above" : 256
              }
            }
          }
        }
      }
    }
  }
}

```

## 2.4 搜索并获取数据

```bash
curl "localhost:9200/get-together/group/_search?\
q=elasticsearch\
&_source=name,location_group\
&size=1\
&pretty"

{
  "took" : 6,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 10,
      "relation" : "eq"
    },
    "max_score" : 1.0238004,
    "hits" : [
      {
        "_index" : "get-together",
        "_type" : "_doc",
        "_id" : "2",
        "_score" : 1.0238004,
        "_source" : {
          "location_group" : "Denver, Colorado, USA",
          "name" : "Elasticsearch Denver"
        }
      }
    ]
  }
}
```

- q=elasticsaerch: 查找包含`elasticsearch`的文档
- fields=name,location, 只需要name和location的信息
- size=1: 只需要排名靠前的
- pretty: 格式化json

要想搜索所有, 可以使用`_all`

搜索的3个内容

- 在哪里搜索
- 回复什么内容
- 搜索什么以及如何搜索

### 2.4.1 在哪里搜索

可以在同一索引的多个字段进行搜索, 也可以在多个索引中搜索

多个类型中搜索, 使用逗号分隔

```bash
curl "localhost:9200/get-together/_doc/_search\
?q=elasticsearch&pretty"
```

在所有索引中的某个类型进行搜索

```bash
curl "localhost:9200/_all/event/_search"
```

日志事件经常以基于事件的索引来组织, 例如`logs-2021-03-14`, 这种设计意味着当天的搜索很热门

### 回复的内容

> 时间

```bash
{
  "took" : 6,
  "timed_out" : false,
}
```

took表示查询所花费的时间, 默认情况下time_out永远为false

除非在请求时待timeout参数, 

```bash
curl "localhost:9200/get-together/_doc/_search\
?q=elasticsearch\
&timeout=3s\
&pretty"
```

这样只会返回3S内查询到的数据, 并且超过3S则time_out为true

> 分片

```bash
{
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
}
```

> 命中统计数据

```json
{
    "total" : {
        "value" : 10, // 总命中数
        "relation" : "eq"
    },
    "max_score" : 1.0238004, // 匹配最高得分
}
```

> 结果文档

```json
{
     "hits" : [
      {
        "_index" : "get-together",
        "_type" : "_doc",
        "_id" : "2",
        "_score" : 1.0238004,
        "_source" : {
          "location_group" : "Denver, Colorado, USA",
          "name" : "Elasticsearch Denver"
        }
      }
    ]
}
```

### 2.4.3 如何搜索

> 设置查询的字符串选项

```bash
curl 'localhost:9200/get-together/_search?pretty'  -H "Content-Type: application/json" -d '{
  "query": {
      "query_string": {
          "query": "elasticsearch san francisco",
          "default_field": "name",
          "default_operator": "AND"
      }
  }
}'
```

获取同样结果的另一种写法:`"query": "name:elasticsearch AND name:san AND name:francisco"`

es默认人`_all`字段里查询, 如果想在分组的名称里查询这样指定`"default_field": "name"`

> 使用过滤器

如果对得分结果不感兴趣, 只关心是否有一条结果匹配条件

```bash
curl 'localhost:9200/get-together/_search?pretty' -H'Content-Type: application/json' -d '{
  "query": {
      "bool": {
          "filter": {
              "term": {
                  "name": "elasticsearch"
              }
          }
      }
  }
}'

```

返回的结果跟查询相同, 但没有根据得分来排序

> 应用聚集

查询统计数

```bash
curl 'localhost:9200/get-together/_search?pretty' -H'Content-Type: application/json' -d '{
  "aggregations": {
      "organizers": {
          "terms": { "field": "organizer" }
      }
  }
}'
```

返回异常

```bash
{
  "error" : {
    "root_cause" : [
      {
        "type" : "illegal_argument_exception",
        "reason" : "Text fields are not optimised for operations that require per-document field data like aggregations and sorting, so these operations are disabled by default. Please use a keyword field instead. Alternatively, set fielddata=true on [organizer] in order to load field data by uninverting the inverted index. Note that this can use significant memory."
      }
    ],
    "type" : "search_phase_execution_exception",
    "reason" : "all shards failed",
    ...
  "status" : 400
}
```

需要给该字段增加属性

```bash
curl -X PUT "localhost:9200/get-together/_mapping/_doc?pretty" -H 'Content-Type: application/json' -d'
{
  "properties": {
    "organizer": {
      "type": "text",
      "fielddata": true
    }
  }
}
'

{
  "acknowledged" : true
}
```

然后再次进行查询可得

```bash
{
  "took" : 227,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 20,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "get-together",
        "_type" : "_doc",
        "_id" : "1",
        "_score" : 1.0,
        "_source" : {
          "relationship_type" : "group",
          "name" : "Denver Clojure",
          "organizer" : [
            "Daniel",
            "Lee"
          ],
          "description" : "Group of Clojure enthusiasts from Denver who want to hack on code together and learn more about Clojure",
          "created_on" : "2012-06-15",
          "tags" : [
            "clojure",
            "denver",
            "functional programming",
            "jvm",
            "java"
          ],
          "members" : [
            "Lee",
            "Daniel",
            "Mike"
          ],
          "location_group" : "Denver, Colorado, USA"
        }
      }
      ...
    ]
  },
  "aggregations" : {
    "organizers" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 0,
      "buckets" : [
        {
          "key" : "lee",
          "doc_count" : 2
        },
        {
          "key" : "andy",
          "doc_count" : 1
        },
        {
          "key" : "daniel",
          "doc_count" : 1
        },
        {
          "key" : "mik",
          "doc_count" : 1
        },
        {
          "key" : "tyler",
          "doc_count" : 1
        }
      ]
    }
  }
}
```

## 2.6 在集群中加入节点

查询分片信息

```bash
 curl  "localhost:9200/_cat/shards?v"
index                  shard prirep state      docs  store ip         node
get-together           1     p      STARTED       4 11.6kb 172.17.0.2 163bef384184
get-together           1     r      UNASSIGNED
get-together           0     p      STARTED      16 20.2kb 172.17.0.2 163bef384184
get-together           0     r      UNASSIGNED
myindex                0     p      STARTED       0   208b 172.17.0.2 163bef384184
myindex                0     r      UNASSIGNED
december_2014_invoices 0     p      STARTED       0   208b 172.17.0.2 163bef384184
december_2014_invoices 0     r      UNASSIGNED
.geoip_databases       0     p      STARTED      42 40.6mb 172.17.0.2 163bef384184
november_2014_invoices 0     p      STARTED       0   208b 172.17.0.2 163bef384184
november_2014_invoices 0     r      UNASSIGNED
```

# 3. 索引、更新和删除数据

本章重点介绍下面3种类型的字段

- 核心: 这些字段暴扣字符串和数值型
- 数组和多元字段: 这些字段在某个字段里存储相同核心类型的多个值
- 预定义: 自卸字段包括_ttl和_timestamp

可以使用_ttl字段让过期的文档自动被删除

## 3.1 使用映射来定义各种文档

### 3.1.1 检索和定义映射

> 获取目前映射

```bash
{
  "get-together" : {
    "mappings" : {
      "properties" : {
        "attendees" : {
          "type" : "text",
          "fields" : {
            "verbatim" : {
              "type" : "keyword"
            }
          }
        },
        "created_on" : {
          "type" : "date",
          "format" : "yyyy-MM-dd"
        },
        "date" : {
          "type" : "date",
          "format" : "date_hour_minute"
        },
        "description" : {
          "type" : "text",
          "term_vector" : "with_positions_offsets"
        },
        "host" : {
          "type" : "text"
        },
        "location_event" : {
          "properties" : {
            "geolocation" : {
              "type" : "geo_point"
            },
            "name" : {
              "type" : "text"
            }
          }
        },
        "location_group" : {
          "type" : "text"
        },
        "members" : {
          "type" : "text"
        },
        "name" : {
          "type" : "text"
        },
        "organizer" : {
          "type" : "text",
          "fielddata" : true
        },
        "relationship_type" : {
          "type" : "join",
          "eager_global_ordinals" : true,
          "relations" : {
            "group" : "event"
          }
        },
        "reviews" : {
          "type" : "integer",
          "null_value" : 0
        },
        "tags" : {
          "type" : "text",
          "fields" : {
            "verbatim" : {
              "type" : "keyword"
            }
          }
        },
        "title" : {
          "type" : "text"
        }
      }
    }
  }
}
```

索引一篇新的文档

```bash
curl -X PUT "localhost:9200/get-together/_doc/1" -H"Content-Type: application/json"  -d'
{
    "name": "Late Night with Elasticsearch",
    "date": "2013-10-25T19:00"
}
'

{"_index":"get-together","_type":"_doc","_id":"1","_version":2,"result":"updated","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":16,"_primary_term":1
```

> 定义新的映射

```bash
curl -X PUT "localhost:9200/get-together/_mapping/_doc" -H"Content-Type: application/json"  -d'
{
    "_doc": {
        "properties": {
            "host": {
                "type": "text"
            }
        }
    }
}
'
{"acknowledged":true}
```

### 3.1.2 扩展现有的映射

无法改变现有字段的数据类型

也通常无法改变一个字段被索引的方式

唯一解决这个问题的办法是

- 移除索引里的数据
- 设置新的映射
- 再次索引所有的数据

## 3.2 用于定义文档字段的核心类型

|核心类型|取值示例|
|---|---|
|字符串|'lee'|
|数值|12, 3.2|
|日期|2021-11-11T10:02:26.231+01:00|
|布尔|true或false|

### 3.2.1 字符串类型

假如在索引字符串`late night with elasticsearch`

那么会生成4个词条`late`, `night`, `with`, `elasticsearch`

可以指定字段的索引方式

- true: 被索引
- false: 不被索引

```bash
curl -X PUT "localhost:9200/get-together/_mapping/_doc" -H"Content-Type: application/json"  -d'
{
    "_doc": {
        "properties": {
            "host": {
                "type": "text",
                "index": true
            }
        }
    }
}
'
```

### 3.2.2 数值类型

es自动检测映射更为安全, 为整数值分配long, 为浮点数值分配double

### 3.2.3 日期类型

```bash
curl "localhost:9200/get-together/_mapping/_doc?pretty"
{
  "get-together" : {
    "mappings" : {
      "_doc" : {
        "properties" : {
          "created_on" : {
            "type" : "date",
            "format" : "yyyy-MM-dd"
          }
        }
      }
    }
  }
}
```

## 3.3 数组和多字段

### 3.3.1 数组

```bash
curl -X PUT "localhost:9200/get-together/_doc/22" -H"Content-Type: application/json"  -d'
{
    "tags_22": ["first", "initial"]
}
'

{"_index":"get-together","_type":"_doc","_id":"22","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":4,"_primary_term":1
```

查看到的映射是

```json
{
    "tags_22" : {
        "type" : "text",
        "fields" : {
            "keyword" : {
                "type" : "keyword",
                "ignore_above" : 256
            }
        }
    }
}
```

### 3.4.1 控制如何存储和搜索文档

> 存储原有内容的_source

```bash
curl "localhost:9200/get-together/_doc/1?pretty"
{
  "_index" : "get-together",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 2,
  "_seq_no" : 16,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "name" : "Late Night with Elasticsearch",
    "date" : "2013-10-25T19:00"
  }
}
```

> 仅返回源文档的某些字段

```bash
curl "localhost:9200/get-together/_doc/1?pretty&_source=name"
{
  "_index" : "get-together",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 2,
  "_seq_no" : 16,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "name" : "Late Night with Elasticsearch"
  }
}

```

### 3.4.2识别文档

> 为文档提供ID

不指定id时, es将自动生成id

```bash
curl -X POST "localhost:9200/get-together/_doc" -H"Content-Type: application/json"  -d'
{
    "name": "xxxxxx"
}
'

{"_index":"get-together","_type":"_doc","_id":"PPqkT3sBnVKKHVdH6BxL","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":17,"_primary_term":1}%
```

## 3.5 更新现有文档

### 3.5.1 使用更新api

> 发送部分文档

```bash
curl -X POST "localhost:9200/get-together/_doc/22/_update" -H"Content-Type: application/json"  -d'
{
    "doc": {
            "name": "yyyyy",
    }
}
'
```

> 使用upsert来创建尚不存在的文档

为了处理文档不存在的使用可以使用upsert


```bash
curl -X POST "localhost:9200/get-together/_doc/33/_update?pretty" -H"Content-Type: application/json"  -d'
{
    "doc": {
            "name": "xxxx"
    },
    "upsert": {
        "name": "yyy",
        "organizer": "bbb"
    }
}
'
{
  "_index" : "get-together",
  "_type" : "_doc",
  "_id" : "33",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 18,
  "_primary_term" : 1
}
```

查询添加的文档:

```bash
curl "localhost:9200/get-together/_doc/33?pretty"
{
  "_index" : "get-together",
  "_type" : "_doc",
  "_id" : "33",
  "_version" : 1,
  "_seq_no" : 18,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "name" : "yyy",
    "organizer" : "zxc"
  }
}
```

> 通过脚本更新文档

默认的脚本语言是Groovy, 语法和java类似

```bash
curl -X POST "localhost:9200/get-together/_doc/33/_update?pretty" -H"Content-Type: application/json"  -d'
{
    "script": {
        "source": "ctx._source.price += params.price_diff",
        "params": {
                "price_diff": 20
        }
    }
}
'

{
  "_index" : "get-together",
  "_type" : "_doc",
  "_id" : "33",
  "_version" : 4,
  "result" : "updated",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 21,
  "_primary_term" : 1
}
```

### 3.5.2 通过版本来实现并发控制

如果同一时刻多次更新都在执行, 可能会出现并发问题

es支持并发控制, 为每篇文档设置了一个版本号, 在最初的文档版本是1

当更新操作重新索引它时, 版本号会设置为2

当一个文档先被更新为版本2, 与此同时, 一个更新版本也设置为2, 则更新失败

这种并发控制称为乐观锁

因为它允许并行的操作并假设冲突是很少出现的, 出现的时候就抛出错误

> 冲突发生时自动重试更新操作, 通过`retry_on_conflict`参数, 让es自动重试

```bash
curl -X POST "localhost:9200/get-together/_doc/33/_update?pretty&retry_on_conflict=3" -H"Content-Type: application/json"  -d'
{
    "script": {
        "source": "ctx._source.price = 3"
    }
}
'
```

> 索引文档的时候使用版本号

更新文档的另一个方法是不使用更新api, 而是在同一索引, 类型和id之处索引一个新的文档

如果认为的版本已经是8了,1 一个重新索引的请求应该是这样的

```bash
curl -XPUT "localhost:9200/get-together/_doc/33?version=8&pretty" -H"Content-Type: application/json"  -d'
{

    "price": 10

}
'
```

如果版本号对不上则会报错`"[_doc][33]: version conflict, current version [8] is different than the one provided [3]`

> 使用外部版本号

es一般版本号是每次更改后递增, 也可以指定version

```bash
curl -XPUT "localhost:9200/get-together/_doc/33?version=8&version_type=external&pretty" -H"Content-Type: application/json"  -d'
{

    "price": 10

}
'
```

这将使es接受任何版本号, 只要比现在高, 而且es不会自己增加版本号

## 3.6 删除数据

### 3.6.1 删除文档

> 删除单个文档

```bash
 curl -X DELETE "localhost:9200/get-together/_doc/33?pretty" -H"Content-Type: application/json"

{
  "_index" : "get-together",
  "_type" : "_doc",
  "_id" : "33",
  "_version" : 10,
  "result" : "deleted",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 27,
  "_primary_term" : 1
}
```

可能会出现删除了文档, 但是由于更新操作重新创建了该文档

为了防止这个问题, es将在一段时间内保留这篇文档的版本, 如此它就能拒绝比删除操作更低的更新操作了, 默认情况是60s, 通过修改index.gc_deletes修改它

> 删除映射类型和删除查询匹配的文档

```bash
 curl -X DELETE "localhost:9200/get-together/_doc" -H"Content-Type: application/json"
```

或根据查询结果删除

```bash
 curl -X DELETE "localhost:9200/get-together/_query?1=es" -H"Content-Type: application/json"
```

### 3.6.2 删除单个索引

```bash
curl -X DELETE 'localhost:9200/get-together/'
```

可以设置action.destructive_requires_name:true, 来防止删除_all索引

### 3.6.3  关闭索引


```bash
curl -X POST "localhost:9200/get-together/_close" -H"Content-Type: application/json"

curl -X POST "localhost:9200/get-together/_open" -H"Content-Type: application/json"

```

# 4. 搜索数据

## 4.1 搜索请求的结构

### 4.1.1 确定搜索范围

```bash
curl "localhost:9200/_search" // 搜索整个集群
curl "localhost:9200/get-together/_search" //搜索get-together索引
curl "localhost:9200/get-together/event/_search" //搜索get-together索引的event类型
curl "localhost:9200/_all/event" //所有所有索引的event类型
curl "localhost:9200/get-together,other/event,group/_search"
curl "localhost:9200/+get-toge*, -get-together/_search" //搜索所有get-toge开头的索引, 但不包括get-together
```

还可以用别名来搜索多个索引, 例如logstash-yymmdd格式命名的索引, 一个logstash别名就可以指向所有相关索引

### 4.1.2 搜索请求的基本模块

- query: 查询DSL和过滤器DSL
- size: 返回文档的数量
- from: 从第XX条开始查
- _source: 文档的存储值
- sort: 默认按得分排序

> 基于URL的搜索请求

按时间倒序返回

```bash
curl "localhost:9200/get-together/_search?sort=date:asc"
```

### 4.1.3 基于请求主体的搜索请求

> 过滤字段

```bash
curl "localhost:9200/get-together/_search?pretty" -H"Content-Type: application/json"  -d'
{
    "query": {
        "match_all": {}
    },
    "_source": {
        "include": ["location.*", "date"],
        "exclude": ["location.geolocation"]
    }
}
'
```

> 结果排序

```bash
curl "localhost:9200/get-together/_search?pretty" -H"Content-Type: application/json"  -d'
{
    "query": {
        "match_all": {}
    },
    "sort": [
        {"created_on": "asc" },
        {"name": "desc"},
        "_score"
    ]
}
'
```

## 4.2 介绍查询和过滤器DSL

### 4.2.1 match查询和term过滤器

> match查询

```bash
curl  http://localhost:9200/get-together/_search  -H"Content-Type: application/json" -d '{
  "query": {
      "match": {
          "title": "hadoop"
      }
  }
}'
```

match查询的是给特定的词条打分, 而过滤器只是为`文档是否匹配这个查询` 返回简单的是或不是

> 使用过滤器查询

```bash
curl  http://localhost:9200/get-together/_search  -H"Content-Type: application/json" -d '{
  "query": {
      "bool": {
          "must": {
              "match": {
                "title": "hadoop"
            }
          },
          "filter": {
            "term": {
                "host": "andy"
            }
          }
      }
  }
}'
```

### 4.2.2 常用的基础查询和过滤器

> query_string查询

默认情况下, query_string会查询_all字段

可以通过设置`default_field`来设置特定请求字段

```bash
curl  http://localhost:9200/get-together/_search  -H"Content-Type: application/json" -d '{
  "query": {
      "query_string": {
          "default_field": "description",
          "query": "nosql"
      }
  }
}'
```

query_string的更多用法:

- 查询所有nosql的分组, 但排除mongodb: `name:nosql AND -description:mongodb`

查询1999年到2001年期间创建的搜索和Lucene分组

`{tag: search OR tag:lucene} AND created_on:[1991-01-01 TO 2001-01-01]`

> term查询和term过滤器

term为词条

词条查询
```bash
curl  http://localhost:9200/get-together/_doc/_search?pretty  -H"Content-Type: application/json" -d '{
  "query": {
      "term": {
          "tags": "elasticsearch"
      }
  },
  "_source": ["name", "tags"]
}'

```

词条过滤器

```bash
curl 'localhost:9200/get-together/_search?pretty' -H'Content-Type: application/json' -d '{
  "query": {
      "bool": {
          "filter": {
              "term": {
                  "name": "elasticsearch"
              }
          }
      }
  },
   "_source": ["name", "tags"]
}'
```

> terms查询

```bash
curl  http://localhost:9200/get-together/_doc/_search?pretty  -H"Content-Type: application/json" -d '{
  "query": {
      "terms": {
          "tags": ["jvm", "hadoop"]
      }
  },
  "_source": ["name", "tags"]
}'
```

限制每篇文档中匹配词条的最小数量

```bash
curl  http://localhost:9200/get-together/_doc/_search?pretty  -H"Content-Type: application/json" -d '{
  "query": {
      "bool": {
          "minimum_should_match": 2,
          "should": [
              { "term": { "tags": "hadoop" } },
              { "term": { "tags": "data" } }
          ]
      }
  },
  "_source": ["name", "tags"]
}'

```

### 4.2.3 match查询和term过滤器

和term查询类似, match查询是一个散列映射, 包含希望搜索的字段和字符串

> 布尔查询行为

默认情况下, match查询使用布尔行为是OR操作符

```bash
curl 'localhost:9200/get-together/_search?pretty' -H'Content-Type: application/json' -d '{
  "query": {
     "match": {
         "name": {
             "query": "Elasticsearch Denver",
             "operator": "and"
         }
     }
  },
   "_source": ["name", "tags"]
}'

```

> 词组查询行为

在文档中查询特定的值, phrase查询非常有用

例如只记得`enterprise`和`london`两个词, 中间有多少个词分离不太记得了, 

就可以设置slop为1或2, 而不是默认的

```bash
 curl 'localhost:9200/get-together/_search?pretty' -H'Content-Type: application/json' -d '{
  "query": {
     "match_phrase": {
         "name": {
             "query": "enterprise london",
             "slop": "1"
         }
     }
  },
   "_source": ["name", "tags"]
}'
{
  "took" : 313,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 1.5762693,
    "hits" : [
      {
        "_index" : "get-together",
        "_type" : "_doc",
        "_id" : "5",
        "_score" : 1.5762693,
        "_source" : {
          "name" : "Enterprise search London get-together",
          "tags" : [
            "enterprise search",
            "apache lucene",
            "solr",
            "open source",
            "text analytics"
          ]
        }
      }
    ]
  }
}

```

### 4.2.4 phrase_prefix查询

和词组最后一个词条进行前缀匹配, 可以通过max_expansions来设置最大的前缀扩展数量

```bash
 curl 'localhost:9200/get-together/_search?pretty' -H'Content-Type: application/json' -d '{
  "query": {
     "match_phrase_prefix": {
         "name": {
             "query": "elasticsearch den",
             "max_expansions": 1
         }
     }
  },
   "_source": ["name", "tags"]
}'
{
  "took" : 59,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 2.0598295,
    "hits" : [
      {
        "_index" : "get-together",
        "_type" : "_doc",
        "_id" : "2",
        "_score" : 2.0598295,
        "_source" : {
          "name" : "Elasticsearch Denver",
          "tags" : [
            "denver",
            "elasticsearch",
            "big data",
            "lucene",
            "solr"
          ]
        }
      }
    ]
  }
}
```

使用multi_match来匹配多个字段

```bash
curl 'localhost:9200/get-together/_search?pretty' -H'Content-Type: application/json' -d '{
  "query": {
     "multi_match": {
        "query": "elasticsearch hadoop",
        "fields": ["name", "description"]
     }
  }
}'
```

## 4.3 组合查询和复合查询

### 4.3.1 bool查询

bool查询可以组合任意数量的查询, 指定哪些部分是必须的`must`, 应该`should`, 不能`must_not`

- must匹配: 只有匹配上查询的才会返回
- should: 只有匹配上指定数量的子句文档才会返回
- 如果没有指定must匹配, 文档至少要匹配一个should子句才返回

```bash
curl 'localhost:9200/get-together/_search?pretty' -H'Content-Type: application/json' -d '{
  "query": {
     "bool": {
         "must": [
             { "term": { "attendees": "david"} }
         ],
         "should": [
             { "term": { "attendees": "clint" } },
             { "term": { "attendees": "andy" } }
         ],
         "must_not": [
             {
                 "range": {
                    "date": {
                        "lt": "2013-06-30T00:00"
                    }
                }
             }
         ],
         "minimum_should_match": 1
     }
  }
}'
{
  "took" : 131,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 2.5546992,
    "hits" : [
      {
        "_index" : "get-together",
        "_type" : "_doc",
        "_id" : "110",
        "_score" : 2.5546992,
        "_routing" : "4",
        "_source" : {
          "relationship_type" : {
            "name" : "event",
            "parent" : "4"
          },
          "host" : "Andy",
          "title" : "Big Data and the cloud at Microsoft",
          "description" : "Discussion about the Microsoft Azure cloud and HDInsight.",
          "attendees" : [
            "Andy",
            "Michael",
            "Ben",
            "David"
          ],
          "date" : "2013-07-31T18:00",
          "location_event" : {
            "name" : "Bing Boulder office",
            "geolocation" : "40.018528,-105.275806"
          },
          "reviews" : 1
        }
      }
    ]
  }
}
```

### 4.3.2 bool过滤器

过滤器和查询版本基本一致, 但在过滤器中,不支持`minimum_should_match`属性, 默认为1

## 4.4 超越match和过滤器查询

### 4.4.1 range查询器

```bash
curl 'localhost:9200/get-together/_search?pretty' -H'Content-Type: application/json' -d '{
  "query": {
     "range": {
         "created_on": {
             "gt": "2012-06-01",
             "lte": "2012-09-01"
         }
     }
  }
}'
{
  "took" : 6,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "get-together",
        "_type" : "_doc",
        "_id" : "3",
        "_score" : 1.0,
        "_source" : {
          "relationship_type" : "group",
          "name" : "Elasticsearch San Francisco",
          "organizer" : "Mik",
          "description" : "Elasticsearch group for ES users of all knowledge levels",
          "created_on" : "2012-08-07",
          "tags" : [
            "elasticsearch",
            "big data",
            "lucene",
            "open source"
          ],
          "members" : [
            "Lee",
            "Igor"
          ],
          "location_group" : "San Francisco, California, USA"
        }
      }
    ]
  }
}
```

### 4.4.2 prefix查询和过滤器

```bash
curl 'localhost:9200/get-together/_search?pretty' -H'Content-Type: application/json' -d '{
  "query": {
     "prefix": {
         "title": "liber"
     }
  },
  "_source": ["title"]
}'
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "get-together",
        "_type" : "_doc",
        "_id" : "100",
        "_score" : 1.0,
        "_routing" : "1",
        "_source" : {
          "title" : "Liberator and Immutant"
        }
      }
    ]
  }
}
```

### 4.4.3 wildcard查询

类似于shell里的正则`ls *foo?ar`

```bash
curl 'localhost:9200/get-together/_search?pretty' -H'Content-Type: application/json' -d '{
  "query": {
     "wildcard": {
         "title": "ba*n"
     }
  },
  "_source": ["title"]
}'
```

## 4.5 使用过滤器查询字段的存在性

### 4.5.1 exists过滤器

过滤文档 是否拥有哪些字段

```bash
curl 'localhost:9200/get-together/_search?pretty' -H'Content-Type: application/json' -d '{
  "query": {
     "bool": {
         "filter": {
             "exists": { "field": "location_event.geolocation" }
         }
     }
  }
}'
```

# 5. 分析数据

## 5.1 分析数据

分析是在文档被发送并加入倒排搜索之前, 



# 架构师修炼之道

# 总结

书中主要讲了在系统价格设计上, 针对设计、流程、决策等方面作了讲述. 推荐评分3.5分

# 2. 切分与扩展

## 2.1 切分

### 2.1.1 数据维度切分

- 3. 实例--数据库分库分表

垂直切分:

1. 将不常用的字段放到一张表慧总
2. 将blob等占用较多的字段拆分放到一张表慧总
3. 将经常一起被访问的列放到一个表中


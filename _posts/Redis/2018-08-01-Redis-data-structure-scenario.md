---
layout: post
title: Redis 常用数据类型的实例及其应用场景
categories: Redis
description: Redis 各数据类型实例及其应用场景
keywords: Redis, String, List, Hash, Set, Sorted Set
---

### String——储存简单的数据

> String 类型是 Redis 中最基本的数据类型，其在 Redis 中是二进制安全，意味着这种数据类型可以接受任何格式的二进制数据，例如一张 JPED 格式的图片或者 JSON 格式的字符串。Redis 中字符串类型的 Value 最多可以容纳的数据长度是 512M。

#### String 的实例

```
127.0.0.1:6379> set name qinbin  --- 设置 key=value(name=qinbin)
OK
127.0.0.1:6379> get name  --- 获取键 key 对应的值
"qinbin"
127.0.0.1:6379> set number 0  --- 设置整型的值
OK
127.0.0.1:6379> incr number  --- 递增键的整数值
(integer) 1
127.0.0.1:6379> incr number  --- 递增键的整数值
(integer) 2
127.0.0.1:6379> incr number  --- 递增键的整数值
(integer) 3
127.0.0.1:6379> decrby number 1  --- 由给定数目递减键的整数值
(integer) 2
127.0.0.1:6379> rename number new-number  --- 更改键的名称
OK
127.0.0.1:6379> get number  --- 获取更改前键的名称
(nil)
127.0.0.1:6379> get new-number  --- 获取更改后键的名称
"2"
127.0.0.1:6379> type new-number  --- 返回存储在键的数据类型的值
string
127.0.0.1:6379> type name  --- 返回存储在键的数据类型的值
string
127.0.0.1:6379> del name  --- 如果存在删除键
(integer) 1
```

#### String 的应用场景
由于 string 类型灵活，可以储存大量的数据，所以经常用来缓存数据，例如 App 中常见的商品分类栏 JSON，这类的特点是：访问频率高，数据不会经常改变。


### Hash——储存对象的数据

> Hash 类型很接近数据库的模型，key 是唯一值，value 是一个 hashmap 结构


#### Hash 的实例


```
127.0.0.1:6379> hset person name qinbin  --- 设置对象指定字段的值，person 为键、name 为字段、qinbin 为值
(integer) 1
127.0.0.1:6379> hset person age 23  --- 设置对象指定字段的值
(integer) 1
127.0.0.1:6379> hset person sex famale  --- 设置对象指定字段的值
(integer) 1
127.0.0.1:6379> hgetall person  --- 获取对象的所有属性域和值
1) "name"
2) "qinbin"
3) "age"
4) "23"
5) "sex"
6) "famale"
127.0.0.1:6379> hkeys person  --- 获取对象的所有属性字段
1) "name"
2) "age"
3) "sex"
127.0.0.1:6379> hvals person  --- 获取对象的所有属性值
1) "qinbin"
2) "23"
3) "famale"
127.0.0.1:6379> hlen person  --- 获取对象的所有属性字段的总数
(integer) 3
127.0.0.1:6379> hmget person name age  --- 获取对象的一个或多个指定字段的值
1) "qinbin"
2) "23"
```


#### Hash 的应用场景
常见的功能是根据 id 获取用户信息，例如根据 id 获取用户的昵称、头像等；（为什么不用 string，因为 string 中通常储存的是整个 json 数据，如果需要得到或者修改用户的昵称，需要查询整个 value 后还要把 json<-> 对象相互转换，增加了没必要的开销和复杂性）


### List——模拟队列操作

> Redis 中的 list 是按照插入的顺序排序的字符串链表，可以作为简单的消息队列使用

#### List 的实例

```
127.0.0.1:6379> lpush list1 redis  --- 在前面 (最左边) 加上一个或多个值的列表
(integer) 1
127.0.0.1:6379> lpush list1 hello  --- 在前面 (最左边) 加上一个或多个值的列表
(integer) 2
127.0.0.1:6379> rpush list1 world  --- 在后面 (最右边) 添加一个或多个值到列表
(integer) 3
127.0.0.1:6379> llen list  --- 获取列表的长度
(integer) 0
127.0.0.1:6379> llen list1  --- 获取列表的长度
(integer) 3
127.0.0.1:6379> lrange list1 0 3  --- 从一个列表获取各种元素
1) "hello"
2) "redis"
3) "world"
127.0.0.1:6379> lpop list1  --- 获取并取出列表中的第一个元素
"hello"
127.0.0.1:6379> rpop list1  --- 获取并取出列表中的最后一个元素
"world"
127.0.0.1:6379> lrange list1 0 3 --- 从一个列表获取各种元素
1) "redis"
```

#### List 的应用场景

发送短信之类的可以异步完成的操作，即可放入消息队列中，异步 + 错峰

### Set——无序且不重复的元素集合

> Redis 中 set 类型可以看作是没有排序、不重复的集合；另外 set 之间支持集合运算，交集、差集、并集等，这些操作在 Redis 内部完成，效率非常高

#### Set 的实例

```
127.0.0.1:6379> sadd myset Hello  --- 添加一个或者多个元素到集合 (set) 里
(integer) 1
127.0.0.1:6379> sadd myset World  --- 添加一个或者多个元素到集合 (set) 里
(integer) 1
127.0.0.1:6379> smembers myset  --- 获取集合里面的所有 key
1) "World"
2) "Hello"
127.0.0.1:6379> sadd myset one  --- 添加一个或者多个元素到集合 (set) 里
(integer) 1
127.0.0.1:6379> sadd myset one --- 插入已存在的元素不成功
(integer) 0
127.0.0.1:6379> sismember myset one  --- 判断值 one 是否是集合中的成员
(integer) 1
127.0.0.1:6379> sismember myset two  --- 判断值 two 是否是集合中的成员
(integer) 0
127.0.0.1:6379> scard myset  --- 获取集合里面的元素数量
(integer) 3
127.0.0.1:6379> smembers myset  --- 获取集合里面的所有 key
1) "one"
2) "World"
3) "Hello"
```

#### Set 的应用场景
社交类 APP 中，会有共同好友的消息，可以轻松通过 set 的交集运算得到结果

### Sorted Set——有序且不重复的元素集合

sorted set 类型与 set 类型很相似，不允许出现重复的元素。主要差别在于 sorted set 中提供了一个分数（score）与每一个成员对应，Redis 根据 score 对成员进行排序

#### Sorted Set 的实例

```
127.0.0.1:6379> zadd dbs 100 redis  --- 添加一个或多个成员到有序集合，或者如果它已经存在更新其分数
(integer) 1
127.0.0.1:6379> zadd dbs 98 memcached  --- 添加一个或多个成员到有序集合，或者如果它已经存在更新其分数
(integer) 1
127.0.0.1:6379> zadd dbs 99 mongodb  --- 添加一个或多个成员到有序集合，或者如果它已经存在更新其分数
(integer) 1
127.0.0.1:6379> zadd dbs 99 leveldb  --- 添加一个或多个成员到有序集合，或者如果它已经存在更新其分数
(integer) 1
127.0.0.1:6379> zcard dbs  --- 得到的有序集合成员的数量
(integer) 4
127.0.0.1:6379> zcount dbs 10 99  --- 计算一个有序集合成员与给定值范围内的分数
(integer) 3
127.0.0.1:6379> zrank dbs leveldb  --- 确定成员的索引在有序集合中
(integer) 1
127.0.0.1:6379> zrank dbs other  --- 确定成员的索引在有序集合中
(nil)
127.0.0.1:6379> zrangebyscore dbs 98 100  --- 返回有序集 key 中，所有 score 值介于 min 和 max 之间 (包括等于 min 或 max) 的成员，有序集成员按 score 值递增 (从小到大) 次序排列
1) "memcached"
2) "leveldb"
3) "mongodb"
4) "redis"
```

#### Sorted Set 的应用场景
sorted set 类型适用于各种类型的排行榜。


### Redis 官方命令手册

查询每个 redis 命令的详细用法，[redis 官方命令手册](http://www.redis.cn/commands.html)


## 参考文档

1. [Redis 使用与实践](https://segmentfault.com/a/1190000010675699)

PS：其它关于 Redis 使用场景和常用命令的文章如下

1. [Redis 中 5 种数据结构的使用场景介绍](https://segmentfault.com/a/1190000004012214)
1. [【Redis】Redis 常用命令](https://segmentfault.com/a/1190000010999677)
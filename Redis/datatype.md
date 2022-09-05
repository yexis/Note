Gredis数据类型：

1. string
2. hash
3. list
4. set
5. zset (sorted set)

#### String（字符串）:

```go
Set key value
Get key
GetRange key start end
GetSet key value
//获取一个或多个key的值
MGet key1[key2...]
//同时设置多个字符串的值
MSet key value [key value...]
Strlen key
```

#### Hash（哈希）：

redis hash 是一个键值集合。

redis hash是一个string类型的field和value的映射表，hash特别适合存储对象。

```go
//Hset 设置单个键值
Hset yex id 13 
//HMset批量设置键值
Hset yex id 13 addr "beijing"
//Hget 获取key对应的value
Hget yex id
```

每个hash最多存储2^32 -1个键值对（40多亿）

#### List(列表)：

双向链表。

redis列表是简单的字符串列表，按照插入允许排序。

```
lpush yex redis
lpush yex mongodb
lpush yex rabitmq
lrange yex 0 10
```

每个List最多存储2^32 -1个键值对

#### Set集合：

redis的set是string的无序集合

集合是通过Hash表实现的。

```go
//sadd命令：添加一个string元素到set集合，成功返回1,如果已经存在，返回0
sadd yex redis
sadd yex mongobdimage-20200706151600856
sadd yex rabitmq
sadd yex rabitmq

smembers yex
```

#### zset有序集合：

与set一样，不允许重复的成员

不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大排序。

zset的成员是唯一的，但是分数却可以重复

```go
//zadd:添加元素到集合，元素在集合中存在则更新score
//格式： zadd key score member
zadd yex 0 redis
zadd yex 0 mongodb
zadd yex 0 rabitmq
zadd yex 0 rabitmq

zrangebyscore yex 0 1000
```

![image-20200706151600856](/home/yex/.config/Typora/typora-user-images/image-20200706151600856.png)
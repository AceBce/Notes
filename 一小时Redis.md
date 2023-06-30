# Redis简介

## 什么是Redis?
Redis是Remote dictionary server的缩写，是一个开源的基于内存的数据存储系统。可以用作数据库缓存和消息队列等场景，也是热门的NoSQL数据库之一。

Redis优点：性能极高

## 安装Redis
MacOS系统,使用homebrew: brew install redis

启动redis服务端: redis-server

启动redis客户端: redis-cli

## Redis和Memcached有什么区别？
共同点：
1. 都是基于内存的数据库，一般都当作缓存来用
2. 都有过期策略
3. 性能都很高

不同点：
1. Redis支持的数据更丰富，而Memcached只支持最简单的key-value类型
2. Redis支持数据的持久化，而Memcached不支持，Memcached重启或挂掉后，数据就没了
3. Redis支持原生集群，Memcached不支持
4. Redis支持发布订阅模型、Lua脚本、事务等功能，Memcached不支持

## 为什么使用Redis作为MySQL的缓存
1. Redis具备「高性能」
    
    相当于MySQL的一个cache
2. Redis支持「高并发」
    
    单台设备的Redis的QPS(Query Per Second每秒钟处理完请求的次数)是MySQL的10倍，直接访问Redis能够承受的请求是远远大于直接访问MySQL的
    
## 图形化界面redisinsight

## Redis常见数据类型
* String字符串
* Hash哈希
* List列表
* Set集合
* Zset有序集合
* BitMap(2.2新增)
* HyperLogLog(2.8新增)
* GEO(3.2新增)
* Stream(5.0新增)

## 字符串数据
String也是k-v结构，最多可以容纳的数据长度是512M
### 内部实现：
String类型的底层数据结构是int和SDS(简单动态字符串)
* SDS不光可以保存文本数据，还可以保存二进制数据，它可以保存图片、音频、视频、压缩文件这样的二进制数据
* SDS获取字符串长度的时间复杂度是O(1),它用len属性记录了字符串长度
* SDS API是安全的，拼接字符串不会造成缓冲区溢出（空间不够会自动扩容）
### 存数据
Redis中的数据是由键值对的形式存储的，所以存数据要指定键和值(在客户端操作)
```
SET key value
```
Redis中的键区分大小写,且默认使用字符串来存储数据
### 取数据
```
GET key
```
### 删除数据
```
DEL key
```
### 判断键是否存在
```
EXISTS key
```
### 查找键
```
KEYS pattern
```
pattern是模式匹配的字符串，*是匹配所有字符串
### 删除所有键（慎用）
```
FLUSHALL
```
### 查看键的过期时间
```
TTL name
```
-1是没有设置过期时间 -2是已经过期了
### 设置过期时间
```
EXPIRE key seconds
```
给一个键设置过期时间

## 列表数据
### 添加数据
```
LPUSH key element
```
给这个key里面加数据,是从头（头是左边）添加的，RPUSH是从右添加的，每次给队尾添加元素
### 查看列表
```
LRANGE key start stop
```
查看列表给定范围内的元素，start从0开始，stop=-1表示最后一个元素
### 删除元素
```
LPOP key [count]
```
从左弹出count个元素
```
LTRIM key start stop
```
删除[start, stop]范围以外的元素，trim是修建的意思
### 查看列表长度
```
LLEN key
```
## 集合数据
### 给集合添加元素
```
SADD key member [member...]
```
集合相当于一个set，给名叫key的集合添加member,里面的元素不能重复
### 显示集合里的所有元素
```
SMEMBERS key
```
显示名叫key的集合里的所有元素
### 判断一个元素是不是在集合里
```
SISMEMBER key member
```
判断member是否在key集合里
### 删除集合里的一个元素
```
SREM key member [member...]
```
## 有序集合
有序集合的每个元素都会关联一个浮点类型的分数,然后按照这个分数从小到大对集合中的元素进行排序,有序集合中的元素是唯一的，但是分数是**可以重复的**
### 给有序集合添加元素
```
ZADD key score member
```
给有序集合添加元素，一个分数一个元素
### 查看有序集合元素
```
ZRANGE key start stop
```
不输出分数
```
ZRANGE key start stop WITHSCORES
```
### 查看某个元素的分数
```
ZSCORE key member
```
查看这个成员key的分数
### 查看某个元素的排名
```
ZRANK key member
```
查看分数从小到大的排名
```
ZREVRANK key member
```
查看分数从大到小的排名
## 哈希
哈希是一个字符类型的字段和值的映射表,是一个键值对的集合(map)

### 给哈希表添加键值对
```
HSET key field value
```
给一个叫key的哈希表添加一个field, value对

### 查询一个哈希表的一个键的值
```
HGET key field
```
查询哈希表key的field键的值

### 删除一个键
```
HDEL key field
```

### 获取所有键值对
```
HGETALL key
```
获取哈希表key中的所有键值对

### 获取所有键
```
HKEYS key
```
获取哈希表key中所有的键

## 发布订阅模式
### 订阅
```
subscribe channel
```
订阅channel频道，接收channel频道传来的消息
### 发布
```
publish channel message
```
给channel频道发一条message

## 消息队列Stream
### 给消息队列放一个消息
一个消息就是一个键值对
```
XADD key ID field value
```
ID如果用*代替就是自动生成ID，如果手动生成ID要保证ID自增
### 查看消息队列的内容
```
XRANGE key start end
```
这里可以使用xrange key - +来输出消息队列的全部内容
### 查看消息队列长度
```
XLEN key
```
### 读取消息
```
XREAD
```

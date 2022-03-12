## Redis常用命令学习

```shell
#连接
redis-cli -h 127.0.0.1 -p 8679
#密码连接，否则不可以用
127.0.0.1:8679>auth 123456
#设置键值对
127.0.0.1:8679>set abc abc
#获取值
127.0.0.1:8679>get abc
#获取所有的键
127.0.0.1:8679>keys *
#删除所有的数据
127.0.0.1:8679>flushdb
#SET if Not eXists,没有值设置，有值不设置,返回值为1成功，0失败
127.0.0.1:8679>setnx abc asd
```

## 常用命令学习

```shell
#查询集群信息
cluster nodes
#查询所有key
keys *
#查询一个key的类型
type keyname
#查询一个key的过期剩余秒数，-1表示没有过期时间，-2表示没有这个key
ttl keyname
#查询一个key的过期剩余毫秒数
pttl keyname
#移除key的过期设置
persist keyname


#key的增删查改
#查询
keys *
#判断key是否存在
EXISTS key

#添加
set key value

#删除
DEL key [key ...]

#修改
set key value
#修改失效日期
EXPIRE key seconds
```

## zset命令学习

```shell
# 添加元素------> 添加已经存在的元素，无论score是否改变，返回值为0，不存在的元素返回值为1
ZADD rank_data 10 helloworld10
ZADD rank_data 11 helloworld11
# 添加多个元素
ZADD rank_data 12 helloworld12 13 helloworld13
ZADD rank_data 14 helloworld14 15 helloworld15

# 查询所有的元素和分数-----------------获取所有数据（排行榜功能）
ZRANGE rank_data 0 -1 WITHSCORES
# 查询所有的元素
ZRANGE rank_data 0 -1

# 返回有序集合的个数,key不存在返回0
ZCARD rank_data

# 获取score值在某个范围（闭区间）的数据
ZCOUNT key min max
zcount rank_data 10 12   ----->3

# 对元素的分值进行增加或减少（负值）------------>
# 当 key 不存在，或 member 不是 key 的成员时， ZINCRBY key increment member 等同于 ZADD key increment member 
ZINCRBY key increment member
ZINCRBY rank_data 1 helloworld15

# ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]
















```




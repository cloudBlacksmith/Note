# day04

## 回顾

## nginx

## Redis

### 引言

#### 数据库压力过大

 用户访问量增大，请求数量也随之增大，数据压力就过大

#### 数据不同步

 多态数据库服务器之间数据不同步

#### 传统锁失效

 多台服务器之间的锁，已经不存在互斥性

### Redis

#### NoSQL介绍

 - redis就是一款NoSQL的数据库
 - NoSQL的全称是not only SQL
 - key value
 - 文档型
 - 面向列
 - 图形化
 - 除了关系型数据库都是非关系型数据库
 - NoSQL只是一种概念，泛指非关系型数据库，和关系型数据库做一个区分

#### Redis介绍

 remote dictionary server

 是一款基于内存的键值类型的可持久化的非关系的缓存数据库

 速度快

 一维结构

 缓存

 可以达到对于256长度的字符串进行每秒钟11w次的读和八万一次的写的操作

#### 安装

 解压redis的压缩包

 redis-server是redis的服务端

 redis-cli是redis的客户端，可以借助客户端与服务器进行交互

 客户端发送一个命令“ping”，得到服务器端的相应是“pong”，证明客户端与服务器端连接成功建立

#### redis的数据类型

redis的值的类型有以下五种：

 String

 ```shell
 set key value
 127.0.0.1:6379> set hello world
 OK
 127.0.0.1:6379> get hello
 "world"
  
 mset key1 value1 key2 value2 ...
 127.0.0.1:6379> mset xun wukong zhu bajie sha heshang
 OK
 127.0.0.1:6379> get xun
 "wukong"
 127.0.0.1:6379> get sha
 "heshang"
 
 mget key1 key2 ...
 127.0.0.1:6379> mget xun zhu sha
 1) "wukong"
 2) "bajie"
 3) "heshang"
 
 incr key
 decr key
 127.0.0.1:6379> set num 1
 OK
 127.0.0.1:6379> incr num
 (integer) 2
 127.0.0.1:6379> incr num
 (integer) 3
 127.0.0.1:6379> get num
 "3"
 127.0.0.1:6379> set money 10000
 OK
 127.0.0.1:6379> incr money
 (integer) 10001
 127.0.0.1:6379> get money
 "10001"
 127.0.0.1:6379> decr money
 (integer) 10000
 127.0.0.1:6379> get money
 "10000"
 
 setex key seconds value
 127.0.0.1:6379> setex subject 5 cloud
 OK
 127.0.0.1:6379> get subject
 (nil)
 127.0.0.1:6379> setex subject 10 cloud
 OK
 127.0.0.1:6379> get subject
 "cloud"
 127.0.0.1:6379> get subject
 "cloud"
 127.0.0.1:6379> get subject
 (nil)
 
 setnx key value  #如果该key不存在，则设置值给该key，如果该key存在，则不设置，该key的值还是之前的数值
 127.0.0.1:6379> setnx zhang san
 (integer) 1
 127.0.0.1:6379> get zhang
 "san"
 127.0.0.1:6379> set zhang xiaosan
 OK
 127.0.0.1:6379> get zhang
 "xiaosan"
 127.0.0.1:6379> setnx li xiaosi
 (integer) 1
 127.0.0.1:6379> setnx li xiaowu
 (integer) 0
 127.0.0.1:6379> get li
 "xiaosi"
 
 append key value #给指定的key的后面追加指定的value
 127.0.0.1:6379> get hello
 "world"
 127.0.0.1:6379> append hello 000
 (integer) 8
 127.0.0.1:6379> get hello
 "world000"
 ```

 hash

 ```
 hset key field value
 127.0.0.1:6379> hset stu01 name zhangsan
 (integer) 1
 127.0.0.1:6379> hset stu01 age 20
 (integer) 1
 127.0.0.1:6379> hset stu01 score 99
 (integer) 1
 127.0.0.1:6379> hset stu01 sex boy
 (integer) 1
 127.0.0.1:6379> hget stu01 name
 "zhangsan"
 127.0.0.1:6379> hget stu01 sex
 "boy"
 
 hmset key field01 value01 field02 value02 ...
 hget key fieild01
 hgetall key
 127.0.0.1:6379> hset stu01 name zhangsan
 (integer) 1
 127.0.0.1:6379> hset stu01 age 20
 (integer) 1
 127.0.0.1:6379> hset stu01 score 99
 (integer) 1
 127.0.0.1:6379> hset stu01 sex boy
 (integer) 1
 127.0.0.1:6379> hget stu01 name
 "zhangsan"
 127.0.0.1:6379> hget stu01 sex
 "boy"
 127.0.0.1:6379> hmset stu02 name lisi age girl score 100
 OK
 127.0.0.1:6379> hset stu02 age 20
 (integer) 0
 127.0.0.1:6379> hget stu02 age
 "20"
 127.0.0.1:6379> hgetall stu02
 1) "name"
 2) "lisi"
 3) "age"
 4) "20"
 5) "score"
 6) "100"
 127.0.0.1:6379> hgetall stu01
 1) "name"
 2) "zhangsan"
 3) "age"
 4) "20"
 5) "score"
 6) "99"
 7) "sex"
 8) "boy"
 
 hincrby key filed increment  #给key中的feild值增加increment数量
 127.0.0.1:6379> hgetall stu01
 1) "name"
 2) "zhangsan"
 3) "age"
 4) "20"
 5) "score"
 6) "99"
 7) "sex"
 8) "boy"
 127.0.0.1:6379> hincrby  stu01 age 2
 (integer) 22
 127.0.0.1:6379> hget stu01 age
 "22"
 127.0.0.1:6379> hincrby stu01 age 10
 (integer) 32
 
 hexist key field   #如果存在则返回1，否则返回0
 127.0.0.1:6379> hexists stu01 sex
 (integer) 1
 127.0.0.1:6379> hexists stu02 sex
 (integer) 0
 127.0.0.1:6379> hgetall stu01
 1) "name"
 2) "zhangsan"
 3) "age"
 4) "32"
 5) "score"
 6) "99"
 7) "sex"
 8) "boy"
 127.0.0.1:6379> hgetall stu02
 1) "name"
 2) "lisi"
 3) "age"
 4) "20"
 5) "score"
 6) "100"
 
 hdel key filed			# 删除指定key下的指定feild
 127.0.0.1:6379> hgetall stu01
 1) "name"
 2) "zhangsan"
 3) "age"
 4) "32"
 5) "score"
 6) "99"
 7) "sex"
 8) "boy"
 127.0.0.1:6379> hdel stu01 age
 (integer) 1
 127.0.0.1:6379> hgetall stu01
 1) "name"
 2) "zhangsan"
 3) "score"
 4) "99"
 5) "sex"
 6) "boy"
 
 hvals key #得到指定key中所有的属性值
 hkeys key #得到指定key中所有的filed
 127.0.0.1:6379> hvals stu01
 1) "zhangsan"
 2) "99"
 3) "boy"
 127.0.0.1:6379> hkeys stu01
 1) "name"
 2) "score"
 3) "sex"
 
 hlen key：获取指定key中所有的属性的数量
 127.0.0.1:6379> hlen stu02
 (integer) 3
 127.0.0.1:6379> hgetall stu02
 1) "name"
 2) "lisi"
 3) "age"
 4) "20"
 5) "score"
 6) "100"
 ```

 list

 ```shell
 lpush key value1 value2 ...
 rpush key value1 value2 ...
 lrange key start end
 127.0.0.1:6379> lpush subje python java c hive
 (integer) 4
 127.0.0.1:6379> lrange subje 0 10
 1) "hive"
 2) "c"
 3) "java"
 4) "python"
 127.0.0.1:6379> rpush major cloud bigdata software
 (integer) 3
 127.0.0.1:6379> lrange major 0 10
 1) "cloud"
 2) "bigdata"
 3) "software"
 
 lpushx key value	# 如果存在，则左插value,不存在则不插入，类型不对报错
 rpushx key value
 127.0.0.1:6379> lpushx abc 1111		# abc是string，无法当成list来进行数据的插入操作
 (error) WRONGTYPE Operation against a key holding the wrong kind of value
 127.0.0.1:6379> lpushx aaa 1111
 (integer) 0
 127.0.0.1:6379> rpushx aaa 222
 (integer) 0
 127.0.0.1:6379> rpush major 333
 (integer) 9
 127.0.0.1:6379> lrange major 0 20
 1) "111"
 2) "cloud"
 3) "bigdata"
 4) "software"
 5) "cloud"
 6) "yuwen"
 7) "yingyu"
 8) "ying"
 9) "333"
 
 lpop key		# key的队首数据弹出
 rpop key		# key的队尾数据弹出
 127.0.0.1:6379> lpop major
 "111"
 127.0.0.1:6379> lrange major 0 10
 1) "cloud"
 2) "bigdata"
 3) "software"
 4) "cloud"
 5) "yuwen"
 6) "yingyu"
 7) "ying"
 8) "333"
 127.0.0.1:6379> rpop major
 "333"
 127.0.0.1:6379> lrange major 0 10
 1) "cloud"
 2) "bigdata"
 3) "software"
 4) "cloud"
 5) "yuwen"
 6) "yingyu"
 7) "ying"
 
 lindex key index 	# 拿到指定key中index处的数值，注意index是从零开始
 127.0.0.1:6379> lrange major 0 10
 1) "cloud"
 2) "bigdata"
 3) "software"
 4) "cloud"
 5) "yuwen"
 6) "yingyu"
 7) "ying"
 127.0.0.1:6379>
 127.0.0.1:6379> lindex major 2
 "software"
 127.0.0.1:6379> lindex major 1
 "bigdata"
 
 llen key 	#拿到指定key的数值长度
 127.0.0.1:6379> llen major
 (integer) 7
 
 
 lrem key count value	#删除指定key中count个value
 127.0.0.1:6379> lrange major 0 19
 1) "cloud"
 2) "bigdata"
 3) "software"
 4) "cloud"
 5) "yuwen"
 6) "yingyu"
 7) "ying"
 127.0.0.1:6379> lrem major 2 cloud
 (integer) 2
 127.0.0.1:6379> lrange major 0 10
 1) "bigdata"
 2) "software"
 3) "yuwen"
 4) "yingyu"
 5) "ying"
 127.0.0.1:6379> lrem major 2 ying
 (integer) 1
 127.0.0.1:6379> lrem major 3 aaa
 (integer) 0
 
 ltrim key start stop # 保留指定key中才弄个start到stop的值
 127.0.0.1:6379> lrange major 0 19
 1) "4444"
 2) "333"
 3) "222"
 4) "111"
 5) "bigdata"
 6) "software"
 7) "yuwen"
 8) "yingyu"
 127.0.0.1:6379> ltrim major 3 8
 OK
 127.0.0.1:6379> lrange major 0 10
 1) "111"
 2) "bigdata"
 3) "software"
 4) "yuwen"
 5) "yingyu"
 
 
 rpoplpush source destination   # 将源数组list的末尾一个元素插入到目标数组的列表的开头
 127.0.0.1:6379> lrange major 0 10
 1) "111"
 2) "bigdata"
 3) "software"
 4) "yuwen"
 5) "yingyu"
 127.0.0.1:6379> lrange subje 0 10
 1) "hive"
 2) "c"
 3) "java"
 4) "python"
 127.0.0.1:6379> rpoplpush subje major
 "python"
 127.0.0.1:6379> lrange subje 0 9
 1) "hive"
 2) "c"
 3) "java"
 127.0.0.1:6379> lrange major 0 9
 1) "python"
 2) "111"
 3) "bigdata"
 4) "software"
 5) "yuwen"
 6) "yingyu"
 ```

 set

 zset
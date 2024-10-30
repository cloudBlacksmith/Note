# day05

## 回顾

## Redis

### 数据类型

 String

 hash

 list

 set

 ```shell
 sadd key value1 value2 value3 ...	# 相同元素会被自动剔除
 smembers key 						# 加入后的元素无序
 127.0.0.1:6379> sadd stus wukong bajie shaseng tangseng
 (integer) 4
 127.0.0.1:6379> smembers stus
 1) "bajie"
 2) "wukong"
 3) "tangseng"
 4) "shaseng"
 127.0.0.1:6379> sadd user songjiang wuyong wusong wusong wuerlang
 (integer) 4
 127.0.0.1:6379> smembers user
 1) "wuerlang"
 2) "wusong"
 3) "wuyong"
 4) "songjiang"
 
 spop key [count]		# count没有默认就是1
 127.0.0.1:6379> spop user
 "songjiang"
 127.0.0.1:6379> smembers user
 1) "wuerlang"
 2) "wusong"
 3) "wuyong"
 127.0.0.1:6379> spop user 2
 1) "wuyong"
 2) "wusong"
 127.0.0.1:6379> smembers user
 1) "wuerlang"
 
 sinter key1 [key2 key3 ...]		#求交集
 127.0.0.1:6379> smembers stus
 1) "bajie"
 2) "wukong"
 3) "tangseng"
 4) "shaseng"
 127.0.0.1:6379> smembers person
 1) "bajie"
 2) "wukong"
 3) "zhizunbao"
 127.0.0.1:6379> sinter stus person
 1) "bajie"
 2) "wukong"
 
 sunion key1 [key2 key3]
 127.0.0.1:6379> sunion stus person
 1) "shaseng"
 2) "bajie"
 3) "wukong"
 4) "zhizunbao"
 5) "tangseng"
 
 sdiff key1 [key2]
 127.0.0.1:6379> sdiff stus person
 1) "tangseng"
 2) "shaseng"
 127.0.0.1:6379> sdiff person stus
 1) "zhizunbao"
 
 srem key member1 [member2 member3 ...]
 127.0.0.1:6379> smembers person
 1) "wukong"
 2) "bajie"
 3) "puti"
 4) "daiyu"
 5) "baoyu"
 6) "zhizunbao"
 127.0.0.1:6379> srem person baoyu daiyu
 (integer) 2
 127.0.0.1:6379> smembers person
 1) "wukong"
 2) "bajie"
 3) "puti"
 4) "zhizunbao"
 
 sismember key member   # 判断指定的key所对应的set中是否含有member
 127.0.0.1:6379> smembers person
 1) "wukong"
 2) "bajie"
 3) "puti"
 4) "zhizunbao"
 127.0.0.1:6379> sismember person zhizunbao
 (integer) 1
 127.0.0.1:6379> sismember person wukong
 (integer) 1
 127.0.0.1:6379> sismember person dasheng
 (integer) 0
 ```

 zset

 ```sh
 zadd key score1 value1 score2 value2 ...
 zrange key start stop
 127.0.0.1:6379> zadd subj 70 java 80 python 20 bigdata 100 cloud
 (integer) 4
 127.0.0.1:6379> zrange subj 0 9
 1) "bigdata"
 2) "java"
 3) "python"
 4) "cloud"
 
 zincrby key score member  # 给key中指定的member添加score数量的分值
 127.0.0.1:6379> zincrby subj 20 bigdata
 "80"
 127.0.0.1:6379> zrange subj 0 9
 1) "java"
 2) "bigdata"
 3) "python"
 4) "cloud"
 
 zcard key 	#获取zset中的元素的数量
 127.0.0.1:6379> zcard subj
 (integer) 4
 
 zscore key member  # 获取指定key下member的score值
 127.0.0.1:6379> zscore subj python
 "80"
 
 zcount key min max	# 获取指定key下分支从min到max区间内的member的元素个数
 127.0.0.1:6379> zcount subj 0 9
 (integer) 0
 127.0.0.1:6379> zcount subj 0 100
 (integer) 4
 127.0.0.1:6379> zcount subj 90 100
 (integer) 1
 127.0.0.1:6379> zcount subj 80 100
 (integer) 3
 
 zrange key min max withscores #将元素连带分值一起显示
 127.0.0.1:6379> zrange subj 0 100 withscores
 1) "java"
 2) "71"
 3) "bigdata"
 4) "80"
 5) "python"
 6) "80"
 7) "cloud"
 8) "100"
 
 zrevrange key min max [withscores]	# 逆序输出key中的所有元素
 127.0.0.1:6379> zrevrange subj 0 100 withscores
 1) "cloud"
 2) "100"
 3) "python"
 4) "80"
 5) "bigdata"
 6) "80"
 7) "java"
 8) "71"
 
 zrangebyscore key min max limit offset count	# 获取可以中score从min到max区间内的所有值，如果有limit分页，则从offset开始取count个元素
 127.0.0.1:6379> zrangebyscore subj 60 100
 1) "java"
 2) "bigdata"
 3) "python"
 4) "cloud"
 127.0.0.1:6379> zrangebyscore subj 60 100 limit 2 2
 1) "python"
 2) "cloud"
 127.0.0.1:6379> zrangebyscore subj 60 100 limit 0 2
 1) "java"
 2) "bigdata"
 127.0.0.1:6379> zrangebyscore subj 60 100 limit 0 3
 1) "java"
 2) "bigdata"
 3) "python"
 127.0.0.1:6379> zrangebyscore subj 60 100 limit 3 3
 1) "cloud"
 127.0.0.1:6379> zrangebyscore subj 0 100
 1) "java"
 2) "bigdata"
 3) "python"
 4) "cloud"
 127.0.0.1:6379> zrangebyscore subj 0 100 limit 0 2
 1) "java"
 2) "bigdata"
 127.0.0.1:6379> zrangebyscore subj 0 100 limit 2 2
 1) "python"
 2) "cloud"
 127.0.0.1:6379> zrangebyscore subj 0 100 limit 4 2
 (empty list or set)
 127.0.0.1:6379> zrangebyscore subj 0 100 limit 0 3
 1) "java"
 2) "bigdata"
 3) "python"
 127.0.0.1:6379> zrangebyscore subj 0 100 limit 3 3
 1) "cloud"
 ```

 还有hyperloglog， geo， streams， bitmaps， pub/sub, modules 

### 配置文件

 redis的核心配置文件

 ```
 bind 127.0.0.1
 port 6379
 loglevel notice	
 	日志级别含有四个值：
 	debug
 	verbose
 	notice
 	warning
 databases 16	#数据库个数
 
 save 900 1		#rdb的持久化策略
 save 300 10
 save 60 10000
 
 dbfilename dump.rdb		#数据库的备份文件
 
 requirepass foobared	#设置密码	
 
 appendfsync always	# 日志模式的同步策略
 appendfsync everysec
 appendfsync no
 ```

 设置核心配置参数

 ```shell
 127.0.0.1:6379> config get loglevel
 1) "loglevel"
 2) "notice"
 127.0.0.1:6379> config set loglevel debug
 OK
 127.0.0.1:6379> config get loglevel
 1) "loglevel"
 2) "debug"
 127.0.0.1:6379> config set loglevel aaaa
 (error) ERR Invalid argument 'aaaa' for CONFIG SET 'loglevel'
 127.0.0.1:6379> config get appendfsync
 1) "appendfsync"
 2) "everysec"
 127.0.0.1:6379> config set appendfsync no
 OK
 127.0.0.1:6379> config get appendfsync
 1) "appendfsync"
 2) "no"
 127.0.0.1:6379> config set appendfsync aaa
 (error) ERR Invalid argument 'aaa' for CONFIG SET 'appendfsync'
 127.0.0.1:6379> config set requirepass aaa
 OK
 127.0.0.1:6379> ping
 (error) NOAUTH Authentication required.
 127.0.0.1:6379> auth aaa
 OK
 127.0.0.1:6379> ping
 PONG
 127.0.0.1:6379> config set requirepass
 (error) ERR Wrong number of arguments for CONFIG set
 127.0.0.1:6379> config set requirepass ''
 OK
 127.0.0.1:6379> ping
 PONG
 ```



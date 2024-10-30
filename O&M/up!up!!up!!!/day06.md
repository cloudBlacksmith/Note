# day06

## 回顾

## redis的主从

 复制两份redis

 一个叫master

 一个slave

 master中可以不做任何修改

 slave中找到redis的核心配置文件，修改两个地方

 ```
 port 6380
 slaveof 127.0.0.1 6379
 ```


## redis集群

一、概述
    Redis3.0版本之后支持Cluster.
1.1、redis cluster的现状
 　　目前redis支持的cluster特性：
　　1):节点自动发现
　　2):slave->master 选举,集群容错
　　3):Hot resharding:在线分片
　　4):进群管理:cluster xxx
　　5):基于配置(nodes-port.conf)的集群管理
　　6):ASK 转向/MOVED 转向机制.
1.2、redis cluster 架构
　　1)redis-cluster架构图

![img](./imgs\redis集群01.jpg)

　　架构细节:
　　(1)所有的redis节点彼此互联(PING-PONG机制),内部使用二进制协议优化传输速度和带宽.
　　(2)节点的fail是通过集群中超过半数的节点检测失效时才生效.
　　(3)客户端与redis节点直连,不需要中间proxy层.客户端不需要连接集群所有节点,连接集群中任何一个可用节点即可
　　(4)redis-cluster把所有的物理节点映射到[0-16383]slot上,cluster 负责维护node<->slot<->value

   2) redis-cluster选举:容错

![](./imgs\redis集群02.jpg)

　　(1)领着选举过程是集群中所有master参与,如果半数以上master节点与master节点通信超过(cluster-node-timeout),认为当前master节点挂掉.
　　(2):什么时候整个集群不可用(cluster_state:fail),当集群不可用时,所有对集群的操作做都不可用，收到((error) CLUSTERDOWN The cluster is down)错误
    　　a:如果集群任意master挂掉,且当前master没有slave.集群进入fail状态,也可以理解成进群的slot映射[0-16383]不完成时进入fail状态.
    　　b:如果进群超过半数以上master挂掉，无论是否有slave集群进入fail状态.

二、redis cluster安装
    1、下载和解包
123 cd /usr/local/wget http://download.redis.io/releases/redis-3.2.1.tar.gztar -zxvf /redis-3.2.1.tar.gz
　2、 编译安装
 cd redis-3.2.1
 make && make install

  3、创建redis节点
     测试我们选择2台服务器，分别为：192.168.1.237，192.168.1.238.每分服务器有3个节点。
  我先在192.168.1.237创建3个节点：

  cd /usr/local/
  mkdir redis_cluster  //创建集群目录
  mkdir 7000 7001 7002  //分别代表三个节点    其对应端口 7000 7001 7002
 //创建7000节点为例，拷贝到7000目录
 cp /usr/local/redis-3.2.1/redis.conf  ./redis_cluster/7000/   
 //拷贝到7001目录
 cp /usr/local/redis-3.2.1/redis.conf  ./redis_cluster/7001/   
 //拷贝到7002目录
 cp /usr/local/redis-3.2.1/redis.conf  ./redis_cluster/7002/   

   分别对7001，7002、7003文件夹中的3个文件修改对应的配置

daemonize    yes                          //redis后台运行
pidfile  /var/run/redis_7000.pid          //pidfile文件对应7000,7002,7003
port  7000                                //端口7000,7002,7003
cluster-enabled  yes                      //开启集群  把注释#去掉
cluster-config-file  nodes_7000.conf      //集群的配置  配置文件首次启动自动生成 7000,7001,7002
cluster-node-timeout  5000                //请求超时  设置5秒够了
appendonly  yes                           //aof日志开启  有需要就开启，它会每次写操作都记录一条日志

   在192.168.1.238创建3个节点：对应的端口改为7003,7004,7005.配置对应的改一下就可以了。
   4、两台机启动各节点(两台服务器方式一样)

cd /usr/local
redis-server  redis_cluster/7000/redis.conf
redis-server  redis_cluster/7001/redis.conf
redis-server  redis_cluster/7002/redis.conf
redis-server  redis_cluster/7003/redis.conf
redis-server  redis_cluster/7004/redis.conf
redis-server  redis_cluster/7005/redis.conf

   5、查看服务
      ps -ef | grep redis   #查看是否启动成功
     netstat -tnlp | grep redis #可以看到redis监听端口
三、创建集群
  前面已经准备好了搭建集群的redis节点，接下来我们要把这些节点都串连起来搭建集群。官方提供了一个工具：redis-trib.rb(/usr/local/redis-3.2.1/src/redis-trib.rb) 看后缀就知道这鸟东西不能直接执行，它是用ruby写的一个程序，所以我们还得安装ruby.
yum -y install ruby ruby-devel rubygems rpm-build 
  再用 gem 这个命令来安装 redis接口    gem是ruby的一个工具包.
gem install redis    //等一会儿就好了
当然，方便操作，两台Server都要安装。
  上面的步骤完事了，接下来运行一下redis-trib.rb

 /usr/local/redis-3.2.1/src/redis-trib.rb
   Usage: redis-trib <command> <options> <arguments ...>
   reshard        host:port
                  --to <arg>
                  --yes
                  --slots <arg>
                  --from <arg>
  check          host:port
  call            host:port command arg arg .. arg
  set-timeout    host:port milliseconds
  add-node        new_host:new_port existing_host:existing_port
                  --master-id <arg>
                  --slave
  del-node        host:port node_id
  fix            host:port
  import          host:port
                  --from <arg>
  help            (show this help)
  create          host1:port1 ... hostN:portN
                  --replicas <arg>
For check, fix, reshard, del-node, set-timeout you can specify the host and port of any working node in the cluster.

     看到这，应该明白了吧， 就是靠上面这些操作 完成redis集群搭建的.
 确认所有的节点都启动，接下来使用参数create 创建 (在192.168.1.237中来创建)
 /usr/local/redis-3.2.1/src/redis-trib.rb  create  --replicas  1  192.168.1.237:7000 192.168.1.237:7001  192.168.1.237:7003 192.168.1.238:7003  192.168.1.238:7004  192.168.1.238:7005
    解释下， --replicas  1  表示 自动为每一个master节点分配一个slave节点    上面有6个节点，程序会按照一定规则生成 3个master（主）3个slave(从)
    前面已经提醒过的 防火墙一定要开放监听的端口，否则会创建失败。
 运行中，提示Can I set the above configuration? (type 'yes' to accept): yes    //输入yes
 接下来 提示  Waiting for the cluster to join..........  安装的时候在这里就一直等等等，没反应，傻傻等半天，看这句提示上面一句，Sending Cluster Meet Message to join the Cluster.
    这下明白了，我刚开始在一台Server上去配，也是不需要等的，这里还需要跑到Server2上做一些这样的操作。
    在192.168.1.238, redis-cli -c -p 700*  分别进入redis各节点的客户端命令窗口， 依次输入 cluster meet 192.168.1.238 7000……
    回到Server1，已经创建完毕了。
    查看一下 /usr/local/redis/src/redis-trib.rb check 192.168.1.237:7000
    到这里集群已经初步搭建好了。

四、测试
1）get 和 set数据
    redis-cli -c -p 7000
    进入命令窗口，直接 set  hello  howareyou
    直接根据hash匹配切换到相应的slot的节点上。
    还是要说明一下，redis集群有16383个slot组成，通过分片分布到多个节点上，读写都发生在master节点。
  2）假设测试
    果断先把192.168.1.238服务Down掉，（192.168.1.238有1个Master, 2个Slave） ,  跑回192.168.1.238, 查看一下 发生了什么事，192.168.1.237的3个节点全部都是Master，其他几个Server2的不见了
    测试一下，依然没有问题，集群依然能继续工作。
    原因：  redis集群  通过选举方式进行容错，保证一台Server挂了还能跑，这个选举是全部集群超过半数以上的Master发现其他Master挂了后，会将其他对应的Slave节点升级成Master.
    疑问： 要是挂的是192.168.1.237怎么办？    哥试了，cluster is down!!    没办法，超过半数挂了那救不了了，整个集群就无法工作了。 要是有三台Server，每台两Master，切记对应的主从节点
            不要放在一台Server,别问我为什么自己用脑子想想看，互相交叉配置主从，挂哪台也没事，你要说同时两台crash了，呵呵哒......
  3）关于一致性
    我还没有这么大胆拿redis来做数据库持久化哥网站数据，只是拿来做cache，官网说的很清楚，Redis Cluster is not able to guarantee strong consistency. 

 五、安装遇到的问题
     1、
　　CC adlist.o
　　/bin/sh: cc: command not found
　　make[1]: *** [adlist.o] Error 127
　　make[1]: Leaving directory `/usr/local/redis-3.2.1/src
　　make: *** [all] Error 2
     解决办法：GCC没有安装或版本不对，安装一下
yum  install  gcc
   2、
　　zmalloc.h:50:31:
　　error: jemalloc/jemalloc.h: No such file or directory
　　zmalloc.h:55:2: error:

　　#error "Newer version of jemalloc required"
　　make[1]: *** [adlist.o] Error
　　1
　　make[1]: Leaving directory `/data0/src/redis-2.6.2/src
　　make: *** [all]
　　Error 2
    解决办法：原因是没有安装jemalloc内存分配器，可以安装jemalloc 或 直接
     输入make MALLOC=libc  && make install
记住该记住的,忘记该忘记的,改变能改变的,接受不能改变的!




什么是redis的雪崩和穿透(击穿)
1.什么是缓存穿透
    一般的缓存系统，都是按照key值去缓存查询，如果不存在对应的value，就应该去DB中查找 。这个时候，如果请求的并发量很大，就会对后端的DB系统造成很大的压力。这就叫做缓存穿透。关键词：缓存value为空；并发量很大去访问DB。

造成的原因
1.业务自身代码或数据出现问题；2.一些恶意攻击、爬虫造成大量空的命中，此时会对数据库造成很大压力。
解决方法
1.设置布隆过滤器，将所有可能存在的数据哈希到一个足够大的bitmap中，一个一定不存在的数据会被这个bitmap拦截掉，
从避免了对底层存储系统的查询压力。
2. 如果一个查询返回的数据为空，不管是数据不存在还是系统故障，我们仍然把这个结果进行缓存，但是它的过期时间会很短
最长不超过5分钟。

二、雪崩
1.什么是雪崩
因为缓存层承载了大量的请求，有效的保护了存储 层，但是如果缓存由于某些原因，整体不能够提供服务，于是所有的请求，就会到达存储层，存储层的调用量就会暴增，造成存储层也会挂掉的情况。缓存雪崩的英文解释是奔逃的野牛，指的是缓存层当掉之后，并发流量会像奔腾的野牛一样，大量后端存储。
存在这种问题的一个场景是：当缓存服务器重启或者大量缓存集中在某一个时间段失效，这样在失效的时候，大量数据会去直接访问DB，此时给DB很大的压力。


2.解决方法
（1）设置redis集群和DB集群的高可用，如果redis出现宕机情况，可以立即由别的机器顶替上来。这样可以防止一部分的风险。
（2）使用互斥锁
在缓存失效后，通过加锁或者队列来控制读和写数据库的线程数量。比如：对某个key只允许一个线程查询数据和写缓存，其他线程等待。单机的话，可以使用synchronized或者lock来解决，如果是分布式环境，可以是用redis的setnx命令来解决。
（3）不同的key,可以设置不同的过期时间，让缓存失效的时间点不一致，尽量达到平均分布。
（4）永远不过期
redis中设置永久不过期，这样就保证了，不会出现热点问题，也就是物理上不过期。
（5）资源保护
使用netflix的hystrix，可以做各种资源的线程池隔离，从而保护主线程池。



实例解读什么是Redis缓存穿透、缓存雪崩和缓存击穿



传陆编程
Redis缓存的使用，极大的提升了应用程序的性能和效率，特别是数据查询方面。但同时，它也带来了一些问题。其中，最要害的问题，就是数据的一致性问题，从严格意义上讲，这个问题无解。如果对数据的一致性要求很高，那么就不能使用缓存。
另外的一些典型问题就是，缓存穿透、缓存雪崩和缓存击穿。目前，业界也都有比较流行的解决方案。本篇文章，并不是要更加完美的解决这三个问题，也不是要颠覆业界流行的解决方案。而是，从实际代码操作，来演示这三个问题现象。之所以要这么做，是因为，仅仅看这些问题的学术解释，脑袋里很难有一个很形象的概念，有了实际的代码演示，可以加深对这些问题的理解和认识。
缓存穿透
缓存穿透，是指查询一个数据库一定不存在的数据。正常的使用缓存流程大致是，数据查询先进行缓存查询，如果key不存在或者key已经过期，再对数据库进行查询，并把查询到的对象，放进缓存。如果数据库查询对象为空，则不放进缓存。

Redis缓存流程
代码流程
参数传入对象主键ID根据key从缓存中获取对象如果对象不为空，直接返回如果对象为空，进行数据库查询如果从数据库查询出的对象不为空，则放入缓存（设定过期时间）想象一下这个情况，如果传入的参数为-1，会是怎么样？这个-1，就是一定不存在的对象。就会每次都去查询数据库，而每次查询都是空，每次又都不会进行缓存。假如有恶意攻击，就可以利用这个漏洞，对数据库造成压力，甚至压垮数据库。即便是采用UUID，也是很容易找到一个不存在的KEY，进行攻击。
小编在工作中，会采用缓存空值的方式，也就是【代码流程】中第5步，如果从数据库查询的对象为空，也放入缓存，只是设定的缓存过期时间较短，比如设置为60秒。

缓存空值
缓存雪崩
缓存雪崩，是指在某一个时间段，缓存集中过期失效。
产生雪崩的原因之一，比如在写本文的时候，马上就要到双十二零点，很快就会迎来一波抢购，这波商品时间比较集中的放入了缓存，假设缓存一个小时。那么到了凌晨一点钟的时候，这批商品的缓存就都过期了。而对这批商品的访问查询，都落到了数据库上，对于数据库而言，就会产生周期性的压力波峰。
小编在做电商项目的时候，一般是采取不同分类商品，缓存不同周期。在同一分类中的商品，加上一个随机因子。这样能尽可能分散缓存过期时间，而且，热门类目的商品缓存时间长一些，冷门类目的商品缓存时间短一些，也能节省缓存服务的资源。

缓存时间加入suijiyinzi
其实集中过期，倒不是非常致命，比较致命的缓存雪崩，是缓存服务器某个节点宕机或断网。因为自然形成的缓存雪崩，一定是在某个时间段集中创建缓存，那么那个时候数据库能顶住压力，这个时候，数据库也是可以顶住压力的。无非就是对数据库产生周期性的压力而已。而缓存服务节点的宕机，对数据库服务器造成的压力是不可预知的，很有可能瞬间就把数据库压垮。
缓存击穿
缓存击穿，是指一个key非常热点，在不停的扛着大并发，大并发集中对这一个点进行访问，当这个key在失效的瞬间，持续的大并发就穿破缓存，直接请求数据库，就像在一个屏障上凿开了一个洞。
小编在做电商项目的时候，把这货就成为“爆款”。
其实，大多数情况下这种爆款很难对数据库服务器造成压垮性的压力。达到这个级别的公司没有几家的。所以，务实主义的小编，对主打商品都是早早的做好了准备，让缓存永不过期。即便某些商品自己发酵成了爆款，也是直接设为永不过期就好了。
大道至简，mutex key互斥锁真心用不上。
结束语
在流行的问题面前一定有流行的解决方案，但有时候，也要根据自己的实际情况酌情处理。大胆设计，说不定你的解决方案就会被流行呢？
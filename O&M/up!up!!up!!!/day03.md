# day03

## 回顾

## Linux

### 用户与用户组

#### 概念

 系统上的每一个进程（运行程序）都作为特定用户来运行

 每个文件都有特定的用户拥有

 对文件和目录的访问受到Linux权限的管理

 与正在运行的进程关联的用户确定该进程可以访问的文件和文件夹

 常用命令：

 ```shell
 useradd
 usermod
 groupadd
 语法：
 useradd [cgd] [选项指定的具体内容] 用户名 
 # c： comment，给用户添加一段注释
 # g： group，可以修改用户的组别
 # d： 代表指定用户的home
 
 userdel 删除用户
 语法：
 userdel 用户名，只是单纯地删除用户
 userdel -r 用户名 删除用户的同时，删除/home/用户
 ```

## nginx

 单节点故障可以由服务器集群来解决

 ![](./imgs\nginx.svg)

 问题：客户端到底要将请求发送给哪台服务器

 假如用户把请求都发送给了服务器1，那么服务器2和3将没有意义

 将ip和端口信息给用户不安全也没意义

 我们想要实现资源优化

 客户端发送的请求可以是动态申请资源的，也有申请静态资源，都是去tomcat中获取

 ![](./imgs\nginx02.svg)

 以上问题可以由nginx来解决

 ![](./imgs\nginx03.svg)

 在搭建集群后，使用nginx

### 概述

 nginx是俄罗斯人研发的

 特定：

 稳定性极强，7*24小时不间断运行

 丰富的配置实例

 占用内存极小

### tomcat集群

 分别复制两套tomcat

 修改tomat/config/server.xml里面的三个端口信息8005， 8009，8080

 分别将http端口改为18080和28080

 分别启动两个tomcat，在bin/startup.bat

 启动成功后，分别访问18080端口和28080端口，如果页面成功显示，则tomcat配置成功

 为了区分，分别在两个tomcat的欢迎页中设置不一样的信息，修改ROOT/index.jsp

 此时用户只能自己手动访问18080来得到18080服务器对应的页面，手动访问28080端口得到28080服务器对应的页面

 这种操作对于用户而言，是不合理的，也是不安全的

 此时需要配置nginx

### nginx配置

 解压nginx，直接双击nginx.exe文件，在任务管理器中出现两个nginx的进程，一个master，一个是worker。如果能够成功启动，用户可以直接访问本机的80端口，默认会打开html中的index.html页面内容

 nginx的配置文件在conf下的nginx.conf文件中

 分别设置要进行集群配置的多个服务器

 ```
     #服务器集群配置
 
     upstream shanxizhiye.com{#服务器集群名称
 
 	server	127.0.0.1:18080 weight=1;#服务器配置，weight是权重，权重越大，分配的概率越大。
 
 	server	127.0.0.1:28080 weight=2;
 
     }
 ```

 当前nginx配置了两个服务器，本机的18080端口对应的服务器和本机的28080端口对应的服务器

 后面还设置了权重，weight，该值越大，代表该节点服务器被调度的机会更大

 最终用户访问nginx的时候，将按照对应的权重动态访问真正的服务器

 在当前配置文件中的server节点中，配置监听端口

 ```
         listen       8888;#侦听端口号
 ```

 意思是将nginx的监听端口设置为了8888，再次访问8888端口时，会走upstream配置的多个服务器，按照对应的权重和轮询策略动态调用对应的服务器进行显示执行。

### session共享

 可以使用redis解决session共享问题

 1. 添加三个jar包放入tomcat的lib目录下

 2. 在每个tomcat的conf下修改context.xml文件，在最后添加如下代码来设置redis的相关信息

    ```xml
        <Valve className="com.orangefunction.tomcat.redissessions.RedisSessionHandlerValve" />
    	<Manager className="com.orangefunction.tomcat.redissessions.RedisSessionManager"
        host="localhost" port="6379" database="0" maxInactiveInterval="60" />
    
    ```

 3. 解压并启动redis服务器

    1. 解压redis的压缩包
    2. 双击运行redis-server


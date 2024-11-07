常驻内存中的程序,且可以提供一些系统或网络功能
## 守护进程
​     linux服务器的主要任务就是为本地或远程用户提供各种服务.通常linux系统上提供服务的程序是由运行在后台的守护进程(Daemon)来执行.一个实际运行中的linux系统一般会有多个这样的程序在运行.这些后台守护进程在开机时就运行了,并起在时刻监听前台的客户服务请求,一旦客户发出了服务请求,守护进程便为他们提供服务
## 特殊守护进程
​     系统初始化进程是一个特殊的守护进程,其PID为1,它是其它所有的守护进程的父进程
也就是说,系统所有的守护京城都是由系统初始化进程管理的(如启动,停止等)
系统上所有的守护进程都是由系统初始化进程管理的
##### 在redhat7之前
systemV
      1. init按照优先级的高低,先后唤醒其他服务
      2. 服务有依赖关系
      3. 多命令协同管理工作管理服务

在sysV中，所有的 **服务脚本都放在/etc/rc.d/init.d/**中，

​可以使用/etc/rc.d/init.d/daemon [start|stop|restart|reload|status] 方式来管理服务，**默认的运行级别在/etc/inittab文件中定义**

当系统以某个运行级别启动时，会运行/etc/rc.d/rcN.d/（其中N范围为06）目录中所有的脚本，而这些脚本的命名都是Knnxxxxx或Snnxxxxx，
​S表示系统启动时调用，
​K表示系统终止时调用，
​nn是0099的数字，数字的大小决定了脚本运行的顺序，
xxxxx为脚本的名称（长度任意），
这些目录里的文件都是指向init.d目录中脚本的软连接，因为各个运行级别的所需的服务可能存在交集，所以这样能节省硬盘使用空间。

在sysV中，服务被分成两大类，一类是可独立运行的服务，另一类是受xinetd管理的服务，而xinetd本身是一个独立运行的服务，用来负责管理一些不常用的服务，当这些服务需要被使用时，由xinetd来唤醒它们，当服务使用完后，这些服务会被结束以减少系统资源的占用。

在sysV中，定义了6个运行级别，分别是：
runlevel0 = 关机
runlevel1 = 单用户模式，仅root
runlevel2 = 带网络的单用户模式
runlevel3 = 多用户模式，字符界面，标准模式
runlevel4 = 保留
runlevel5 = 多用户模式，图形界面，X11(X Window)
>runlevel6 = 重启

`/etc/rc.d/rc.local` 是一个脚本文件，里面定义了用户自定义启动的程序

/etc/rc.d/init.d/：（文件functions定义了很多函数，供给本目录里的脚本调用，而文件README是一个说明文件，主要说明“现在的服务由systemd管理，不再推荐使用这些脚本”，剩下的文件都是对应服务的脚本文件）

##### 在redhat7之后
systemd
      1. 并行启动
      2. 服务依赖性的自我检查
      3. 一个命令管理服务
      4. 向下兼容init服务脚本

 `systemctl command unit`
command 主要有:
    start: 立刻启动后面接的 unit。
    stop:立刻关闭后面接的 unit。
    restart: 立刻关闭后启动后面接的 unit，亦即执行 stop 再 start 的意思
    reload : 不关闭 unit 的情况下，重新载入配置文件，让设置生效。
    enable : 设置下次开机时，后面接的 unit 会被启动。
    disable :设置下次开机时，后面接的 unit 不会被启动。
    status:目前后面接的这个 unit 的状态，会列出有没有正在执行、开机时是否启动等信息is-active:目前有没有正在运行中
    is-enable:开机时有没有默认要启用这个 unit。
    kill : 不要被 kill 这个名字吓着了，它其实是向运行 unit 的进程发送信号show:列出 unit 的配置。
    mask: 注销 unit，注销后你就无法启动这个 unit 了。unmask:取消对 unit 的注销

在systemd中，所有的服务脚本都称为unit，主要分成6类：
`.service    .socket     .target     .path   .snapshot   .timer`
它们都存放在 **/usr/lib/systemd/system/** 目录中。

在systemd中，统一采用systemctl命令来管理所有的服务，主要用法：
`systemctl [start|stop|restart|reload|status|is-active|is-enable|enable|disable|mask|umask] 服务名.类型`
（当服务为servce类型时可以省略类型，如atd.service可以简写为atd，其他的类型不能省略，如talnet.socket）

systemclt set-default|get-default|isolate xxxxx.target 设置默认运行级别|获取当前的默认运行级别|不重启切换当前环境 
（什么是环境呢，target类型的服务都为环境，当运行或切换（需要使用isolate而不能使用start）一个环境时往往会伴随着启动很多其他的服务用以支持这个环境，最常见的环境就是字符界面和图形界面，比如想从现在的字符界面临时切换到图形界面，使用systemctl isolate graphical.tatget）

在systemd中，**运行级别由/etc/systemd/system/default.target** 定义，这个文件本身是一个软连接，如果它指向graphical.targer那么默认的运行级别就是图形界面。

 在systemd中，为了向下兼容也提供了一些target来映射sysV中的运行级别

有一些以.wants结尾的目录，环境的变化往往会伴随着很多其他服务，而wants目录就是当target类型的服务切换之后自动运行的其他服务。

## 系统服务的分类
##### 独立服务
采用systemd管理,服务独立的运行在内存中,服务响应速度快,但占用更多内存

##### 非独立服务
xinetd服务本身独立存在,管理一些服务.用户通过xinetd服务请求其管理的一些服务,然后xinetd返回请求服务的回复给用户相当于代理
​超级守护进程,可以把一些小服务放到xinetd里进行托管.

  

​托管后的好处就是可以使用xinetd强大的参数来控制这些服务,并且增强安全性.

  

​xinetd提供类似inetd + TCP Wrappers的功能,但更加的强大和安全.后面xinetd已经取代了inetd,并且提供了访问控制、加强的日志和资源管理功能.

[inetd]: https://blog.csdn.net/wsx199397/article/details/38270885

[TCP Wrappers]: https://www.cnblogs.com/duzhaoqi/p/7607801.html

TCP wrappers是一个应用层的访问控制程序,其原理就是在服务器向外提供的TCP服务上包裹一层安全监测机制,外来的链接请求首先要通过这层安全监测,获得认证后才能被系统服务接收

**xinetd服务的主配置文件: /etc/xinerd.conf**
**用于存放被托管的服务的目录 : /etc/xinetd.d/**

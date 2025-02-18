# CPU
## 性能检查项
进程总数
看总进程数 - 0业务进程数约为业务进程数

      1. T:说明机器压力大,需要升级配置或通过集群来解决
      2. F:查看任务管理器中的进程,找是否有陌生进程,综合后续指标继续判断

running队列 正在使用cpu的进程
   1. **预警线:核心数*10**
   2. 超过说明CPU资源不足,是否是业务造成的
      1. T: 说明机器压力大,需要升级配置或通过集群来解决

           2. F:找到R队列中的进程,判断是否为恶意程序,是直接kill,并清除相关链接
3. load值 cpu负载值
4. 使用率
      load和使用率是成正比的,负载值高.负载就高
        预警线:load average 接受范围 3-5(单核)(多核*N) 使用率:80%
        重点看cpu使用率,单核100%,多核N*100%

5. 找到高消耗cpu的进程,判断是否为业务进程

   1. T: 说明机器压力大,需要升级配置或通过集群来解决

   2. F:判断是否为恶意程序,是直接kill,并删除相关链接

#### 进程与程序

  1. linux系统中的几乎任何行动都会以进程的形式进行,进程是完成工作的形式,linux内核的基本职责就是为进程提供做事的地方及使用的资源,不让彼此撞车

   1. 浏览网页,浏览器就作为进程运行

   2. 如果键入bash shell的命令行,这个shell就作为单独的进程来执行

  

2. 进程是已启动点可执行程序的运行实例,进程有以下组成部分:

   1. 已分配内存的地址空间

   2. 安全属性,包括所有权凭据及特权

   3. 程序代码的一个或多个执行进程

   4. 进程状态


  程序: 二进制文件,静态

  

  进程: 是程序运行的过程,动态的产生和消亡,有生命周期及运行状态

  

##### 进程的属性

  

1. 进程ID(PID) : 是唯一的值,用来区分进程

2. 父进程(PPID) : 任何一个进程都可以fork子进程, 而自己就是父进程

3. 启动进程的用户ID(UID)和所属组(GID)

4. 进程状态 : 分为运行R ,休眠S,僵尸Z

5. 进程执行的优先级

6. 进程所连接的终端名

7. 进程资源占用: 如CPU,内存等

  

##### 进程的五种状态

  

1. 可运行(R)

   1. 处于可运行状态的进程,一旦有机会,就会访问CPU多个进程可以(而且经常)处于可以运行状态,但是因为在任何给定时间内只有一个进程可以在CPU上运行,所以实际上 这些进程中只有一个在任何给定的实例上运行

2. 自愿睡眠(可中断的)睡眠(s)

   1. 处于自愿睡眠状态的进程西安则处于该状态.通常,这一进程在某事发生之前无事可做

3. 非自愿(不可中断或强制)睡眠(D)

   1. 内核迫使进程进入非自愿睡眠状态.该进程没有选择休眠,它情愿运行一边做完事情.当资源被释放时,内核会唤醒进程并将设置为可运行状态.

4. 停止的(挂起的)进程(T)

   1. 用户又死决定挂起进程,被挂起的进程在被用户重新启动前不会执行任何操作

5. 僵尸进程(Z)

   1. 每个快要终止的进程会经历一个短暂的僵尸状态,然而有时有些进程会一直停留在僵尸状态

###### command ps
静态任务管理器

```
ps - report a snapshot of the current processes
语法
ps [options)]
常用的参数 :
	a: 显示跟当前终端关联的所有进程
	u:基于用户的格式显示
	U: 显示某用户ID所有的进程
	x: 显示所有进程，不以终端机来区分
	A: 显示所有程序
	e:此参数的效果和指定”A”参数相同
	f: 用ASCII字符显示树状结构，表达程序间的相互关系
	o:自定义输出格式
```
输出:

```
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND

root          2  0.0  0.0      0     0 ?        S    11:57   0:00 [kthreadd]
```

详解:

```
USER : 进程拥有者
PID : pid
%CPU : 占用的CPU使用率
%MEM : 占用的内存使用率
VSZ : 占用的虚拟内存大小
RSS : 占用的内存大小
TTY : 终端号码
STAT : 该进程的状态
START : 进程开始时间
TIME : 执行时间
COMMAND : 所执行的指令
```

```
进程状态:
D;		不可中断的禁止
R;		正在执行中
```



###### command top

 动态任务管理器

```
top [选项]
命令选项
	-d number	number代表秒数，表示top命令显示的页面更新一次的间隔 (default=5s)
	-b	以批次的方式执行top
	-n	与-b配合使用，表示需要进行几次top命令的输出结果
	-p	指定特定的pid进程号进行观察
```

**输出**

```
top - 18:15:55 up  6:18,  2 users,  load average: 0.00, 0.01, 0.05
Tasks: 110 total,   1 running, 109 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.2 sy,  0.0 ni, 99.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  2027896 total,  1435364 free,   240620 used,   351912 buff/cache
KiB Swap:  2097148 total,  2097148 free,        0 used.  1619768 avail Mem

PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND

  1 root      20   0  193672   6812   4184 S   0.3  0.3   0:08.93 systemd
  
 14 root      20   0       0      0      0 S   0.3  0.0   0:00.34 ksoftirqd/1
```

**详解:**

**第一行:**

```
top - 18:15:55 up  6:18,  2 users,  load average: 0.00, 0.01, 0.05
```

| 内容                           | 含义                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| 18:15:55                       | 表示当前时间                                                 |
| up  6:18                       | 系统远行时间，格式为时：分                                   |
| 2 users                        | 当前登陆用户数                                               |
| load average: 0.00, 0.01, 0.05 | 系统负载，即任务队列的平均长度。 三个数值分别为 1分钟、5分钟、15分钟前到现在的平均值 |

**第二行:**

```
Tasks: 110 total,   1 running, 109 sleeping,   0 stopped,   0 zombie
```

| 内容             | 含义             |
| ---------------- | ---------------- |
| Tasks: 110 total | 进程总数         |
| 1 running        | 正在运行的进程数 |
| 109 sleeping     | 睡眠的进程数     |
| 0 stopped        | 停止的进程数     |
| 0 zombie         | 僵尸进程数       |

**第三行:**

```
%Cpu(s):  0.0 us,  0.2 sy,  0.0 ni, 99.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
```

|  内容   |                        含义                        |
| :-----: | :------------------------------------------------: |
| 0.0 us  |               用户空间占用CPU百分比                |
| 0.2 sy  |               内核空间占用CPU百分比                |
| 0.0 ni  |   用户进程空间内改变过优先级的进程占用CPU百分比    |
| 99.8 id |                   空闲CPU百分比                    |
| 0.0 wa  |            等待输入输出的CPU时间百分比             |
| 0.0 hi  |       硬中断（Hardware IRQ）占用CPU的百分比        |
| 0.0 si  |    软中断（Software Interrupts）占用CPU的百分比    |
| 0.0 st  | 用于有虚拟cpu的情况，用来指示被虚拟机偷掉的cpu时间 |

**第四行:**

```
KiB Mem :  2027896 total,  1435364 free,   240620 used,   351912 buff/cache
```

| 内容              | 含义             |
| ----------------- | ---------------- |
| 2027896 total     | 物理内存总量     |
| 1435364 free      | 空闲中的内存总量 |
| 240620 used       | 使用中的内存总量 |
| 351912 buff/cache | 缓存的内存量     |

**第五行:**

```
KiB Swap:  2097148 total,  2097148 free,        0 used.  1619768 avail Mem
```

| 内容           | 含义             |
| -------------- | ---------------- |
| 835576k total  | 交换区总量       |
| 0k used        | 使用的交换区总量 |
| 835576k free   | 空闲交换区总量   |
| 111596k cached | 缓冲的交换区总量 |

**进程信息**

| 列名    | 含义                                                         |
| ------- | ------------------------------------------------------------ |
| PID     | 进程id                                                       |
| USER    | 进程所有者的用户名                                           |
| PR      | 优先级                                                       |
| NI      | nice值。负值表示高优先级，正值表示低优先级                   |
| VIRT    | 进程使用的虚拟内存总量，单位kb。VIRT=SWAP+RES                |
| RES     | 进程使用的、未被换出的物理内存大小，单位kb。RES=CODE+DATA    |
| SHR     | 共享内存大小，单位kb                                         |
| S       | 进程状态。D=不可中断的睡眠状态 R=运行 S=睡眠 T=跟踪/停止 Z=僵尸进程 |
| %CPU    | 上次更新到现在的CPU时间占用百分比                            |
| %MEM    | 进程使用的物理内存百分比                                     |
| TIME+   | 进程使用的CPU时间总计，单位1/100秒                           |
| COMMAND | 命令名/命令行                                                |



###### command kill

杀死单个进程

```
kill - terminate a process

语法
kill [选项] [进程号]

选项
	-l 打印信号编号,如果不加信号的编号参数,则使用'-l'参数会列出全部的信号名称
	-a 当处理当前进程时,不限制命令名和进程号的对应关系
	-p 只打印相关进程的进程号,而不发送任何信号
	-s 指定发送信号
	-u 指定用户,kill某个用户的所有进程
```

kill命令工作原理:

```
向linux内核发送一个系统操作信号和某个程序的的进程标识号,然后系统内核就可以对进程标识号指定的进程进行操作
```

信号编号:

```
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
 
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM

16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP

21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ

26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR

31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3

38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8

43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13

48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12

53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7

58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2

63) SIGRTMAX-1  64) SIGRTMAX

```

###### command killall

杀死一个进程树

```
Linux系统中的killall命令用于杀死指定名字的进程( kill processes by name)
命令格式:
killal [选项] [进程名]
命令选项:
	-l			忽略小写
	-g 			杀死进程组而不是进程
	-i 			交互模式，杀死进程前先询问用户
	-1 			列出所有的已知信号名称
	-q 			不输出警告信息
	-s 			发送指定的信号
	-v 			报告信号是否成功发送
	-w 			等待进程死亡
	--help		显示帮助信息
	-version 	显示版本显示
	-u:			杀死指定用户的进程
	-r:			使用正规表达式匹配要杀死的进程名称
	-p:			杀死进程所属的进程组
```

###### command nice

Linux nice命令以更改过的优先序来执行程序，如果未指定程序，则会印出目前的排程优先序，内定的 adjustment 为 10，范围为 -20（最高优先序）到 19（最低优先序）。

使用权限：所有使用者  

语法

```

nice [-n adjustment] [-adjustment] [--adjustment=adjustment] [--help] [--version] [command [arg...]]

```

**参数说明**

- -n adjustment, -adjustment, --adjustment=adjustment 皆为将该原有优先序的增加 adjustment

- --help 显示求助讯息

- --version 显示版本资讯

  

###### renice

Linux renice命令用于重新指定一个或多个行程（Process）的优先序（一个或多个将根据参数而定）。

**注意：**每一个行程（Process）都有一个唯一的（unique）id。

使用权限：所有使用者。

语法

```

renice priority [[-p] pid ...] [[-g] pgrp ...] [[-u] user ...]

```

**参数说明**：

- -p pid 重新指定行程的 id 为 pid 的行程的优先序

- -g pgrp 重新指定行程群组(process group)的 id 为 pgrp 的行程 (一个或多个) 的优先序

- -u user 重新指定行程拥有者为 user 的行程的优先序



# Memory

###### command free

Linux free命令用于显示内存状态。

free指令会显示内存的使用情况，包括实体内存，虚拟的交换文件内存，共享内存区段，以及系统核心使用的缓冲区等。

语法

```
free [-bkmotV][-s <间隔秒数>]
```

**参数说明**：

- -b 　以Byte为单位显示内存使用情况。

- -k 　以KB为单位显示内存使用情况。

- -m 　以MB为单位显示内存使用情况。

- -h 　以合适的单位显示内存使用情况，最大为三位数，自动计算对应的单位值。单位有：

  ```
  B = bytes
  K = kilos
  M = megas
  G = gigas
  T = teras
  ```

- -o 　不显示缓冲区调节列。

- -s<间隔秒数> 　持续观察内存使用状况。

- -t 　显示内存总和列。

- -V 　显示版本信息。
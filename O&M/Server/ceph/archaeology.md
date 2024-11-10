### ceph 集群部署 考古记录

本文使用弃用的 `ceph-deploy`

推荐为奇数节点,本文使用三个节点

防火墙和selinux 全部关闭

#### 进行ntp时间同步

```bash
yum install -y ntp 
ntpdate ntp1.aliyun.com
```

#### 更改节点主机名

```bash
hostnamectl set-hostname ceph1
```

其余节点依次

#### 在hosts 文件中添加主机名与地址映射

```conf
IP ceph1
IP ceph2
IP ceph3
```

使用scp 分发到三个节点

#### 安装ceph-deploy (node1)

```bash
yum install ceph-deploy -y python-setuptools
```

#### 创建ceph集群

```bash
mkcd /etc/ceph
ceph-deploy new ceph1
```

#### 安装cdph 二进制软件包

```bash
ceph-deploy install ceph-node1 ceph-node2 ceph-node3 ## 如果你使用的是本地源 添加--no-adjust-repos 参数
```

分别在三个节点上运行 `ceph -v` 验证安装是否完毕

#### 初始化并安装monitor

```bash
ceph-deploy mon create-initial
```

#### 分发密钥

```bash
ceph-deploy admin ceph{1,2,3}
```

#### 创建 osd

```bash
ceph-deploy osd create --data /dev/sdb1 ceph1
```

三个节点都需要创建

--data /dev/sdb1 指定系统上的可用磁盘分区

ceph1 所处节点

> [!warning]
>
> 如果在非monitor节点上创建osd时遇到了
>
> `[ceph_deploy][ERROR ] RuntimeError: bootstrap-osd keyring not found; run 'gatherkeys'`
>
> 执行一下 `ceph-deploy gatherkeys ceph1` 即可

#### 验证集群状态

```bash
[root@ceph1 ceph]# ceph -s
  cluster:
    id:     c6b31138-f10d-49c0-aa83-e2fe55044cab
    health: HEALTH_WARN
            no active mgr
            mon is allowing insecure global_id reclaim
 
  services:
    mon: 1 daemons, quorum ceph1 (age 47m)
    mgr: no daemons active
    osd: 3 osds: 3 up (since 37s), 3 in (since 37s)
 
  data:
    pools:   0 pools, 0 pgs
    objects: 0 objects, 0 B
    usage:   0 B used, 0 B / 0 B avail
    pgs:     
```

注意:

```bash
    health: HEALTH_WARN
            no active mgr
            mon is allowing insecure
```

ceph-deploy 命令是用于部署和管理 Ceph 集群的工具。在您的命令中，您使用 ceph-deploy mgr create 命令来创建 Ceph 管理器（Manager）ceph-deploy 将在指定的主机上创建一个 Manager 节点，并将其添加到 Ceph 集群中。Manager 节点将负责集群管理任务，例如监控、维护和提供 RESTful API 服务

#### 创建manager

```bash
 ceph-deploy mgr create ceph1 ceph2 ceph3
```

#### 禁用安全模式

```bash
ceph config set mon auth_allow_insecure_global_id_reclaim false
```

#### 验证

```bash
[root@ceph1 ceph]#  ceph -s
  cluster:
    id:     c6b31138-f10d-49c0-aa83-e2fe55044cab
    health: HEALTH_OK
 
  services:
    mon: 1 daemons, quorum ceph1 (age 56m)
    mgr: ceph1(active, since 3m), standbys: ceph2, ceph3
    osd: 3 osds: 3 up (since 9m), 3 in (since 9m)
 
  data:
    pools:   0 pools, 0 pgs
    objects: 0 objects, 0 B
    usage:   3.0 GiB used, 117 GiB / 120 GiB avail
    pgs:     
```


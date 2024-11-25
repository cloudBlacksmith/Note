##### ubuntu 在系统终端可列出本地镜像，但在vscode 中无法列出

code终端信息：

```bash
.
.
.
store:
  configFile: /home/fonzo/.config/containers/storage.conf
  containerStore:
    number: 0
    paused: 0
    running: 0
    stopped: 0
  graphDriverName: overlay
  graphOptions: {}
  graphRoot: /home/fonzo/snap/code/176/.local/share/containers/storage
  graphRootAllocated: 247677284352
  graphRootUsed: 67019624448
  graphStatus:
    Backing Filesystem: extfs
    Native Overlay Diff: "true"
    Supports d_type: "true"
    Supports shifting: "false"
    Supports volatile: "true"
    Using metacopy: "false"
  imageCopyTmpDir: /var/tmp
  imageStore:
    number: 0
  runRoot: /run/user/1000/containers
  transientStore: false
  volumePath: /home/fonzo/snap/code/176/.local/share/containers/storage/volumes
.
.
.
```

系统终端信息：

```bash
.
.
.
store:
  configFile: /home/fonzo/.config/containers/storage.conf
  containerStore:
    number: 0
    paused: 0
    running: 0
    stopped: 0
  graphDriverName: overlay
  graphOptions: {}
  graphRoot: /home/fonzo/.local/share/containers/storage
  graphRootAllocated: 247677284352
  graphRootUsed: 67020087296
  graphStatus:
    Backing Filesystem: extfs
    Native Overlay Diff: "true"
    Supports d_type: "true"
    Supports shifting: "false"
    Supports volatile: "true"
    Using metacopy: "false"
  imageCopyTmpDir: /var/tmp
  imageStore:
    number: 4
  runRoot: /run/user/1000/containers
  transientStore: false
  volumePath: /home/fonzo/.local/share/containers/storage/volumes
.
.
.
```

可以看出：`volumePath` `graphRoot` 两个变量不同；指向了不同的数据存储地

解决方法：

编写配置文件：`~/.config/containers/storage.conf`

```bash
[storage]
driver = "overlay"
graphroot = "/home/fonzo/.local/share/containers/storage"
runroot = "/run/user/1000/containers"
```


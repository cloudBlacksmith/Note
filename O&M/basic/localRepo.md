### 什么是YUM源？YUM的工作原理

在Linux系统中，包管理器（如YUM）用于自动安装、更新、卸载软件包。YUM源是指存放软件包的服务器或本地目录，通过YUM源，用户可以轻松地下载和安装所需的软件包及其依赖关系。



#### YUM源的定义

YUM源实际上是一个包含了RPM包的服务器地址或者本地目录。通过配置不同的YUM源，用户可以在安装软件时自动解决软件的依赖关系。YUM会根据配置文件中的源路径，自动从这些路径中下载和安装依赖包。

#### YUM的工作原理

YUM的工作流程包括两个部分：服务器端和客户端。

1. **服务器端：**
   服务器端存放所有的RPM软件包，并通过分析这些包的依赖关系，生成一个记录依赖关系的文件，存储在服务器的特定目录下。

2. **客户端：**
   当客户端需要安装某个软件包时，它会先下载包含依赖关系信息的文件（这些文件通常是通过WWW或FTP方式访问）。然后，YUM会分析这些文件，确定所需的所有软件包，并一次性下载和安装这些软件包。

### YUM文件

YUM源的配置信息存储在`/etc/yum.repos.d/`目录下。（因为centos官方已经停止维护支持，所以需要将原有的官方镜像源删掉，需要使用第三方源）

```bash
[root@linux-6 ~]# vim /etc/yum.repos.d/

```

查看某个具体的YUM源配置文件：

```bash
[root@linux-6 ~]# vim /etc/yum.repos.d/rhel-source.repo
```

`rhel-source.repo`文件的示例内容：

```ini
[rhel-source-beta]       
name=Red Hat Enterprise Linux $releasever Beta - $basearch - Source
baseurl=ftp://ftp.redhat.com/pub/redhat/linux/beta/$releasever/en/os/SRPMS/  
enabled=0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-beta,file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
```

- `name`：源的名称，用来标识该源。
- `baseurl`：指定软件包的路径，可以是网址或本地路径。
- `enabled`：表示源是否可用，`0`表示不可用，`1`表示可用。
- `gpgcheck`：表示是否进行GPG验证，`1`表示验证，`0`表示不验证。
- `gpgkey`：指定公钥路径，用于验证下载的软件包是否来源安全。

### YUM源的类型

YUM源可以分为两种类型：

1. **本地YUM源**
2. **网络YUM源**

本文主要介绍如何搭建**本地YUM源**。

#### 搭建本地YUM源

1. **准备ISO镜像文件**：首先需要有包含YUM源所需RPM包的ISO镜像文件。可以从网络上下载合适的ISO文件，确保与当前系统版本一致。

2. **创建目录用于挂载ISO文件**：

```bash
[root@linux-6 ~]# mkdir /opt/centos/
```

3. **挂载ISO镜像文件**到`/iso`目录：

```bash
[root@linux-6 ~]# mount -o loop CentOS-7-x86_64-DVD-2009.iso /mnt/
```

**命令解析**：

- `mount`：挂载命令，用于将文件系统挂载到指定的目录。
- `-o loop`：这是一个挂载选项，`loop`表示挂载一个ISO文件。ISO镜像通常是一个单一文件，而不是常规的磁盘设备，所以使用`loop`选项来让操作系统将它视为一个虚拟磁盘。
- `CentOS-7-x86_64-DVD-2009.iso`：ISO文件的路径，可以是绝对路径或相对路径。
- `/mnt/`：挂载点，是一个空目录，所有ISO文件的内容将显示在这个目录下。你也可以指定其他目录作为挂载点。
- 

```
mount: /dev/loop0 is write-protected, mounting read-only
```

**解析**

- 这个警告提示你，ISO 文件的挂载是以只读方式进行的。
- 为什么是只读挂载？
- ISO 镜像通常是只读的，因为它模拟了光盘或安装盘的内容。ISO 文件本身并不允许修改，因此在挂载时会以只读方式挂载。
- **`/dev/loop0`** 是一个虚拟设备，通常用于挂载 ISO 文件。因为 ISO 镜像文件通常是只读的，所以该设备会被标记为只读。
- 这个警告是为了告知用户，尽管可以读取 ISO 中的内容，但不能对其进行任何写入操作。

**挂载后**：

- 挂载成功后，你可以进入`/mnt/`目录，查看和操作ISO镜像中的文件。例如，ISO文件中的安装包或其他资源将出现在该目录中。
  如果使用光盘并且光驱已经挂载，可以使用以下命令：

```bash
[root@linux-6 ~]# mount /dev/cdrom /mnt/
```

**命令解析**：

- `/dev/cdrom`：这是光驱的设备文件，Linux系统通过`/dev/cdrom`设备来识别和访问光驱。通常，`/dev/cdrom`是指向真实光驱设备（如`/dev/sr0`）的符号链接。如果系统中没有该设备，可以尝试查看设备列表，使用其他设备路径，如`/dev/sr0`。
- `/mnt/`：挂载点目录。此目录是你用来挂载光盘的路径，它可以是任何空目录，通常会选择像`/mnt/`、`/media/`或其他指定的目录作为挂载点。

**挂载后**：

- 挂载成功后，你可以访问光盘中的内容。通过进入挂载目录（`/mnt`），你可以查看光盘上的文件。

4. **查看挂载是否成功**：

```bash
[root@worker ~]# df -Th /mnt/
Filesystem     Type     Size  Used Avail Use% Mounted on
/dev/loop0     iso9660  4.4G  4.4G     0 100% /mnt

```

这表示 ISO 文件（`CentOS-7-x86_64-DVD-2009.iso`）已经成功挂载在 `/mnt` 目录上，类型是 `iso9660`，并且已经使用了 100% 的空间。

 **删除原有的YUM源配置文件**

 因为CentOS 官方已经停止了维护，官方的 YUM 源也不再可用，所以需要将原有的yum源配置文件全部删掉，需要通过配置第三方的镜像源或使用本地源来替换现有的 YUM 配置。
 确保清理旧的配置，并添加新的有效源，才能继续使用 YUM 管理软件包。

 ```bash
rm -rf /etc/yum.repos.d/*

 ```

 这条命令删除了 `/etc/yum.repos.d/` 目录下的所有文件。删除所有现有的 YUM 仓库配置文件删除

 **创建YUM源配置文件**

```bash
[root@teacher ~]# vim /etc/yum.repos.d/local.repo
```

更新后的配置如下：

```ini
[centos]
name=centos
baseurl=file:///opt/centos
gpgcheck=0
enabled=1

```



**创建目标目录**：

   ```bash
mkdir /opt/centos
   ```

  创建了一个目录 `/opt/centos`，用来存放从 ISO 镜像中复制的内容。

**复制文件到新目录**：

   ```bash
cp -rvf /mnt/* /opt/centos/
   ```

   你使用 `cp` 命令将 ISO 镜像 `/mnt/` 目录中的所有文件复制到 `/opt/centos/` 目录中。

   - `-r`：递归复制目录及其内容。
   - `-v`：显示详细的复制过程。
   - `-f`：强制复制，覆盖目标目录中的同名文件。



```bash
[root@linux-6 ~]# yum clean all
[root@linux-6 ~]# yum repolist
```


 **清除YUM缓存**

   ```bash
yum clean all
   ```

   这条命令清理了 YUM 缓存，包括软件包列表、元数据缓存、已下载的软件包等。输出显示：

   ```
Cleaning repos: centos
Cleaning up list of fastest mirrors
Other repos take up 261 M of disk space (use --verbose for details)
   ```

   这意味着 YUM 仓库的缓存文件已经被清理，释放了 261MB 的磁盘空间。

 **列出 YUM 仓库**：

   ```bash
yum repolist

   ```

   这条命令显示了当前启用的 YUM 仓库列表。输出显示：

   ```
repo id                                                        repo name                                                     status
centos                                                         centos                                                        4,070
repolist: 4,070
   ```

   这表示 `centos` 仓库已经启用，并且包含 4,070 个软件包。然后可以使用本地YUM源安装软件包了。



## 各种yum源配置方法

### 前置步骤

1. 将准备好的iso镜像挂载到 `/mnt`上 `mount <mirror name> /mnt`
2. 在 `/opt`目录下创建挂载目录 `mkdir /opt/<mirror name>`
3. copy `/mnt` 下的所有文件到 `/opt` 目录下的挂载点 `cp -rfv /mnt /opt/<mirror name>`

#### File Repo

```ini
[<mirror name>]
baseurl=file:///opt/<mirror name>
gpgcheck=0
```

#### Http Repo

##### apache

http服务器配置项:

1. 将`/opt`下创建的目录软连接到`/var/www/html` `ln -s /opt/centos/ /var/www/html/`

```bash
[[<mirror name>]]
baseurl=http://<http server ip>/<mirror name>
gpgcheck=0
```

##### nginx






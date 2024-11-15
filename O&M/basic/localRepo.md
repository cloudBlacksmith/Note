### 前言

在某些时候,我们无法使用网络包源,这时就需要我们自行搭建本地包源;

### 什么是repo文件



##### File Repo

1. 将准备好的iso镜像挂载到 `/mnt`上
2. 在 `/opt`目录下创建挂载目录
3. copy `/mnt` 下的所有文件到 `/opt` 目录下的挂载点
4. 编写`file.repo`文件

```bash
mount <mirror name> /mnt
mkdir /opt/<mirror name>
cp -rfv /mnt /opt/<mirror name>
vim /etc/yum.repos.d/file.repo
```

###### repo 文件内容

```ini
[<mirror name>]
baseurl=file:///opt/<mirror name>
gpgcheck=0
```






## 前言

在某些时候,我们无法使用网络包源,这时就需要我们自行搭建本地包源;

## 什么是repo文件





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






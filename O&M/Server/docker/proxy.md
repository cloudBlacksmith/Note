### 设置系统全局代理
在 `～/.bashrc` 中添加
```bash
export http_proxy="http://your_proxy_address:port"
export https_proxy="http://your_proxy_address:port"
```
#### 虚拟机中的 `docker`
编辑 /etc/environment：

```bash
vim /etc/environment
```
然后添加以下内容：

```bash
http_proxy="http://your_proxy_address:port"
https_proxy="http://your_proxy_address:port"
```
保存后，重启终端或使用 source /etc/environment 使配置生效。

### docker 代理配置文件
```bash
mkdir -p /etc/systemd/system/docker.service.d
vim /etc/systemd/system/docker.service.d/proxy.conf
```
``` conf
[Service]
Environment="HTTP_PROXY=http://your_proxy_address:port"
Environment="HTTPS_PROXY=http://your_proxy_address:port"
Environment="NO_PROXY=localhost,127.0.0.1"
```

### 一键式配置
```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json << 'EOF'
{
  "registry-mirrors": [
    "https://docker.aityp.com",
    "https://docker.m.daocloud.io",
    "https://reg-mirror.qiniu.com",
    "https://k8s.m.daocloud.io",
    "https://elastic.m.daocloud.io",
    "https://gcr.m.daocloud.io",
    "https://ghcr.m.daocloud.io",
    "https://k8s-gcr.m.daocloud.io",
    "https://mcr.m.daocloud.io",
    "https://nvcr.m.daocloud.io",
    "https://quay.m.daocloud.io",
    "https://jujucharms.m.daocloud.io",
    "https://rocks-canonical.m.daocloud.io",
    "https://d3p1s1ji.mirror.aliyuncs.com"
  ],
  "exec-opts": [
    "native.cgroupdriver=systemd"
  ],
  "max-concurrent-downloads": 10,
  "max-concurrent-uploads": 5,
  "log-opts": {
    "max-size": "300m",
    "max-file": "2"
  },
  "live-restore": true,
  "log-level": "debug"
}
EOF

sudo systemctl daemon-reload
sudo systemctl restart docker
```

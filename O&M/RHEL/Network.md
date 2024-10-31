### 引言
在RHEL更行之后,修改 `/etc/sysconfig/network-scripts/` 下的配置文件来进行网络配置的方法已经半作废了;
故本文将介绍RHEL新的网络配置命令: `nmcli`;



### 常见问题
#### 启用网卡以及添加配置文件

网卡未启用且没有配置文件

![test](/annex/Pasted%20image%2020240725105304.png)

尝试启用网卡

![test](/annex/Pasted%20image%2020240725105551.png)

新装的系统我们可能会遇到网卡禁用且使用命令启用时没有反应的情况,这时就有很大的可能是这张网卡没有配置文件;

添加配置文件:
`sudo nmcli connection add type ethernet ifname eth1 con-name eth1`

两种更改配置文件的方法:
1. 使用 `nmtui` 命令
2. `sudo nmcli connection modify <新建的配置文件名> ipv4.addresses <你的IP> ipv4.gateway <你的网关> ipv4.dns 8.8.8.8 ipv4.method manual`

重启 `NetworkManager` 服务
`sudo systemctl restart NetworkManager`

操作命令:
一般情况使用 `nmtui` 即可;

| 命令                                                                                                      | 简介         |
| ------------------------------------------------------------------------------------------------------- | ---------- |
| nmcli connection add type ethernet ifname `NIC name` con-name `NIC conf name`                           | 添加网卡配置文件   |
| nmtui                                                                                                   | 命令行图形化配置命令 |
| nmcli connection delete `NIC conf name`                                                                 | 删除网卡配置文件   |
| nmcli connection show                                                                                   | 查看现有连接配置   |
| nmcli device set `NIC name` managed yes                                                                 | 启用网卡       |
| nmcli connection up `NIC name`                                                                          | 更新连接       |
| nmcli connection modify `NIC` ipv4.addresses `IP` ipv4.gateway `网关` ipv4.dns 8.8.8.8 ipv4.method manual | 修改配置文件     |

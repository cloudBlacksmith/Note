### Lnmp 环境搭建

获取商城项目包

https://www.tp-shop.cn/download/

将解压后的www文件直接替换/var/www 注:解压出来有一个www目录,别用别的替换;

安装rpm包

```bash
yum install php nginx mysql-server  php-mysqli php-gd  -y
```

如果没有则自行构建或启用epel库安装

## http

### 配置文件更改

##### php

更改监听地址

在/path/to/www.conf 中修改参数 `listen = /run/php-fpm/www.sock`
为 `listen = 127.0.0.1:9000`

更改时区

在/path/to/php.ini 中修改参数 `date.timezone = "Asia/Shanghai"`

##### nginx

在/etc/nginx/nginx.conf中的 `http` 模块中添加 `include /etc/nginx/conf.d/*.conf;`

在 `/etc/nginx/conf.d/` 下添加配置文件 `www.conf`

```conf
server 
{
        listen       80;
        server_name  www.shangcheng.com;
        root         /var/www/public;
		
		location ~ \.php$ {
		fastcgi_pass   127.0.0.1:9000;
		fastcgi_index  index.php;
		fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
		include        fastcgi_params;
		}

}
```

赋予权限
```bash
chown nginx:nginx www -R
find /var/www  -type f -name "*.php" -exec chmod 755 {} \;
```


##### 分布式

更改 `fastcgi_pass   127.0.0.1:9000;` 的地址为php解析服务器地址即可
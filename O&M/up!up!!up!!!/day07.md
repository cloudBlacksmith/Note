# day07

## 回顾

## redis

 redis的特点

 redis是一款基于内存的支持多种数据类型的可持久化的键值类型的缓存数据库

 五种（string, list, set, zset, hash）基本的数据类型中数据的插入和获取

 redis的核心配置文件的参数

 ```
 bind 0.0.0.0
 port 6379
 save seconds changes
 slaveof masterip masterport
 loglevel debug|verbos|notice|warning
 
 dbfilename dump.rdb		#数据库的备份文件
 requirepass foobared	#设置密码	
 
 appendfsync always|everysec|no
 ```

 redis主从配置

 redis哨兵

 redis集群

 redis事务

 redis的三大缓存问题以及对应的解决方案

 redis的缓存淘汰策略

## lnmp&lamp

 l： Linux

 m： MySQL

 p： PHP python

 n： nginx

 a： apache

 对比：

 nginx相对于apache

 - 性能稳定，功能丰富，运维简单，处理静态文件速度快且消耗资源极小，支持更大的并发，效率更高
 - 作为负载均衡器，nginx支持python和php，也可支持http代理
 - 作为邮件服务器，nginx是一款优秀的邮件代理服务器
 - 反向代理可以根据url将请求转向于不同用途的集群

## MySQL

### 简介

 MySQL安装

 MySQL使用

 默认数据库

 mysql

 sys

 infomation-schme

 performance_schema

### 创建数据库

 ```mysql
 create database sales_management;		#创建数据库
 
 mysql show create database sales_management;
 +------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
 | Database         | Create Database                                                                                                                            |
 +------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
 | sales_management | CREATE DATABASE `sales_management` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */ /*!80016 DEFAULT ENCRYPTION='N' */ |
 +------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
 1 row in set (0.00 sec)
 #	通过show create database 数据库名可以查看该数据库创建的完整SQL脚本，可以看到编码集
 
 CREATE TABLE users (  
     id INT AUTO_INCREMENT PRIMARY KEY,  		
     username VARCHAR(255) NOT NULL UNIQUE,  
     password VARCHAR(255) NOT NULL,  
     email VARCHAR(255),  
     role ENUM('ADMIN', 'SALESPERSON') NOT NULL DEFAULT 'SALESPERSON'  
 ); 
 
 # users表创建了五个字段，分别是id, username, password, email, role
 # 其中id是int类型，AUTO_INCREMENT是自增，默认从1开始每次加一,PRIMARY KEY主键，唯一和非空
 # username字符串类型，字符串类型有char和varchar，char代表是固定长度，varchar代表可变长度，not null代表非空，unique代表唯一，唯一可以为空
 # password,字符串，非空
 # email 字符串
 # role enum代表枚举类型，意思是用户要么是admin，要么是SALESPERSON，不能为空，DEFAULT代表默认值，默认值是SALESPERSON，如果不加role角色的值或者添加默认值，那么该字段的值就是SALESPERSON
 
   
 -- 插入示例数据  
 INSERT INTO users (username, password, email, role) VALUES  
 ('admin', '$2a$10$BCryptPassword', 'admin@example.com', 'ADMIN'),  
 ('salesperson1', '$2a$10$AnotherBCryptPassword', 'sales1@example.com', 'SALESPERSON');
 ```

## 表结构设计
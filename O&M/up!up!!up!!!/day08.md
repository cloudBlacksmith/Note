# day08

## 回顾

## MySQL

 创建数据库

 创建表

 约束：

 - 实体完整性约束

   - 主键约束：primary key非空唯一

     - 单字段主键：用户表中编号就是单字段主键，角色表中角色编号就是单字段主键
     - 多字段主键：联合主键，下面表用户角色中间表中用户编号和角色编号合起来不重复，构成联合主键。角色权限中间表中角色编号和权限编号合起来不重复，构成联合主键

     用户表users

     | 编号 | 姓名 |
     | ---- | ---- |
     | 1    | 悟空 |
     | 2    | 八戒 |
     | 3    | 沙僧 |
     | 4    | 唐僧 |

     角色表roles

     | 角色编号 | 角色名 |
     | -------- | ------ |
     | 101      | 总经理 |
     | 102      | 经理   |
     | 103      | 员工   |

     权限表 perms

     | 权限编号 | 权限   |
     | -------- | ------ |
     | 011      | select |
     | 012      | insert |
     | 013      | update |
     | 014      | delete |

     用户与角色的中间表user_role

     | 用户id | 角色id |
     | ------ | ------ |
     | 1      | 102    |
     | 1      | 103    |
     | 2      | 103    |
     | 3      | 103    |
     | 4      | 101    |
     | 4      | 102    |
     | 4      | 103    |

     角色权限中间表role_perm

     | 角色编号 | 权限编号 |
     | -------- | -------- |
     | 101      | 011      |
     | 101      | 012      |
     | 101      | 013      |
     | 101      | 014      |
     | 102      | 011      |
     | 102      | 013      |
     | 103      | 011      |

   - 自增约束： auto_incremnt, 默认从1开始，每次加一，不能单独使用，要与主键配合使用

   - 唯一约束：unique，唯一可以有空值，标识表中的一行数据不可以重复，可以为空

 - 域完整性约束

   - 默认值： default，不加该字段或者使用default关键字就使用默认值来填充该字段的值
   - 非空： not null，非空约束，默认不写的话，该字段默认为空

 - 引用完整性约束

   - 语法：constraint 引用名 foreign key （列名） references  被引用的表(引用表的列名)
   - 描述:  foreign key引用外部表的某个列的值，新增数据时，约束次列的值必须是引用表中存在的值
   - 注意：如果存在引用完整性约束，直接删除主表是不被允许的。除非删除与该主表关联的所有的从表中的数据之后才可以删除该主表中的数据
   - 级联操作

 外键分类：

 - 物理外键：强制给表中对应的字段通过foreign key和references设置外键，如果添加了主表中不存在的记录，执行SQL语句将报错
 - 逻辑外键：通过程序逻辑来控制数值的准确性，注意逻辑外键有可能会出现添加错误值的可能性

## 数据库设计

 客户表

 ```mysql
 CREATE TABLE customers (  
     id INT AUTO_INCREMENT PRIMARY KEY,  
     name VARCHAR(255) NOT NULL,  
     contact_email VARCHAR(255),  
     contact_phone VARCHAR(50),  
     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP  
 );  
   
 -- 插入示例数据  
 INSERT INTO customers (name, contact_email, contact_phone) VALUES  
 ('Company A', 'companya@example.com', '123-456-7890'),  
 ('Company B', 'companyb@example.com', '098-765-4321');
 ```

 销售线索表

 ```mysql
 CREATE TABLE leads (  
     id INT AUTO_INCREMENT PRIMARY KEY,  
     customer_id INT,  
     source VARCHAR(255),  
     description TEXT,  
     status ENUM('NEW', 'ASSIGNED', 'CONVERTED', 'CLOSED') DEFAULT 'NEW',  
     assigned_to INT, -- 指向users表的id  
     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
     FOREIGN KEY (customer_id) REFERENCES customers(id), # 外键约束，当前表leads中的customer_id字段要与customer表中的id关联 
     FOREIGN KEY (assigned_to) REFERENCES users(id)  # 外键约束，当前表leads中的asssign_to字段与users表中的id关联
 );  
   
 -- 插入示例数据  
 INSERT INTO leads (customer_id, source, description, assigned_to) VALUES  
 (1, 'Website', 'Inquired about product X', 2),  
 (2, 'Trade Show', 'Expressed interest in service Y', 2);
 ```


### 数据库设计范式

 数据库设计范式（normal forms nf）：是为了设计出合理、高效和规范的数据库结构而指定的一些列规则。

 常见的有第一范式（1NF），第二范式（2NF），第三范式（3NF），巴斯科特范式（BCNF）等

 一般而言，我们的系统几乎都设计到第三范式即可

 数据库设计范式越严格，数据的准确性越高，去冗余性会更高，但是编码会更困难

 第一范式（1NF）

 定义：数据库表中每一列都属不可分割的原子数据项，也就是说每一个字段的值都应该是一个单一的值，而不能是一集合或者数组等复合结构

 | 学生编号 | 学生姓名 | 课程信息 |
 | -------- | -------- | -------- |
 | 1        | 张三     | 语数英   |
 | 2        | 李四     | 数理化   |

 | 学号 | 姓名   | 联系方式          |
 | ---- | ------ | ----------------- |
 | 101  | 王五   | 1333333，山西职业 |
 | 102  | 赵六   | 1888888，陕西西安 |
 | 103  | 鬼脚七 | 1999999，山西职业 |

 这两张表都是不满足第一范式的案例

 第二范式（2NF）

 满足第一范式的基础之上，非主属性完全依赖于主键，不能存在部分依赖，即一张表中的某个非主属性如果只依赖与键的一部分就不符合第二范式

 | 订单编号 | 客户编号 | 客户姓名 | 产品编号 | 产品名称 | 产品价格 | 订单数量 | 订单金额 |
 | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
 | 1001     | 101      | 悟空     | a101     | 手机     | 3999     | 3        | 13997    |
 | 1002     | 102      | 八戒     | a102     | 电脑     | 12999    | 2        | 25998    |

 第三范式（3NF）

 定义：在满足第二范式的基础之上，任何非主属性不依赖于其他非主属性，也就是非主属性之间不能有传递依赖

 | 员工编号 | 员工姓名 | 部门编号 | 部门名称 | 部门经理 | 部门经理电话 |
 | -------- | -------- | -------- | -------- | -------- | ------------ |
 | 10001    | 宝玉     | 101      | cloud    | 贾政     | 110          |
 | 10002    | 黛玉     | 102      | bigdata  | 林某     | 114          |

 主键是员工编号，部门名称依赖于部门编号，而部门经理又依赖于部门经理编号，且部门经理编号通常又和部门编号关联，所以部门经理姓名通过部门编号和部门经理编号对于主键存在传递依赖，不符合第三范式


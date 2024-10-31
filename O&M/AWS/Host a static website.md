#  一, 网站的托管与公共网络访问
## 1. 使用AWS S3存储桶服务托管静态网站
1. 在AWS控制台创建S3存储桶
2. 上传网站文件
3. 在属性标签页中开启静态网站托管并设定网站主页
## 2. 配置可通过公共网络访问S3中的静态网站
1. 在权限页面关闭"**_屏蔽公共访问权限_**"
2. 编写存储桶策略以允许公共网络访问(对象所有权为ACL已禁用)
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "Public access is allowed",
			"Principal": "*",
			"Effect": "Allow",
			"Action": [
				"s3:GetObject"
			],
			"Resource": [
				"arn:aws:s3:::<your S3 ARN>/*"
			]
		}
	]
}
```
# 二, 保障网站数据的安全性
## 1. 启用S3存储桶的版本控制
1. 在属性标签页下启用存储桶版本控制

	**_没了就这样_**
## 2. 配置S3存储桶的跨区复制
1. 在非当前存储桶所在区域创建一个S3存储桶(应启用版本控制,如已经拥有跳过)
2. 创建一个IAM角色以进行复制该IAM角色应有如下权限:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "s3:ListBucket",
                "s3:ReplicateObject",
                "s3:ReplicateDelete",
                "s3:ReplicateTags",
                "s3:Get*"
            ],
            "Resource": [
                "*"
            ],
            "Effect": "Allow"
        }
    ]
}
```
3. 在管理标签页中创建复制规则
4. 在源存储桶选项组中选中 **_应用到存储桶中的所有对象_**
5. 在目标选项组中指定目标存储桶
6. 指定IAM角色为新创建的角色

参考文档:
[向用户授予权限以将角色传递给 AWS 服务 - AWS Identity and Access Management (amazon.com)](https://docs.aws.amazon.com/zh_cn/IAM/latest/UserGuide/id_roles_use_passrole.html)
[为同一账户拥有的源存储桶和目标存储桶配置复制 - Amazon Simple Storage Service](https://docs.aws.amazon.com/zh_cn/AmazonS3/latest/userguide/replication-walkthrough1.html)
## 三, 成本优化
## 1. 配置与使用生命周期策略
### 创建移动旧版本到IA存储桶中策略
1. 选中***应用到存储桶中的所有对象***
2. 选中***在存储类之间移动对象的非当前版本***
3. 填写天数
### 创建删除旧版本策略
1. 选中***应用到存储桶中的所有对象***
2. 选中***永久删除对象的非当前版本***
3. 设定天数
#### 生产环境中不建议***选中应用到存储桶中的所有对象***

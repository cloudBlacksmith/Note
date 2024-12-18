## 配置文件nova.conf

##### 自动删除镜像

检索条件: ^#remove
```conf
# Should unused base images be removed? (boolean value)
remove_unused_base_images=true
```

##### 内存预留
检索条件: host_m
```conf
# Amount of memory in MB to reserve for the host so that it is always available
# to host processes. The host resources usage is reported back to the scheduler
# continuously from nova-compute running on the compute node. To prevent the
# host
# memory from being considered as available, this option is used to reserve
# memory for the host. For more information, refer to the documentation.
# (integer value)
# Minimum value: 0
reserved_host_memory_mb=4096
```

##### 修改连接池大小和最大允许超出的连接数
检索条件: \[database\]
```conf
# 链接池大小
# Maximum number of SQL connections to keep open in a pool. Setting a value of 0
# indicates no limit (integer value)
# Deprecated group;name - DEFAULT;sql_max_pool_size
# Deprecated group;name - [DATABASE]/sql_max_pool_size
max_pool_size=10

# 最大允许链接数
# If set, use this value for max_overflow with SQLAlchemy (integer value)
# Deprecated group;name - DEFAULT;sql_max_overflow
# Deprecated group;name - [DATABASE]/sqlalchemy_max_overflow
max_overflow=10
```

##### 因等待时间过长而导致虚拟机启动超时从而获取不到 IP 地址而报错失败

检索条件: `g_is_`
```conf
# Determine if instance should boot or fail on VIF plugging timeout. For more
# information, refer to the documentation. (boolean value)
vif_plugging_is_fatal=false
```

##### 修改调度器规则采用缓存调度器

检索条件：`ver=f`

```conf
# The class of the driver used by the scheduler. This should be chosen from one
# of the entrypoints under the namespace 'nova.scheduler.driver' of file
# 'setup.cfg'. If nothing is specified in this option, the 'filter_scheduler' is
# used. For more information, refer to the documentation. (string value)
# Deprecated group;name - DEFAULT;scheduler_driver
driver=caching_scheduler
```

##### 可调整实例大小

检索条件: `me_host`

```conf
# Allow destination machine to match source for resize. Useful when
# testing in single-host environments. By default it is not allowed
# to resize to the same host. Setting this option to true will add
# the same host to the destination options. Also set to true
# if you allow the ServerGroupAffinityFilter and need to resize.
#  (boolean value)
allow_resize_to_same_host=true
```


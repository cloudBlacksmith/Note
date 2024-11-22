#### 增加工作进程数量

检索条件: `workers`

```conf
# Number of Glance worker processes to start.
#
# Provide a non-negative integer value to set the number of child
# process workers to service requests. By default, the number of CPUs
# available is set as the value for ``workers`` limited to 8. For
# example if the processor count is 6, 6 workers will be used, if the
# processor count is 24 only 8 workers will be used. The limit will only
# apply to the default value, if 24 workers is configured, 24 is used.
#
# Each worker process is made to listen on the port set in the
# configuration file and contains a greenthread pool of size 1000.
#
# NOTE: Setting the number of workers to zero, triggers the creation
# of a single API process with a greenthread pool of size 1000.
#
# Possible values:
#     * 0
#     * Positive integer value (typically equal to the number of CPUs)
#
# Related options:
#     * None
#
#  (integer value)
# Minimum value: 0
workers = 2
```


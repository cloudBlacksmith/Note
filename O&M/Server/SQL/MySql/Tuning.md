更改配置: `/etc/my.cnf`
## innodb 调优项目
检索条目: `mysql -uroot -e "show variables like 'innodb%';"`
```mysql
+------------------------------------------+------------------------+
| Variable_name                            | Value                  |
+------------------------------------------+------------------------+
| innodb_adaptive_flushing                 | ON                     |
| innodb_adaptive_flushing_lwm             | 10                     |
| innodb_adaptive_hash_index               | ON                     |
| innodb_adaptive_hash_index_parts         | 8                      |
                                  .
                                  .
                                  .
| innodb_version                           | 8.0.36                 |
| innodb_write_io_threads                  | 4                      |
+------------------------------------------+------------------------+
```
根据输出编写配置条目;
例:
```conf
innodb_log_buffer_size=64M # M m 都可以但要注意同意,大写就都大写,小写就都小写
innodb_buffer_pool_size=4G
....
```

## mysql 支持大小写
检索条目: `mysql -uroot -e "show variables like 'low%';"`
```mysql
+------------------------+-------+
| Variable_name          | Value |
+------------------------+-------+
| low_priority_updates   | OFF   |
| lower_case_file_system | OFF   |
| lower_case_table_names | 0     |
+------------------------+-------+
```
`| lower_case_table_names | 0     |`这一条即为是否严格区分大小写;0 为否,1 为是;

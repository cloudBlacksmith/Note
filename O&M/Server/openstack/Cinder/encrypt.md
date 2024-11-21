##### 修改nova配置文件

检索条件: `ed_key`

```conf
# Fixed key returned by key manager, specified in hex. For more information,
# refer to the documentation. (string value)
fixed_key=000000000000000 # 使用有效的十六进制字符串作为密钥,每个字节两个字符
```

##### 修改cinder配置文件

检索条件: `ed_key`

```conf
# Fixed key returned by key manager, specified in hex (string value)
fixed_key = 0000000000000000
```


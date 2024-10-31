### 配置文件 keystone.conf

#### 增加 token 失效时间
检索条件: \[token\]
```conf
# The amount of time that a token should remain valid (in seconds). Drastically
# reducing this value may break "long-running" operations that involve multiple
# services to coordinate together, and will force users to authenticate with
# keystone more frequently. Drastically increasing this value will increase the
# number of tokens that will be simultaneously valid. Keystone tokens are also
# bearer tokens, so a shorter duration will also reduce the potential security
# impact of a compromised token. (integer value)
# Minimum value: 0
# Maximum value: 9223372036854775807
expiration = 7200
```

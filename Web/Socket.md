### 简述:
1. 允许WebServer向client推送数据更新;
2. 低延迟,实时通讯
3. 位于OSI7;

### 什么是WebSocket？

WebSocket是一种计算机通信协议，提供了在客户端（通常是浏览器）和服务器之间建立全双工通信通道的方法。简单来说，WebSocket允许两者之间的持续双向对话，而不是传统的HTTP请求-响应模式。


### WebSocket的工作原理

1. **建立连接**：客户端通过标准的HTTP请求向服务器发起WebSocket连接请求。这就像拨打电话一样。
2. **握手协议**：服务器收到请求后，会与客户端进行握手确认。握手成功后，连接升级为WebSocket。这一步类似于对方接听电话并确认身份。
3. **双向通信**：一旦连接建立，客户端和服务器之间就可以开始双向通信。任何一方都可以随时发送数据，无需等待对方响应。这就像电话两端的双方可以随时说话和听到对方说话。
4. **关闭连接**：任一方都可以随时关闭连接，结束通信。这就像挂断电话一样。

### 技术细节

#### 1. 建立连接

客户端发送一个带有特殊头部的HTTP请求来发起WebSocket连接：

```http
GET /chat HTTP/1.1 
Host: server.example.com 
Upgrade: websocket
Connection: Upgrade 
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ== 
Sec-WebSocket-Version: 13
```

- `Upgrade: websocket` 和 `Connection: Upgrade` 表示请求升级到WebSocket协议。
- `Sec-WebSocket-Key` 是一个随机生成的密钥，用于服务器验证请求的合法性。
- `Sec-WebSocket-Version` 指定WebSocket协议的版本。

服务器收到请求后，会进行验证，并返回如下响应：


```http
HTTP/1.1 101 Switching Protocols 
Upgrade: websocket 
Connection: Upgrade 
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
```


- `101 Switching Protocols` 表示服务器同意协议升级。
- `Sec-WebSocket-Accept` 是服务器生成的一个校验值，验证客户端的请求。


#### 2. 数据帧传输

WebSocket通过数据帧（frames）在客户端和服务器之间传输数据。数据帧有不同类型，用于表示文本数据、二进制数据、控制信息等。每个数据帧都包含帧头和有效负载。

- **帧头**：包含帧的基本信息，如数据类型、长度等。
- **有效负载**：实际传输的数据。

#### 3. 关闭连接

连接关闭时，客户端或服务器发送一个关闭帧，通知对方关闭连接。对方收到关闭帧后，发送一个确认关闭帧，随后连接关闭。

### 优点和应用

WebSocket的主要优点在于它的实时性和效率：

- **低延迟**：由于无需每次都建立新连接，通信延迟大大降低。
- **减少网络开销**：长时间保持一个连接，减少了连接建立和关闭的开销。
- **双向通信**：允许服务器主动向客户端推送消息，适用于需要实时更新的应用场景。

典型应用包括：

- 实时聊天应用（如Slack、微信）
- 实时通知系统（如股票价格更新、体育赛事直播）
- 在线游戏
- 协同编辑工具（如Google Docs）

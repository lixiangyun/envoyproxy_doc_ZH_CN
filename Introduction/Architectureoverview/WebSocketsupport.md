### WebSocket支持
Envoy支持将HTTP/1.1连接升级到WebSocket连接。仅当下游客户端发送正确的升级头并且匹配的HTTP路由显式配置为使用WebSocket（use_websocket）时才允许连接升级。如果一个请求到达启用了WebSocket的路由而没有必要的升级头（upgrade headers），它将被视为任何常规的HTTP/1.1请求。

由于Envoy将WebSocket连接视为纯TCP连接，因此它支持WebSocket协议的所有草案，而与它们的连线格式无关。WebSocket路由不支持某些HTTP请求级别的功能，如重定向，超时，重试，速率限制和阴影（shadowing）。然而，支持前缀重写，显式和自动主机重写，流量转移和分离。

#### 连接语义
即使通过HTTP/1.1连接进行WebSocket升级，WebSockets代理也与普通TCP代理类似，即Envoy不会解释WebSocket帧。下游客户端和/或上游服务器负责正确终止WebSocket连接（例如，通过发送关闭帧）和底层TCP连接。

当连接管理器通过支持WebSocket的路由接收到WebSocket升级请求时，它通过TCP连接将请求转发给上游服务器。特使不知道上游服务器是否拒绝了升级请求。上游服务器负责终止TCP连接，这将导致Envoy终止相应的下游客户端连接。

### 返回
- [架构介绍](../Architectureoverview.md)
- [简介](../../Introduction.md)
- [首页目录](../../README.md)

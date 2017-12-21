### HTTP连接管理

HTTP是现代服务架构的关键组件，Envoy实现了大量HTTP定制的功能。Envoy有一个内置的网络级过滤器，称为HTTP连接管理器。该过滤器将原始字节转换为HTTP级别消息和事件（例如，接收到的头域，接收到的body数据，接收的尾部等）。它还会处理所有HTTP连接和访问记录，请求ID生成和跟踪，请求/响应头域处理，路由表管理和统计等请求。

**HTTP连接管理器配置**

#### HTTP协议

Envoy的HTTP连接管理器支持HTTP/1.1，WebSockets，和HTTP/2，但它不支持SPDY。Envoy的HTTP默认被设计成一个HTTP/2多路复用代理。在其内部用HTTP/2术语用于描述系统组件。例如，HTTP请求和响应发生在流上。编解码器API用于从不同的协议转换，用于stream、请求、响应等的不可知的形式。在HTTP/1.1的情况下，编解码器将协议的serial/pipeline功能转换为类似于HTTP/2到更高层的东西。这意味着大多数代码不需要理解stream是否是HTTP/1.1还是HTTP/2。

#### HTTP头域清理
出于安全原因，HTTP连接管理器将会清理各种头域敏感数据。

#### 路由表配置
每个[HTTP连接管理器](../../Configurationreference/HTTPconnectionmanager.md)过滤器都有一个关联的路由表。路由表可以通过以下两种方式之一来指定：

- 静态配置
- 通过[RDS API动态配置](../../Configurationreference/HTTPconnectionmanager/RoutediscoveryserviceRDS.md)

### 返回
- [架构介绍](../Architectureoverview.md)
- [简介](../../Introduction.md)
- [首页目录](../../README.md)

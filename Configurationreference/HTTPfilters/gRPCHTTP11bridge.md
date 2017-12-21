## gRPC HTTP/1.1 桥接
- [gRPC 架构概述](../../Introduction/Architectureoverview/gRPC.md)
- [v1 API 参考](../../v1APIreference/HTTPfilters/gRPCHTTP11bridge.md)
- [v2 API 参考](../../v2APIreference/Filters/Networkfilters/HTTPconnectionmanager.md)

这是一个简单的过滤器，可以将不支持gRPC响应的HTTP/1.1客户端桥接到兼容的gRPC服务器。它的工作原理如下：

- 发送请求时，过滤器会查看连接是否为HTTP/1.1，请求内容类型为`application/grpc`。
- 如果是这样，当收到响应时，过滤器会缓存并等待预告片，然后检查`grpc-status`代码。如果不为零，则过滤器将HTTP响应代码切换为503。它还将`grpc-status`和`grpc-message`复制到响应头部中，以便客户端可以根据需要查看它们。
- 客户端应该发送翻译成以下伪首标的HTTP/1.1请求：
 - `:method:` POST
 - `:path:` `<gRPC-METHOD-NAME>`
 - `content-type:` application/grpc
- Body应该是序列化的grpc body：
 - 1个字节的零（未压缩）。
 - 网络顺序4个字节的原始消息长度。
 - 序列化的原始消息。
- 因为这个方案必须缓冲响应，以查找`grpc-status`，所以它只能用于一元gRPC API。

更多信息：http://www.grpc.io/docs/guides/wire.html

此过滤器还收集所有gRPC传输请求的统计信息，即使这些请求是通过HTTP/2传输的正常gRPC请求。

### 统计
过滤器收集的统计信息命名空间为`cluster.<route target cluster>.grpc.`。

|	名称	|	类型	|	描述	|
|	 -------------	|	 -------------	|	 -------------	|
|	`<grpc service>.<grpc method>.success`	|	Counter	|	Total successful service/method calls	|
|	`<grpc service>.<grpc method>.failure`	|	Counter	|	Total failed service/method calls	|
|	`<grpc service>.<grpc method>.total`	|	Counter	|	Total service/method calls	|

## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)

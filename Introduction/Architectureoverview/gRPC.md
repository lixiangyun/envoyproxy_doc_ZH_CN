### gRPC

[gRPC](http://www.grpc.io/)是来自Google的RPC框架。它使用协议缓冲区作为基础的序列化/IDL格式。在传输层，它使用HTTP/2进行请求/响应复用。Envoy在传输层和应用层都很好的支持gRPC：

- gRPC使用HTTP/2尾部来传送请求状态。 Envoy是能够正确支持HTTP/2尾部的少数几个HTTP代理之一，因此是少数可以传输gRPC请求和响应的代理之一。
- 某些语言的gRPC运行时相对不成熟。Envoy支持gRPC桥接过滤器，允许gRPC请求通过HTTP/1.1发送给Envoy。然后，Envoy将请求转换为HTTP/2传输到目标服务器。该响应被转换回HTTP/1.1。
- 安装后，网桥过滤器除了统计全局HTTP统计数据之外，还会根据RPC统计信息进行收集。
- gRPC-Web由过滤器支持，它允许gRPC-Web客户端通过HTTP/1.1向Envoy发送请求并代理到gRPC服务器。目前正处于积极的发展阶段，预计将成为[gRPC桥接过滤器](../../Configurationreference/HTTPfilters/gRPCHTTP11bridge.md)的后续产品。
- gRPC-JSON代码转换器由一个过滤器支持，该过滤器允许RESTful JSON API客户端通过HTTP向Envoy发送请求并代理到gRPC服务。

### 返回
- [架构介绍](../Architectureoverview.md)
- [简介](../../Introduction.md)
- [首页目录](../../README.md)

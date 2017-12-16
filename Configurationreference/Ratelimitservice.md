## 速率限制服务

速率限制服务配置指定，Envoy在需要作出全局速率限制决策时，与之交互的全局限速服务。如果没有配置速率限制服务，则会使用“null”服务，如果调用它将总是返回OK。

- [v1 API 参考](../v1APIreference/Ratelimitservice.md)
- [v2 API 参考](../v2APIreference/Bootstrap.md)

### gRPC IDL
速率限制服务支持[/source/common/ratelimit/ratelimit.proto](https://github.com/envoyproxy/envoy/blob/master//source/common/ratelimit/ratelimit.proto)中指定的gRPC IDL。有关更多关于API工作的信息，请参阅IDL文档。请参阅Lyft的[参考实现](https://github.com/lyft/ratelimit)。

## 返回
- [上一级](../Configurationreference.md)
- [首页目录](../README.md)
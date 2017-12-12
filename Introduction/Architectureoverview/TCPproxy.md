### TCP代理

由于Envoy基本上是作为L3/L4服务器编写的，因此很容易实现基本的L3/L4代理。TCP代理过滤器在下游客户端和上游群集之间执行基本的1：1网络连接代理。它本身可以用作替代通道，或与其他过滤器（如MongoDB过滤器或速率限制过滤器）结合使用。

TCP代理过滤器将遵守每个上游集群的全局资源管理器施加的连接限制。TCP代理过滤器检查上游集群的资源管理器是否可以创建连接，而不会超过该集群的最大连接数，如果它不能通过TCP代理进行连接。

TCP代理过滤器[配置参考](../../Configurationreference/Networkfilters/TCPproxy.md)。

### 返回
- [架构介绍](../Architectureoverview.md)
- [简介](../../Introduction.md)
- [首页目录](../../README.md)

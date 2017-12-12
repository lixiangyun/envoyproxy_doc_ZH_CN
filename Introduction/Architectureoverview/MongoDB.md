### MongoDB

Envoy支持具有以下功能的网络级别MongoDB过滤器：

- MongoDB格式的BSON解析器。
- 详细的MongoDB查询/操作统计信息，包括路由集群的计时和`scatter/multi-get`计数。
- 查询记录。
- 每个通过`$comment`查询参数的`callsite`统计信息。
- 故障注入。

MongoDB过滤器是Envoy的可扩展性和核心抽象的一个很好的例子。 在Lyft中，我们在所有应用程序和数据库之间使用这个过滤器。它提供了对应用程序平台，及正在使用的特定MongoDB驱动程序不可知的重要数据源。

MongoDB代理过滤器[配置参考](../../Configurationreference/Networkfilters/Mongoproxy.md)。

### 返回
- [架构介绍](../Architectureoverview.md)
- [简介](../../Introduction.md)
- [首页目录](../../README.md)

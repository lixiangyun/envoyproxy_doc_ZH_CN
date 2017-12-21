## 速率限制

- 速率限制[架构概述](../../Introduction/Architectureoverview/Globalratelimiting.md)
- [v1 API 参考](../../v1APIreference/HTTPfilters/Ratelimit.md)
- [v2 API 参考](../../v2APIreference/Filters/HTTPfilters/Ratelimit.md)

当请求的路由或虚拟主机有一个或多个符合过滤器设置的速率限制配置时，HTTP速率限制过滤器将调用速率限制服务。路由可以选择包含虚拟主机速率限制配置。可配置多个应用于请求。每个配置描述符都会导致被发送到速率限制服务。

如果速率限制服务被调用，并且任何响应超出限制的描述符，都将返回429响应。

### 组成操作
**注意：本文是为v1 API编写的，但这些概念也适用于v2 API。它将在未来版本中使用v2 API重写。**

路由或虚拟主机上的每个速率限制操作都需要一个描述符作为输入。若要创建更复杂的速率限制描述符，可由一组描述符按任意顺序进行组合操作。描述符将按照配置中指定操作的顺序进行填充。

**例1**

例如，要生成以下描述符：
```
("generic_key", "some_value")
("source_cluster", "from_cluster")
```

配置将是：
```
{
  "actions" : [
  {
    "type" : "generic_key",
    "descriptor_value" : "some_value"
  },
  {
    "type" : "source_cluster"
  }
  ]
}
```

**例2**

如果某个操作不附加描述符条目，则不会为该配置生成描述符。

对于以下配置：

```
{
  "actions" : [
  {
    "type" : "generic_key",
    "descriptor_value" : "some_value"
  },
  {
    "type" : "remote_address"
  },
  {
    "type" : "souce_cluster"
  }
  ]
}
```

如果请求没有设置[x-forwarded-for](../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md)，则不会生成描述符。

如果请求设置了[x-forwarded-for](../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md)，则会生成以下描述符：

```
("generic_key", "some_value")
("remote_address", "<trusted address from x-forwarded-for>")
("source_cluster", "from_cluster")
```

### 统计
缓冲区过滤器输出集群中的统计信息以`cluster.<route target cluster>.ratelimit.`为命名空间。429响应被发送到正常的群集[动态HTTP统计](../../Configurationreference/Clustermanager/Statistics.md)。

|	名称	|	类型	|	描述	|
|	 -------------	|	 -------------	|	 -------------	|
|	ok	|	Counter	|	低于限速请求的速率限制服务总数	|
|	error	|	Counter	|	连接速率限制服务的失败总数	|
|	over_limit	|	Counter	|	高于限速请求的速率限制服务总数	|

### 运行时设置
HTTP速率限制过滤器支持以下运行时设置：

- **ratelimit.http_filter_enabled**</br>
将调用速率限制服务的请求的百分比。默认为100。

- **ratelimit.http_filter_enforcing**</br>
将调用速率限制服务并执行决定的请求百分比。默认为100。这可以用来测试完全执行结果之前会发生什么。

- **ratelimit.`<route_key>`.http_filter_enabled**</br>
将调用速率限制配置中指定的给定`route_key`的速率限制服务请求的百分比。默认为100。

## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)

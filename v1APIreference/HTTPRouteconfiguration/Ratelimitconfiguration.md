### 速率限制配置
全局速率限制[架构概述](../../Introduction/Architectureoverview/Globalratelimiting.md)。

```
{
  "stage": "...",
  "disable_key": "...",
  "actions": []
}
```
- **stage**<br />
	(optional, integer) 指在过滤器中设置的阶段。速率限制配置仅适用于具有相同阶段编号的过滤器。默认的阶段编号是0。

    注意：对于阶段编号，过滤器支持0-10的范围。

- **disable_key**<br />
	(optional, string) 在运行时设置的key，用于禁用此速率限制配置。

- **actions**<br />
	(required, array) 将应用于此速率限制配置的操作列表。顺序很重要，因为这些操作是按顺序处理的，描述符是通过在该顺序中附加描述符条目来组成的。如果某个操作无法附加描述符条目，则不会为该配置生成描述符。请参阅相关[操作文档](../../Configurationreference/HTTPfilters/Ratelimit.md)。

### Actions
```
{
  "type": "..."
}
```
- **type**<br />
	(required, string) 要执行的速率限制操作的类型。当前支持的操作类型是`source_cluster`，`destination_cluster`，`request_headers`，`remote_address`，`generic_key`和`header_value_match`。

### Source Cluster
```
{
  "type": "source_cluster"
}
```
以下描述符条目被追加到描述符中：

```
("source_cluster", "<local service cluster>")
```

`<local service cluster>` 是从 `--service-cluster` 选项派生出来的.

### Destination Cluster
```
{
  "type": "destination_cluster"
}
```
以下描述符条目被追加到描述符中：

```
("destination_cluster", "<routed target cluster>")
```

一旦请求与路由表规则匹配，路由的集群就由以下路由表配置设置之一确定：

- [cluster](../../v1APIreference/HTTPRouteconfiguration/Route.md#cluster) 指定要路由到的上游群集。
- [weighted_clusters](../../v1APIreference/HTTPRouteconfiguration/Route.md#weighted_clusters) 从一组具有权重属性的集群组中随机选择一个集群。
- [cluster_header](../../v1APIreference/HTTPRouteconfiguration/Route.md#cluster_header) 指定从请求中的头部获取目标群集。

### Request Headers
```
{
  "type": "request_headers",
  "header_name": "...",
  "descriptor_key" : "..."
}
```
- **header_name**<br />
	(required, string) 要从请求头中查询的该头的名称。头的值用于填充`descriptor_key`的描述符条目的值。

- **descriptor_key**<br />
	(required, string) 在描述符条目中使用的关键。

当一个头包含一个与`header_name`匹配的关键字时，附加下面的描述符条目：

```
("<descriptor_key>", "<header_value_queried_from_header>")
```

### Remote Address
```
{
  "type": "remote_address"
}
```
以下描述符条目被追加到描述符中，并使用来自[x-forwarded-for](../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-forwarded-for)的可信地址填充：

```
("remote_address", "<trusted address from x-forwarded-for>")
```

### Generic Key
```
{
  "type": "generic_key",
  "descriptor_value" : "..."
}
```
- **descriptor_value**<br />
	(required, string) 描述符条目中使用的值。

以下描述符条目被追加到描述符中：

```
("generic_key", "<descriptor_value>")
```

### Header Value Match
```
{
  "type": "header_value_match",
  "descriptor_value" : "...",
  "expect_match" : "...",
  "headers" : []
}
```
- **descriptor_value**<br />
	(required, string) 描述符条目中使用的值。

- **expect_match**<br />
	(optional, boolean) 如果设置为true，则该操作将在请求与头部匹配时附加描述符条目。如果设置为false，则该操作将在请求与头部不匹配时附加描述符条目。默认值是true。

- **[headers](../../v1APIreference/HTTPRouteconfiguration/Route.md#headers)**<br />
	(required, array) 指定速率限制操作应匹配的一组头。将检查请求的头部与配置中所有指定的头部。如果配置中的所有报头都存在于具有相同值的请求中（或者如果没有配置`value`字段，则认为存在），则匹配将发生。

以下描述符条目被追加到描述符中`: .. code-block:: cpp`

```
(“header_match”, “<descriptor_value>”)
```

## 返回
- [上一级](../HTTPRouteconfiguration.md)
- [首页目录](../../README.md)
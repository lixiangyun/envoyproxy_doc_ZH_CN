## Mongo代理

- MongoDB[体系结构概述](../../Introduction/Architectureoverview/MongoDB.md)
- [v1 API 参考](../../v1APIreference/Networkfilters/Mongoproxy.md)
- [v2 API 参考](v2APIreference/Filters/Networkfilters/Mongoproxy.md)

### 故障注入
Mongo代理过滤器支持故障注入。有关如何配置，请参阅v1和v2 API参考。

### 统计
每个配置的MongoDB代理过滤器的统计信息都以`mongo.<stat_prefix>`为根。统计如下：

|	名称	|	类型	|	描述	|
|	 -----------------------	|	 -----------------------	|	 -----------------------	|
|	decoding_error	|	Counter	|	MongoDB协议解码错误的数量	|
|	delay_injected	|	Counter	|	注入被延迟的次数	|
|	op_get_more	|	Counter	|	OP_GET_MORE消息的数量	|
|	op_insert	|	Counter	|	OP_INSERT消息的数量	|
|	op_kill_cursors	|	Counter	|	OP_KILL_CURSORS消息的数量	|
|	op_query	|	Counter	|	OP_QUERY消息的数量	|
|	op_query_tailable_cursor	|	Counter	|	具有可设置cursor标示的OP_QUERY的数量	|
|	op_query_no_cursor_timeout	|	Counter	|	没有设置cursor超时标志的OP_QUERY的数量	|
|	op_query_await_data	|	Counter	|	具有等待数据标志的OP_QUERY的数量	|
|	op_query_exhaust	|	Counter	|	设置耗尽标志的OP_QUERY数量	|
|	op_query_no_max_time	|	Counter	|	没有设置maxTimeMS的查询数量	|
|	op_query_scatter_get	|	Counter	|	分散查询的数量	|
|	op_query_multi_get	|	Counter	|	多重查询的次数	|
|	op_query_active	|	Gauge	|	活跃查询的数量	|
|	op_reply	|	Counter	|	OP_REPLY消息的数量	|
|	op_reply_cursor_not_found	|	Counter	|	未找到设置cursor标志的OP_REPLY数量	|
|	op_reply_query_failure	|	Counter	|	设置了查询失败标志的OP_REPLY数量	|
|	op_reply_valid_cursor	|	Counter	|	具有有效cursor标志的OP_REPLY数量	|
|	cx_destroy_local_with_active_rq	|	Counter	|	使用在本地销毁查询连接总数	|
|	cx_destroy_remote_with_active_rq	|	Counter	|	使用远程销毁查询连接总数	|
|	cx_drain_close	|	Counter	|	在服务退出时，连接被优雅关闭的总数	|

### 分散GETS
Envoy将 `scatter get` 定义为任何不使用`_id`字段作为查询参数的查询。同时在文档以及`_id`的`$query`字段中查找。

### 多重GETS
Envoy将 `multi get` 定义为任何使用`_id`字段作为查询参数的查询，但其中`_id`不是标量值（即文档或数组）。同时在文档以及`_id`的`$query`字段中查找。

### 注释解析
如果一个查询的顶层有一个`$comment`字段（通常添加了一个`$query`字段），Envoy会将其解析为JSON并查找以下结构：

```
{
  "callingFunction": "..."
}
```
**callingFunction**</br>
*(required, string)* 查询功能。如果可用，该函数将用于调用查询点的统计。


### 按命令统计
MongoDB过滤器将收集命名空间为`mongo.<stat_prefix>.cmd.<cmd>.`中命令的统计信息。

|名称    |类型	|描述	|
|------------|------------|----------|
|total	|	Counter	|命令的数量	|
|reply_num_docs	|	Histogram	|应答中的文件数量	|
|reply_size	|	Histogram	|应答的字节数（单位bytes）	|
|reply_time_ms	|	Histogram	|命令的时间（单位毫秒）	|


### 查询统计收集
MongoDB过滤器将收集mongo中查询的统计信息，命名空间`mongo.<stat_prefix>.collection.<collection>.query.`。

|	名称	|	类型	|	描述	|
|	 -----------------------	|	 -----------------------	|	 -----------------------	|
|	total	|	Counter	|	查询数量	|
|	scatter_get	|	Counter	|	分散查询的数量	|
|	multi_get	|	Counter	|	多重查询的数量	|
|	reply_num_docs	|	Histogram	|	应答文件的数量	|
|	reply_size	|	Histogram	|	应答大小（单位：字节）	|
|	reply_time_ms	|	Histogram	|	查询时间（单位：毫秒）	|


### 查询的调用点统计收集
如果应用程序在`$comment`字段中提供调用函数，Envoy将生成每个调用点统计信息。 这些统计信息匹配每个查询信息，匹配的命名空间为
`mongo.<stat_prefix>.collection.<collection>.callsite.<callsite>.query.`。

### 运行时
Mongo代理过滤器支持以下运行配置：

**mongo.connection_logging_enabled**

- 将启用日志记录连接的百分比。默认为100。若允许有百分之百连接的日志记录，但这些连接上的所有消息都将被记录。

**mongo.proxy_enabled**

- 将会启用代理连接的百分比。默认为100。

**mongo.logging_enabled**

- 将被记录的消息的百分比。默认为100，如果小于100，没有查询回复可能会被记录等。

**mongo.mongo.drain_close_enabled**

- 如果服务器逐出关闭，将会关闭的连接百分比，否则将尝试强制关闭。默认为100。

**mongo.fault.fixed_delay.percent**

- 当没有活跃的故障时，正常的MongoDB操作，受到注入故障影响的概率。默认为`percent`配置。

**mongo.fault.fixed_delay.duration_ms**

- 延迟时间以毫秒为单位。默认在使用`duration_ms`配置。

### 访问日志格式
访问日志格式不可定制，并具有以下布局：

```
{"time": "...", "message": "...", "upstream_host": "..."}
```

**time**

- 完整的消息被解析的系统时间，包括毫秒。

**message**

- 消息的文本扩展。消息是否完全展开取决于上下文。有时会提供汇总数据，以避免超大日志。

**upstream_host**

- 代理连接的上游主机。如果与[TCP代理过滤器](TCPproxy.md)一起使用，则会填充此项。


## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)

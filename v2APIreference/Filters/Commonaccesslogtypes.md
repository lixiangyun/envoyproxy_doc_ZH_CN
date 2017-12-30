## 常见访问日志类型

Envoy访问日志记录的是在一段固定的时间内通过Envoy进行入站的交互，典型场景包括单个请求/响应交互（例如HTTP），流（例如通过HTTP 2/gRPC）或连接代理（例如TCP等）。访问日志包含协议指定的`protobuf`消息中定义的字段。

除非另有明确声明，否则所有字段都描述Envoy与连接的客户端之间的下游交互。描述上游交互的字段将在其名称中明确包含上游。

- [filter.accesslog.AccessLog](#filteraccesslogaccesslog)
- [filter.accesslog.AccessLogFilter](#filteraccesslogaccesslogfilter)
- [filter.accesslog.ComparisonFilter](#filteraccesslogcomparisonfilter)
- [filter.accesslog.ComparisonFilter.Op (Enum)](#filteraccesslogcomparisonfilterop-enum)
- [filter.accesslog.StatusCodeFilter](#filteraccesslogstatuscodefilter)
- [filter.accesslog.DurationFilter](#filteraccesslogdurationfilter)
- [filter.accesslog.NotHealthCheckFilter](#filteraccesslognothealthcheckfilter)
- [filter.accesslog.TraceableFilter](#filteraccesslogtraceablefilter)
- [filter.accesslog.RuntimeFilter](#filteraccesslogruntimefilter)
- [filter.accesslog.AndFilter](#filteraccesslogandfilter)
- [filter.accesslog.OrFilter](#filteraccesslogorfilter)
- [filter.accesslog.FileAccessLog](#filteraccesslogfileaccesslog)

### filter.accesslog.AccessLog
[filter.accesslog.AccessLog proto]()

```
{
  "name": "...",
  "filter": "{...}",
  "config": "{...}"
}
```
- **name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 要实例化的访问日志实现的名称。 该名称必须与静态注册的访问日志匹配。当前的内置记录器为：1）“envoy.file_access_log”

- **filter**<br />
	([filter.accesslog.AccessLogFilter](#filteraccesslogaccesslogfilter)) 在写入访问日志时需要使用的过滤器。

- **config**<br />
	([Struct](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#struct)) 依赖实例化访问日志的自定义配置。内置的配置包括：1）`“envoy.file_access_log”：FileAccessLog`

### filter.accesslog.AccessLogFilter
[filter.accesslog.AccessLogFilter proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/accesslog/accesslog.proto#L261)

```
{
  "status_code_filter": "{...}",
  "duration_filter": "{...}",
  "not_health_check_filter": "{...}",
  "traceable_filter": "{...}",
  "runtime_filter": "{...}",
  "and_filter": "{...}",
  "or_filter": "{...}"
}
```

注意：必须正确设置`status_code_filter`，`duration_filter`，`not_health_check_filter`，`traceable_filter`，`runtime_filter`，`and_filter`，`or_filter`其中一个。


- **status_code_filter**<br />
	([filter.accesslog.StatusCodeFilter](#filteraccesslogstatuscodefilter)) 状态码过滤器。

- **duration_filter**<br />
	([filter.accesslog.DurationFilter](#filteraccesslogdurationfilter)) 时长过滤器。

- **not_health_check_filter**<br />
	([filter.accesslog.NotHealthCheckFilter](#filteraccesslognothealthcheckfilter)) 不健康检查过滤器。

- **traceable_filter**<br />
	([filter.accesslog.TraceableFilter](#filteraccesslogtraceablefilter)) 可追踪过滤器。

- **runtime_filter**<br />
	([filter.accesslog.RuntimeFilter](#filteraccesslogruntimefilter)) 运行时过滤器。

- **and_filter**<br />
	([filter.accesslog.AndFilter](#filteraccesslogandfilter)) 于过滤器。

- **or_filter**<br />
	([filter.accesslog.OrFilter](#filteraccesslogorfilter)) 或过滤器。


### filter.accesslog.ComparisonFilter
[filter.accesslog.ComparisonFilter proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/accesslog/accesslog.proto#L289)

整数比较过滤器。

```
{
  "op": "...",
  "value": "{...}"
}
```
- **op**<br />
	([filter.accesslog.ComparisonFilter.Op](#filteraccesslogcomparisonfilterop)) 比较运算符。

- **value**<br />
	([RuntimeUInt32](#runtimeuint32)) 与之比较的值。

### filter.accesslog.ComparisonFilter.Op (Enum)
[filter.accesslog.ComparisonFilter.Op proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/accesslog/accesslog.proto#L290)

- **EQ**<br />
	(DEFAULT) 相等`=`

- **GE**<br />
    大于等于`>=` 

### filter.accesslog.StatusCodeFilter
[filter.accesslog.StatusCodeFilter proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/accesslog/accesslog.proto#L306)

HTTP响应/状态代码的过滤器。

```
{
  "comparison": "{...}"
}
```

- **comparison**<br />
	([filter.accesslog.ComparisonFilter](#filteraccesslogcomparisonfilter), REQUIRED) 对比。

### filter.accesslog.DurationFilter
[filter.accesslog.DurationFilter proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/accesslog/accesslog.proto#L312)

请求持续总时间，以毫秒为单位过滤。

```
{
  "comparison": "{...}"
}
```

- **comparison**<br />
	([filter.accesslog.ComparisonFilter](#), REQUIRED) 对比。

### filter.accesslog.NotHealthCheckFilter
[filter.accesslog.NotHealthCheckFilter proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/accesslog/accesslog.proto#L319)

筛选不健康检查请求。由健康检查过滤器标记。

```
{}
```

### filter.accesslog.TraceableFilter
[filter.accesslog.TraceableFilter proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/accesslog/accesslog.proto#L324)

筛选可追踪的请求。请参阅跟踪概述，以获取有关请求如何可跟踪的更多信息。

```
{}
```

### filter.accesslog.RuntimeFilter
[filter.accesslog.RuntimeFilter proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/accesslog/accesslog.proto#L333)

过滤器用于随机抽样请求。在`x-request-id`头部存在的情况下采样抽取。如果存在`x-request-id`，则过滤器将根据运行时Key/value和从`x-request-id`提取值并在多个主机上持续采样。如果缺失，过滤器将根据运行时Key/value随机抽样。

```
{
  "runtime_key": "..."
}
```

- **runtime_key**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 运行时key，以获取要采样的请求的百分比。此运行时值控制在0-100范围内，默认为0。

### filter.accesslog.AndFilter
[filter.accesslog.AndFilter proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/accesslog/accesslog.proto#L342)

对过滤器中每个过滤器的结果执行逻辑“和”运算。过滤器将按顺序进行评估，如果其中一个返回false，则过滤器立即返回false。

```
{
  "filters": []
}
```

- **filters**<br />
	([filter.accesslog.AccessLogFilter](#filteraccesslogaccesslogfilter), REQUIRED)

### filter.accesslog.OrFilter
[filter.accesslog.OrFilter proto]()

对每个单独的过滤器的结果执行逻辑“或”操作。过滤器将按顺序进行评估，如果其中一个返回true，则过滤器立即返回true。

```
{
  "filters": []
}
```

- **filters**<br />
	([filter.accesslog.AccessLogFilter](#filteraccesslogaccesslogfilter), REQUIRED)

### filter.accesslog.FileAccessLog
[filter.accesslog.FileAccessLog proto]()

将日志条目直接写入文件的`AccessLog`的自定义配置。内置配置为`envoy.file_access_log AccessLog`。

```
{
  "path": "...",
  "format": "..."
}
```

- **path**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 要写入访问日志条目的本地文件的路径。

- **format**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 访问日志的格式。Envoy支持自定义访问日志格式以及默认格式。


## 返回
- [上一级](../Filters.md)
- [首页目录](../../README.md)


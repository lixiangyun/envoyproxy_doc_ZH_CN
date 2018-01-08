## 访问日志

### 配置项

```
{
  "access_log": [
    {
      "path": "...",
      "format": "...",
      "filter": "{...}",
    },
  ]
}
```
- **path**<br />
	(required, string) 写入访问日志的路径。

- **format**<br />
	(optional, string) 访问日志格式。Envoy支持[自定义访问日志格式](../Configurationreference/Accesslogging.md)以及[默认格式](../Configurationreference/Accesslogging.md)。

- **filter**<br />
	(optional, object) 用于明确是否需要写入的访问日志过滤器。

### 过滤器
Envoy支持以下访问日志过滤器：

- [Status code](#status-code)
- [Duration](#duration)
- [Not health check](#not-health-check)
- [Traceable](#traceable)
- [Runtime](#runtime)
- [And](#and)
- [Or](#or)

### Status code
```
{
  "filter": {
    "type": "status_code",
    "op": "...",
    "value": "...",
    "runtime_key": "..."
  }
}
```
基于HTTP响应/状态代码的过滤器。

- **op**<br />
	(required, string) 比较运算符。目前仅支持`>=`和`=`运算符。

- **value**<br />
	(required, integer) 如果运行时值不可用，则使用默认值进行比较。

- **runtime_key**<br />
	(optional, string) 运行时的key，用于获取比较值。如果定义，则使用此值。

### Duration
```
{
  "filter": {
    "type": "duration",
    "op": "..",
    "value": "...",
    "runtime_key": "..."
  }
}
```
请求持续的总时间过滤器，以毫秒为单位。

- **op**<br />
	(required, string) 比较运算符。目前仅支持`>=`和`=`运算符。

- **value**<br />
	(required, integer) 如果运行时值不可用，则使用默认值进行比较。

- **runtime_key**<br />
	(optional, string) 运行时的key，用于获取比较值。如果定义，则使用此值。

### Not health check
```
{
  "filter": {
    "type": "not_healthcheck"
  }
}
```
筛选健康检查失败的请求。健康检查的请求由健康检查过滤器进行标记。

### Traceable
```
{
  "filter": {
    "type": "traceable_request"
  }
}
```
筛选可跟踪的请求。请参阅[跟踪概述](../Introduction/Architectureoverview/Tracing.md)，以获取有关请求如何使能跟踪的详细信息。

### Runtime
```
{
  "filter": {
    "type": "runtime",
    "key" : "..."
  }
}
```
使用随机采样请求。在`x-request-id`头存在的情况下进行采样。如果存在`x-request-id`，则过滤器将根据运行时键值和从`x-request-id`提取的值在多个主机上持续采样。如果缺失，过滤器将根据运行时键值随机采样。

- **key**<br />
	(required, string) 通过运行时key获取要采样的请求的百分比。此运行时值控制在`0-100`范围内，默认为0。

### And
```
{
  "filter": {
    "type": "logical_and",
    "filters": []
  }
}
```
对过滤器中每个过滤器的结果执行逻辑"和"运算。过滤器将按顺序进行评估，如果其中一个返回false，则过滤器立即返回false。

### Or
```
{
  "filter": {
    "type": "logical_or",
    "filters": []
  }
}
```
对每个单独的过滤器的结果执行逻辑"或"操作。过滤器将按顺序进行评估，如果其中一个返回true，则过滤器会立即返回true。


## 返回
- [上一级](../v1APIreference.md)
- [首页目录](../README.md)


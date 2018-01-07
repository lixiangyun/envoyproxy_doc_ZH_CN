### 故障注入
故障注入[配置概述](../../Configurationreference/HTTPfilters/FaultInjection.md)。

### Configuration
```
{
  "name" : "fault",
  "config" : {
    "abort" : "{...}",
    "delay" : "{...}",
    "upstream_cluster" : "...",
    "headers" : [],
    "downstream_nodes" : []
  }
}
```
- **abort**<br />
	(sometimes required, object) 如果指定，过滤器将根据对象中的值中止请求。必须指定至少`abort`或`delay`。

- **delay**<br />
	(sometimes required, object) 如果指定，过滤器将根据对象中的值注入延迟。必须指定至少`abort`或`delay`。

- **upstream_cluster**<br />
	(optional, string) 指定过滤器应匹配的（目标）上游群集的名称。故障注入将限于绑定到特定上游群集的请求。

- **headers**<br />
	(optional, array) 指定过滤器应匹配的一组标题。故障注入过滤器可以选择一组头相匹配的请求，应用该故障注入。实际故障注入的机会进一步取决于[abort_percent](#abort_percent)和[fixed_delay_percent](#fixed_delay_percent)参数的值。过滤器会根据配置中的所指定头部，来检查请求的头。如果配置中的所有头都存在于具有相同值的请求中（或者如果没有配置`value`字段，则认为存在），则匹配将发生。

- **downstream_nodes**<br />
	(optional, array) 针对指定的下游主机列表，注入故障。如果未设置此设置，则会为所有下游节点注入故障。下游节点名称取自HTTP `x-envoy-downstream-service-node头，并与`downstream_nodes`列表进行比较。

### Abort
```
{
  "abort_percent" : "...",
  "http_status" : "..."
}
```
- **abort_percent**<br />
	(required, integer) 使用指定的`http_status`代码中止请求的百分比。有效值范围从0到100。

- **http_status**<br />
	(required, integer) 将被用作中止请求的响应码，即HTTP状态代码。

### Delay
```
{
  "type" : "...",
  "fixed_delay_percent" : "...",
  "fixed_duration_ms" : "..."
}
```
- **type**<br />
	(required, string) 指定被注入的延迟类型。目前只支持`fixed`延迟类型（`step function`）。

- **fixed_delay_percent**<br />
	(required, integer) 将在`fixed_duration_ms`指定的时间内延迟的请求的百分比。有效值范围从0到100。

- **fixed_duration_ms**<br />
	(required, integer) 延迟时间，以毫秒为单位。必须大于0。

## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)
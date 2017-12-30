## 故障注入

故障注入[配置参考](../../../Configurationreference/HTTPfilters/FaultInjection.md)。

### filter.http.FaultAbort
[filter.http.FaultAbort proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/http/fault.proto#L12)

```
{
  "percent": "...",
  "http_status": "..."
}
```

- **percent**<br />
	([uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 一个介于0到100之间的整数，表示请求/操作/连接通过下面的状态码中止的百分比。 

- **http_status**<br />
	([uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 用于中止HTTP请求的HTTP状态码。

    注意：必须设置正确的http_status。

### filter.http.HTTPFault
[filter.http.HTTPFault proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/http/fault.proto#L25)

```
{
  "delay": "{...}",
  "abort": "{...}",
  "upstream_cluster": "...",
  "headers": [],
  "downstream_nodes": []
}
```
- **delay**<br />
	([filter.FaultDelay](#filterfaultdelay)) 如果指定，过滤器将根据配置的值注入延迟。必须指定中止或延迟。

- **abort**<br />
	([filter.http.FaultAbort](#filterhttpfaultabort)) 如果指定，过滤器将根据配置的值中止请求。必须指定中止或延迟。

- **upstream_cluster**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指定过滤器所匹配的（目标）上游群集的名称。故障注入将仅限于特定上游群集的请求。

- **headers**<br />
	([HeaderMatcher](#headermatcher)) 指定过滤器应匹配的一组头部键值。故障注入过滤器支持根据配置中指定的一组头部匹配请求，来应用故障注入。实际故障注入的概率依赖与百分比字段的值。过滤器会根据配置中的所指定头部来检查请求。如果配置中的所有头部名称以及相应的值都存在于请求中（若没有配置头部的值，则也认为存在），则匹配将发生。

- **downstream_nodes**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 针对指定的下游主机列表进行注入故障。如果未设置此设置，则会为所有下游节点注入故障。下游节点名称取自HTTP的`x-envoy-downstream-service-node`头，并与下游节点列表进行比较。


## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../../README.md)


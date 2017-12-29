## 常见故障注入类型

### Common fault injection types
### filter.FaultDelay
[filter.FaultDelay proto]()

Delay specification is used to inject latency into the HTTP/gRPC/Mongo/Redis operation or delay proxying of TCP connections.

```
{
  "type": "...",
  "percent": "...",
  "fixed_delay": "{...}"
}
```
- **type**<br />
	([filter.FaultDelay.FaultDelayType](#)) Delay type to use (fixed|exponential|..). Currently, only fixed delay (step function) is supported.

- **percent**<br />
	([uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar)) An integer between 0-100 indicating the percentage of operations/connection requests on which the delay will be injected.

- **fixed_delay**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) Add a fixed delay before forwarding the operation upstream. See https://developers.google.com/protocol-buffers/docs/proto3#json for the JSON/YAML Duration mapping. For HTTP/Mongo/Redis, the specified delay will be injected before a new request/operation. For TCP connections, the proxying of the connection upstream will be delayed for the specified period. This is required if type is FIXED.


Precisely one of fixed_delay must be set.

### filter.FaultDelay.FaultDelayType(Enum)
[filter.FaultDelay.FaultDelayType proto]()

### FIXED
	(DEFAULT) ?Fixed delay (step function).




## 返回
- [上一级](../Filters.md)
- [首页目录](../../README.md)
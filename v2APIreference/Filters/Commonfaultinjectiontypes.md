## 常见故障注入类型


### filter.FaultDelay
[filter.FaultDelay proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/fault.proto#L13)

延迟故障注入适用于在HTTP/gRPC/Mongo/Redis的操作中，提供延迟或延迟TCP连接的代理。

```
{
  "type": "...",
  "percent": "...",
  "fixed_delay": "{...}"
}
```

- **type**<br />
	([filter.FaultDelay.FaultDelayType](#filterfaultdelayfaultdelaytype-enum)) 要使用的延迟类型（固定|指数|..）。目前只支持固定延时（`step function`）。

- **percent**<br />
	([uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 0到100之间的整数，表示将被注入延迟的操作/连接请求的百分比。

- **fixed_delay**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 在向上游转发操作之前添加固定延迟。有关JSON/YAML持续时间映射，请参阅[连接](https://developers.google.com/protocol-buffers/docs/proto3#json)。对于HTTP/Mongo/Redis，指定的延迟将在新的请求/操作之前被注入。对于TCP连接，连接上游的代理将在指定的时间段延迟。如果type是FIXED，则这是必需的。

    注意：fixed_delay必须被设置。

### filter.FaultDelay.FaultDelayType (Enum)
[filter.FaultDelay.FaultDelayType proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/fault.proto#L14)

- **FIXED**<br />
	(DEFAULT) 固定延迟（步进功能）。


## 返回
- [上一级](../Filters.md)
- [首页目录](../../README.md)
## 健康检查

### Health check
Health checking architecture overview.
If health checking is configured for a cluster, additional statistics are emitted. They are documented here.
### HealthCheck
[HealthCheck proto]()

```
{
  "timeout": "{...}",
  "interval": "{...}",
  "interval_jitter": "{...}",
  "unhealthy_threshold": "{...}",
  "healthy_threshold": "{...}",
  "reuse_connection": "{...}",
  "http_health_check": "{...}",
  "tcp_health_check": "{...}",
  "redis_health_check": "{...}"
}
```
- **timeout**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) The time to wait for a health check response. If the timeout is reached the health check attempt will be considered a failure.

- **interval**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) The interval between health checks.

- **interval_jitter**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) An optional jitter amount in millseconds. If specified, during every internal Envoy will add 0 to interval_jitter to the wait time.

- **unhealthy_threshold**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The number of unhealthy health checks required before a host is marked unhealthy. Note that for http health checking if a host responds with 503 this threshold is ignored and the host is considered unhealthy immediately.

- **healthy_threshold**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The number of healthy health checks required before a host is marked healthy. Note that during startup, only a single successful health check is required to mark a host healthy.

- **reuse_connection**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) Reuse health check connection between health checks. Default is true.

- **http_health_check**</br>
	([HealthCheck.HttpHealthCheck](#)) HTTP health check.


Precisely one of http_health_check, tcp_health_check, redis_health_check must be set.

- **tcp_health_check**</br>
	([HealthCheck.TcpHealthCheck](#)) TCP health check.


Precisely one of http_health_check, tcp_health_check, redis_health_check must be set.

- **redis_health_check**</br>
	([HealthCheck.RedisHealthCheck](#)) Redis health check.


Precisely one of http_health_check, tcp_health_check, redis_health_check must be set.

### HealthCheck.Payload
[HealthCheck.Payload proto]()

Describes the encoding of the payload bytes in the payload.

```
{
  "text": "..."
}
```
- **text**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Hex encoded payload. E.g., “000000FF”.


Precisely one of text must be set.

### HealthCheck.HttpHealthCheck
[HealthCheck.HttpHealthCheck proto]()

```
{
  "path": "...",
  "service_name": "..."
}
```
- **path**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Specifies the HTTP path that will be requested during health checking. For example /healthcheck.

- **service_name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) An optional service name parameter which is used to validate the identity of the health checked cluster. See the architecture overview for more information.

### HealthCheck.TcpHealthCheck
[HealthCheck.TcpHealthCheck proto]()

```
{
  "send": "{...}",
  "receive": []
}
```
- **send**</br>
	([HealthCheck.Payload](#)) Empty payloads imply a connect-only health check.

- **receive**</br>
	([HealthCheck.Payload](#)) When checking the response, “fuzzy” matching is performed such that each binary block must be found, and in the order specified, but not necessarily contiguous.

### HealthCheck.RedisHealthCheck
[HealthCheck.RedisHealthCheck proto]()

```
{}
```

## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)
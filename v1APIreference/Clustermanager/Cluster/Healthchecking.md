### Health checking
Health checking architecture overview.
If health checking is configured for a cluster, additional statistics are emitted. They are documented here.
```
{
  "type": "...",
  "timeout_ms": "...",
  "interval_ms": "...",
  "unhealthy_threshold": "...",
  "healthy_threshold": "...",
  "path": "...",
  "send": [],
  "receive": [],
  "interval_jitter_ms": "...",
  "service_name": "..."
}
```
- **type**<br />
	([required](#), string) The type of health checking to perform. Currently supported types are http, redis, and tcp. See the architecture overview for more information.

- **timeout_ms**<br />
	([required](#), integer) The time in milliseconds to wait for a health check response. If the timeout is reached the health check attempt will be considered a failure.

- **interval_ms**<br />
	([required](#), integer) The interval between health checks in milliseconds.

- **unhealthy_threshold**<br />
	([required](#), integer) The number of unhealthy health checks required before a host is marked unhealthy. Note that for http health checking if a host responds with 503 this threshold is ignored and the host is considered unhealthy immediately.

- **healthy_threshold**<br />
	([required](#), integer) The number of healthy health checks required before a host is marked healthy. Note that during startup, only a single successful health check is required to mark a host healthy.

- **path**<br />
	([sometimes required](#), string) This parameter is required if the type is http. It species the HTTP path that will be requested during health checking. For example /healthcheck.

- **send**<br />
	([sometimes required](#), array) This parameter is required if the type is tcp. It specifies the bytes to send for a health check request. It is an array of hex byte strings specified as in the following example:


```
[
  {"binary": "01"},
  {"binary": "000000FF"}
]
```
The array is allowed to be empty in the case of “connect only” health checking.

- **receive**<br />
	([sometimes required](#), array) This parameter is required if the type is tcp. It specified the bytes that are expected in a successful health check response. It is an array of hex byte strings specified similarly to the send parameter. The array is allowed to be empty in the case of “connect only” health checking.

- **interval_jitter_ms**<br />
	([optional](#), integer) An optional jitter amount in millseconds. If specified, during every internal Envoy will add 0 to interval_jitter_ms milliseconds to the wait time.

- **service_name**<br />
	([optional](#), string) An optional service name parameter which is used to validate the identity of the health checked cluster. See the architecture overview for more information.


## 返回
- [上一级](../Cluster.md)
- [首页目录](../../../README.md)
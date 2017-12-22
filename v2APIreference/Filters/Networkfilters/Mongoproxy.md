## Mongo代理

### Mongo proxy
MongoDB configuration overview.

- **filter.network.MongoProxy**</br>
[filter.network.MongoProxy proto]()

```
{
  "stat_prefix": "...",
  "access_log": "...",
  "delay": "{...}"
}
```
- **stat_prefix**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The human readable prefix to use when emitting statistics.

- **access_log**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) The optional path to use for writing Mongo access logs. If not access log path is specified no access logs will be written. Note that access log is also gated runtime.

- **delay**</br>
	([filter.FaultDelay](#)) Inject a fixed delay before proxying a Mongo operation. Delays are applied to the following MongoDB operations: Query, Insert, GetMore, and KillCursors. Once an active delay is in progress, all incoming data up until the timer event fires will be a part of the delay.

### Next  Previous



## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../../README.md)
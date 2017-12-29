### Mongo proxy
MongoDB configuration overview.

```
{
  "name": "mongo_proxy",
  "config": {
    "stat_prefix": "...",
    "access_log": "...",
    "fault": {}
  }
}
```
- **stat_prefix**<br />
	([required](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) The prefix to use when emitting statistics.

- **access_log**<br />
	([optional](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) The optional path to use for writing Mongo access logs. If not access log path is specified no access logs will be written. Note that access log is also gated by runtime.

- **fault**<br />
	([optional](#), object) If specified, the filter will inject faults based on the values in the object.

### Fault configuration
Configuration for MongoDB fixed duration delays. Delays are applied to the following MongoDB operations: Query, Insert, GetMore, and KillCursors. Once an active delay is in progress, all incoming data up until the timer event fires will be a part of the delay.

```
{
  "fixed_delay": {
    "percent": "...",
    "duration_ms": "..."
  }
}
```
- **percent**<br />
	([required](#), integer) Probability of an eligible MongoDB operation to be affected by the injected fault when there is no active fault. Valid values are integers in a range of [0, 100].

- **duration_ms**<br />
	([required](#), integer) Non-negative delay duration in milliseconds.



## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)


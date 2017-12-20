## 路由

### Router
Router configuration overview.

- **filter.http.Router**</br>
[filter.http.Router proto]()

```
{
  "dynamic_stats": "{...}",
  "start_child_span": "...",
  "upstream_log": []
}
```
- **dynamic_stats**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) Whether the router generates dynamic cluster statistics. Defaults to true. Can be disabled in high performance scenarios.

- **start_child_span**</br>
	([bool](#)) Whether to start a child span for egress routed calls. This can be useful in scenarios where other filters (auth, ratelimit, etc.) make outbound calls and have child spans rooted at the same ingress parent. Defaults to false.

- **upstream_log**</br>
	([filter.accesslog.AccessLog](#)) Configuration for HTTP upstream logs emitted by the router. Upstream logs are configured in the same way as access logs, but each log entry represents an upstream request. Presuming retries are configured, multiple upstream requests may be made for each downstream (inbound) request.



## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../../README.md)
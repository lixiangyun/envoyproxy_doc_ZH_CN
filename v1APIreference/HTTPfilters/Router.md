## Router
Router configuration overview.

```
{
  "name": "router",
  "config": {
    "dynamic_stats": "...",
    "start_child_span": "..."
  }
}
```
- **dynamic_stats**</br>
	([optional](#), boolean) Whether the router generates dynamic cluster statistics. Defaults to true. Can be disabled in high performance scenarios.

- **start_child_span**</br>
	([optional](#), boolean) Whether to start a child tracing span for egress routed calls. This can be useful in scenarios where other filters (auth, ratelimit, etc.) make outbound calls and have child spans rooted at the same ingress parent. Defaults to false.

### Next  Previous



## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)
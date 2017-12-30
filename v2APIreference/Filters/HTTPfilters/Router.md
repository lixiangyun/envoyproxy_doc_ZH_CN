## 路由

路由[配置参考](../../../Configurationreference/HTTPfilters/Router.md)

### filter.http.Router
[filter.http.Router proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/http/router.proto#L11)

```
{
  "dynamic_stats": "{...}",
  "start_child_span": "...",
  "upstream_log": []
}
```

- **dynamic_stats**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 路由器是否生成动态集群统计信息。默认为true。可以在高性能场景下禁用。

- **start_child_span**<br />
	([bool](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 是否为出口路由请求启动子span。在其他过滤器（`auth`，`ratelimit`等）进行出站请求并且子span的根位于同一入口父span的情况下，这可能很有用。默认为false。

- **upstream_log**<br />
	([filter.accesslog.AccessLog](../../../v2APIreference/Filters/Commonaccesslogtypes.md)) 配置由路由器发出的HTTP上游访问日志。上游日志的配置方式与访问日志相同，但是每个日志条目代表上游请求。若配置了重试，则路由器可以为每个下游（入站）请求做出多个上游请求。


## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../../README.md)


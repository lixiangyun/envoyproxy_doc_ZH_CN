## 路由
路由[配置概述](../../Configurationreference/HTTPfilters/Router.md)

```
{
  "name": "router",
  "config": {
    "dynamic_stats": "...",
    "start_child_span": "..."
  }
}
```
- **dynamic_stats**<br />
	(optional, boolean) 是否为[动态集群](../../Configurationreference/Clustermanager/Statistics.md)
生成统计信息。默认为true。可以在高性能场景下禁用。

- **start_child_span**<br />
	(optional, boolean) 是否为出站路由的调用启动子[跟踪](../../Introduction/Architectureoverview/Tracing.md)
，在其他过滤器（auth，ratelimit等）进行出站调用，并且子父span位于同一入口的情况下，这可能很有用。默认为false。

## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)

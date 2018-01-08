### 速率限制服务
速率限制[配置概述](../Configurationreference/Ratelimitservice.md)。

```
{
  "type": "grpc_service",
  "config": {
    "cluster_name": "..."
  }
}
```
- **type**<br />
	(required, string) 指定要调用的速率限制服务的类型。目前唯一支持的选项是`grpc_service`，它指定了Lyft的全局速率限制服务和关联的IDL。

- **config**<br />
	(required, object) 特定速率限制服务类型的配置。

- **cluster_name**<br />
	(required, string) 指定所承载的速率限制服务的群集名称。当需要进行速率限制服务请求时，客户端将连接到该群集。

## 返回
- [上一级](../v1APIreference.md)
- [首页目录](../README.md)


## 跟踪
跟踪配置指定了Envoy使用的HTTP跟踪器的全局设置。在服务的顶层配置上定义。未来，Envoy可能会支持其他跟踪器，但现在HTTP跟踪器是唯一支持的跟踪器。

```
{
  "http": {
    "driver": "{...}"
  }
}
```

- **http**<br />
	(optional, object) 提供HTTP跟踪器的配置。

- **driver**<br />
	(optional, object) 提供处理跟踪和创建span的驱动程序。

目前支持LightStep和Zipkin驱动程序。

### LightStep跟踪驱动
```
{
  "type": "lightstep",
  "config": {
    "access_token_file": "...",
    "collector_cluster": "..."
  }
}
```

- **access_token_file**<br />
	(required, string) 包含访问到LightStep API的令牌文件。

- **collector_cluster**<br />
	(required, string) 承载LightStep集群的集群管理器。

### Zipkin跟踪驱动
```
{
  "type": "zipkin",
  "config": {
    "collector_cluster": "...",
    "collector_endpoint": "..."
  }
}
```

- **collector_cluster**<br />
	(required, string) 承载Zipkin集群的群集管理器。请注意，Zipkin集群必须在集群管理器定义相应的集群配置。

- **collector_endpoint**<br />
	(optional, string) 将span发送的Zipkin服务的API端口。当安装标准的Zipkin时，API端口通常是/api/v1/span，同时也是默认值。


## 返回
- [上一级](../v1APIreference.md)
- [首页目录](../README.md)


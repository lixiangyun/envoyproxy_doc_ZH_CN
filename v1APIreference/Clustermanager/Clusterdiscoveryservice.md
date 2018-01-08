### 集群发现服务(CDS)
```
{
  "cluster": "{...}",
  "refresh_delay_ms": "..."
}
```
- **[clusters](../../v1APIreference/Clustermanager/Cluster.md)**<br />
	(required, object) 承载群集发现服务的上游群集的定义。群集必须实现并运行CDS HTTP API的REST服务。

- **refresh_delay_ms**<br />
	(optional, integer) 每次从CDS API刷新的延迟（以毫秒为单位）。Envoy将在`0-refresh_delay_ms`之间，添加一个额外的随机抖动。因此，最长可能的刷新延迟是2*refresh_delay_ms。默认值是30000ms（30秒）。

### REST API

```
GET /v1/clusters/(string: service_cluster)/(string: service_node)
```

集群发现服务返回`service_cluster`和`service_node`的所有群集定义。`service_cluster`对应于`--service-cluster`CLI选项。`service_node`对应于`--service-node`CLI选项。使用以下JSON格式响应:

```
{
  "clusters": []
}
```

- **[clusters](../../v1APIreference/Clustermanager/Cluster.md)**<br />
	(required, array) 将在集群管理器中动态添加/修改的集群列表。Envoy将协调此列表与当前加载的集群，并根据需要添加/修改/删除集群。请注意，在Envoy配置中静态定义的任何群集，都不能通过CDS API进行修改。


## 返回
- [上一级](../Clustermanager.md)
- [首页目录](../../README.md)

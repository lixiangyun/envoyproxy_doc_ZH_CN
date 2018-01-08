## 集群管理

集群管理[架构概述](../Introduction/Architectureoverview/Clustermanager.md)。

```
{
  "clusters": [],
  "sds": "{...}",
  "local_cluster_name": "...",
  "outlier_detection": "{...}",
  "cds": "{...}"
}
```
- **clusters**<br />
	(required, array) 群集管理器将执行服务发现，健康检查和负载平衡的上游群集列表。

- **sds**<br />
	(sometimes required, object) 如果任何集群定义使用sds集群发现类型，则必须指定全局SDS配置。

- **local_cluster_name**<br />
	(optional, string) 本地集群的名称（即拥有运行此配置的Envoy集群）。若要启用区域感知路由，必须设置此选项。如果定义了`local_cluster_name`，则群集必须包含具有相同名称的群集定义。

- **outlier_detection**<br />
	(optional, object) 用于异常值检测的可选全局配置。

- **cds**<br />
	(optional, object) 用于群集发现服务（CDS）API的可选配置。

### 子章节
- [集群](Clustermanager/Cluster.md)
- [异常检测](Clustermanager/Outlierdetection.md)
- [集群发现服务](Clustermanager/Clusterdiscoveryservice.md)
- [服务发现服务](Clustermanager/Servicediscoveryservice.md)

## 返回
- [上一级](../v1APIreference.md)
- [首页目录](../README.md)


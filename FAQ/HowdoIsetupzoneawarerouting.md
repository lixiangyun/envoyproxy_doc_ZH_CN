## 如何设置区域感知路由？

在源服务（“cluster_a”）和目标服务（“cluster_b”）之间启用[区域感知路](../Introduction/Architectureoverview/Loadbalancing.md)由需要执行几个步骤。

### 在源服务上的Envoy配置
本节介绍与源服务并行运行的Envoy配置。这些要求是：

- Envoy必须使用`--service-zone`选项启动，该选项定义了当前主机的区域。
- 源和目标集群的定义都必须具有[sds](../v1APIreference/Clustermanager/Cluster.md)类型。
- [local_cluster_name](../v1APIreference/Clustermanager.md)必须设置为源群集。

以下配置中仅列出了集群管理器的主要部分。

```
{
  "sds": "{...}",
  "local_cluster_name": "cluster_a",
  "clusters": [
    {
      "name": "cluster_a",
      "type": "sds",
    },
    {
      "name": "cluster_b",
      "type": "sds"
    }
  ]
}
```

### 目标服务上的Envoy配置
没有必要与目标服务并排运行Envoy，但重要的是目标群集中的每个主机都注册源服务Envoy查询的[发现服务](../v1APIreference/Clustermanager/Servicediscoveryservice.md)。区域信息必须作为回应的一部分。

下面只列出了与区域相关的响应数据。

```
{
   "tags": {
       "az": "us-east-1d"
   }
}
```

### 基础架构设置
上述配置对于区域感知路由是必需的，但是当区域感知路由不被执行时有一定的[条件](../Introduction/Architectureoverview/Loadbalancing.md)。

### 验证步骤
- 使用每个区域Envoy的[统计信息](../Configurationreference/Clustermanager/Statistics.md)来监视跨区域流量。

## 返回
- [上一级](../FAQ.md)
- [首页目录](../README.md)
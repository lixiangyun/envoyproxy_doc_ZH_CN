## 熔断
- 熔断[架构概述](../../Introduction/Architectureoverview/Circuitbreaking.md)
- [v1 API 文档](../../v1APIreference/Clustermanager/Cluster/Circuitbreakers.md)
- [v2 API 文档](../../v2APIreference/ClustersandCDS.md)

### 运行时配置
所有的断路设置都可以根据集群名称定义所有运行时配置。他们遵循以下命名规则`circuit_breakers.<cluster_name>.<priority>.<setting>`，其中`cluster_name`表示每个集群的名称，可以在Envoy配置文件中进行设置。也可用的运行时配置覆盖Envoy配置文件中设置的值。

## 返回
- [上一级](../Clustermanager.md)
- [首页目录](../../README.md)

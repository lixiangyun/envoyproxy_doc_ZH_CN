### 动态配置

Envoy架构支持多种的配置管理方法。采用哪种部署方法，取决于需求实现者。可以采用全静态的配置方式，实现简单的部署。更复杂的动态部署，需要采用更复杂的动态配置，需要基于实现者提供一个或多个外部REST的配置API。本文档概述了可用的配置选项。

- 全量参考[配置](../../Configurationreference.md)
- 安装参考[配置](../../Buildingandinstallation/Referenceconfigurations.md)
- [Envoy v2 API概述](../../Configurationreference/Overviewv2api.md)

#### 术语

- SDS（[Service Discovery Service]()）
- EDS（[Endpoint Discovery Service]()）
- CDS（[Cluster Discovery Service]()）
- RDS（[Route Discovery Service]()）
- LDS（[Listener Discovery Service]()）

#### 完全静态

在完全静态配置中，实现者提供了一组监听器、过滤器链、集群以及HTTP路由配置可选。只能通过DNS服务来实现动态主机发现。必须通过内置的热启动机制进行配置的重新加载。

虽然简单，当然可以使用静态配置和优雅的热重启，来实现比较复杂的部署。

#### 仅限SDS/EDS
服务发现服务（SDS）API提供了一种更高级的机制，Envoy可以通过该机制发现上游群集的成员。SDS已在v2API中重命名为Endpoint Discovery Service（EDS）。在静态配置的基础上，SDS允许Envoy部署避开DNS的限制（响应中的最大记录等），并消耗更多信息用于负载平衡和路由（例如，灰度发布，区域等）。

#### SDS/EDS和CDS
群集发现服务（CDS）API层上Envoy可以发现路由期间使用的上游群集的机制。Envoy将优雅地添加，更新和删除由API指定的集群。这个API允许实现者构建一个拓扑，在这个拓扑中，Envoy在初始配置时，不需要知道所有的上游集群。通常，在与CDS一起进行HTTP路由（但没有路由发现服务）时，实现者将利用路由器将请求转发到HTTP请求标头中指定的集群的能力。

虽然可以通过指定完全静态集群来使用没有SDS/EDS的CDS，但我们建议仍然使用SDS/EDS API来通过CDS指定集群。在内部，更新集群定义时，操作是优雅的。但是，所有现有的连接池将被断开并重新连接。SDS/EDS不受此限制。当通过SDS/EDS添加和删除主机时，群集中的现有主机不受影响。

#### SDS/EDS，CDS和RDS
路由发现服务（RDS）API层，Envoy可以在运行时发现HTTP连接管理器过滤器的整个路由配置。路由配置将优雅地交换，而不会影响现有的请求。该API与SDS/EDS和CDS一起使用时，允许执行者构建复杂的路由拓扑（流量转移，蓝/绿部署等），除了获取新的Envoy二进制文件外，不需要任何Envoy重启。

#### SDS/EDS，CDS，RDS和LDS
监听器发现服务（LDS）在Envoy可以在运行时发现整个监听器的机制上分层。这包括所有的过滤器堆栈，并包含嵌入式参考RDS的HTTP过滤器。在混合中添加LDS可以使Envoy的几乎所有方面都能够进行动态配置。只有非常少见的配置更改（管理员，跟踪驱动等）或二进制更新时才需要热启动。


### 返回
- [架构介绍](../Architectureoverview.md)
- [简介](../../Introduction.md)
- [首页目录](../../README.md)

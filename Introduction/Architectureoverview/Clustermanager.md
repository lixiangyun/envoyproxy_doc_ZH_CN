### 集群管理

Envoy的集群管理器管理所有配置的上游集群。就像Envoy配置可以包含任意数量的监听器一样，配置也可以包含任意数量的独立配置的上游集群。

上游集群和主机从网络/HTTP过滤器堆栈中抽象出来，因为上游集群和主机可以用于任意数量的不同代理任务。集群管理器向过滤器堆栈公开API，允许过滤器获得到上游集群的L3/L4连接，或者到上游集群的抽象HTTP连接池的句柄（无论上游主机是支持HTTP/1.1还是HTTP/2被隐藏）。筛选器阶段确定是否需要L3/L4连接或新的HTTP流，并且集群管理器处理了知道哪些主机可用且健康，负载平衡，上游连接数据的线程本地存储的所有复杂性（因为大多数Envoy代码被写为单线程），上游连接类型（TCP/IP，UDS），适用的上游协议（HTTP/1.1，HTTP/2）等

群集管理器已知的群集可以静态配置，也可以通过群集发现服务（CDS）API动态获取。动态集群提取允许将更多配置存储在中央配置服务器中，因此需要更少的Envoy重新启动和配置分配。

- 集群管理器[配置](../../Configurationreference/Clustermanager.md)
- CDS[配置](../../Configurationreference/Clustermanager/Clusterdiscoveryservice.md)

### 返回
- [架构介绍](../Architectureoverview.md)
- [简介](../../Introduction.md)
- [首页目录](../../README.md)

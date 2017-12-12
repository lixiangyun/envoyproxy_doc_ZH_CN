### 初始化

Envoy在启动过程中，初始化是很复杂的。本节将从高层次解释工作流程。以下所有情况，都发生在任何新连接接收之前。

- 在启动过程中，集群管理器会经历多阶段初始化，首先初始化静态/DNS集群，然后是预定义的SDS集群。然后，如果适用，它会初始化CDS，等待一个响应（或失败），并执行CDS提供的集群相同的主/次初始化。
- 如果群集使用主动健康检查，Envoy也做一个活跃的健康检查环节。
- 集群管理器初始化完成后，RDS和LDS将初始化（如果适用）。在LDS/RDS请求至少有一个响应（或失败）之前，服务器不会开始接受连接。
- 如果LDS本身返回需要RDS响应的监听器，则Envoy会进一步等待，直到收到RDS响应（或失败）。请注意，这个过程通过LDS发生在每个未来的收听者身上，并被称为收听者变暖。
- 在所有先前的步骤发生之后，听众开始接受新的连接。该流程确保在热启动期间，新进程在旧进程完全退出时，才可以接收并处理新的连接。


### 返回
- [架构介绍](../Architectureoverview.md)
- [简介](../../Introduction.md)
- [首页目录](../../README.md)
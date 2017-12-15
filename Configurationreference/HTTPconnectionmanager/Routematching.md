## 路由匹配

**注意：本部分是为v1 API编写的，但这些概念也适用于v2 API。它将在未来版本的v2 API中重新描述。**

Envoy的路由匹配过程如下：

1. HTTP请求的头域字段 *host* 或 *:authority* 与[虚拟主机](../../v1APIreference/HTTPRouteconfiguration/Virtualhost.md)匹配。
2. 按顺序检查虚拟主机中的每个[路由表](../../v1APIreference/HTTPRouteconfiguration/Route.md)。如果匹配，则使用该路由并且不再匹配路由。
3. 独立地，依次检查虚拟主机中的每个[虚拟集群](../../v1APIreference/HTTPRouteconfiguration/Virtualcluster.md)。如果匹配，则使用虚拟群集，不再进一步匹配集群。


## 返回
- [上一级](../HTTPconnectionmanager.md)
- [首页目录](../../README.md)

## 统计概述

Envoy输出许多统计信息，这取决于如何配置服务器。它们可以通过本地`GET /stats`命令查看，通常发送到一个[statsd](../Introduction/Architectureoverview/Statistics.md)集群。输出的统计信息记录在[配置指南](../Configurationreference.md)的相关章节中。一些常用的重要的统计数据，可以在下面的章节中找到：

- [HTTP连接管理](../Configurationreference/HTTPconnectionmanager/Statistics.md)
- [上游集群管理](../Configurationreference/Clustermanager/Statistics.md)


## 返回
- [上一级](../Operationsandadministration.md)
- [首页目录](../README.md)

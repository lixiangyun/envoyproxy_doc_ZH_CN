### DynamoDB

Envoy支持具有以下功能的HTTP级别DynamoDB过滤器：

- DynamoDB API请求/响应解析器。
- DynamoDB每个操作/表/分区和操作统计。
- 4xx响应的失败类型统计信息，从响应JSON分析，例如`ProvisionedThroughputExceededException`。
- 批量操作部分的失败统计。

DynamoDB过滤器是Envoy在HTTP层的可扩展性和核心抽象的一个很好的例子。在Lyft中，我们使用此过滤器与DynamoDB进行所有应用程序通信。 它为使用中的应用程序平台和特定的AWS SDK提供了宝贵的数据不可知的来源。

DynamoDB过滤器[配置](../../Configurationreference/HTTPfilters/DynamoDB.md)。

### 返回
- [架构介绍](../Architectureoverview.md)
- [简介](../../Introduction.md)
- [首页目录](../../README.md)

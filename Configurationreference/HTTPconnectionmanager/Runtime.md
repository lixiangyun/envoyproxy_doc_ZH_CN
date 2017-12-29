## 运行时设置

HTTP连接管理器支持以下运行时设置：

- **tracing.client_enabled**<br />
如果设置了[x-client-trace-id](../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md)头部，请求将被强制跟踪的百分比。默认为100。

- **tracing.global_enabled**<br />
在所有其他检查（强制跟踪，采样等）生效之后，将被跟踪的请求百分比。默认为100。

- **tracing.random_sampling**<br />
被随机跟踪的请求万分比。浏览此处获取[更多信息](../../Introduction/Architectureoverview/Tracing.md)。此运行时间值设置范围为0-10000，默认为1000。因此，可以以0.01％的粒度设置跟踪采样。

## 返回
- [上一级](../HTTPconnectionmanager.md)
- [首页目录](../../README.md)

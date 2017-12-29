## 文件系统标识

Envoy支持在启动时改变状态的文件系统“标识”。这用于在必要时重新启动之间保持更改。标识文件应放置在配置选项[flags_path](../Configurationreference/Overviewv1API.md)中指定的目录中。当前支持的标识文件是：

- **drain**<br />
    如果此文件存在，Envoy将以HC失败模式启动，类似于执行`GET /healthcheck/fail`命令之后。

## 返回
- [上一级](../Operationsandadministration.md)
- [首页目录](../README.md)

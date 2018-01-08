## 运行时配置

运行时[配置概述](../Configurationreference/Runtime.md)。

```
{
  "symlink_root": "...",
  "subdirectory": "...",
  "override_subdirectory": "..."
}
```
- **symlink_root**<br />
	(required, string) 当前的实现是假定文件系统目录是通过符号链接方式进行访问。在切换到新文件目录时，应该使用原子链接交换。此参数是指定链接符号的路径。Envoy将观察连接是否更改，并在发生更改时重新加载文件目录树。

- **subdirectory**<br />
	(required, string) 指定在根目录中加载的子目录。如果有多个系统的运行时配置共享相同的链接交换机制，这很有用。Envoy配置元素可以包含在一个专用的子目录中。

- **override_subdirectory**<br />
	(optional, string) 指定在根目录中加载的可选子目录。如果指定并且目录存在，则此目录中的配置值将覆盖在主子目录中找到的值。当Envoy跨多种不同类型的服务器部署时，这是非常有用的。有时为运行时配置提供每个服务集群目录是有用的。请参阅下面，明确如何使用覆盖目录。

## 返回
- [上一级](../v1APIreference.md)
- [首页目录](../README.md)

